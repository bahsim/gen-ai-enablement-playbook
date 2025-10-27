# Step Status Logic - Implementation Analysis

## Core Architecture

The `getCheckoutStepStatuses.ts` file contains the complex business logic for determining checkout step statuses, including completion, editability, and requirements. It uses Reselect selectors for performance optimization and handles various payment methods and checkout scenarios.

### Step Status Interface

```typescript
interface CheckoutStepStatus {
    type: CheckoutStepType;
    isActive: boolean;
    isComplete: boolean;
    isEditable: boolean;
    isRequired: boolean;
    isBusy?: boolean;
}
```

### Step Types

```typescript
enum CheckoutStepType {
    Customer = 'customer',
    Shipping = 'shipping',
    Billing = 'billing',
    Payment = 'payment',
    BillingAndPayment = 'billingAndPayment',
    OrderDetails = 'orderDetails'
}
```

## Customer Step Status Logic

### Customer Step Selector

```typescript
const getCustomerStepStatus = createSelector(
    ({ data }: CheckoutSelectors) => data.getCheckout(),
    ({ data }: CheckoutSelectors) => data.getCustomer(),
    ({ data }: CheckoutSelectors) => data.getBillingAddress(),
    ({ data }: CheckoutSelectors) => data.getConfig(),
    ({ data }: CheckoutSelectors) => data.getCart(),
    ({ data }: CheckoutSelectors) => data.getPaymentProviderCustomer(),
    (checkout, customer, billingAddress, config, cart, paymentProviderCustomer) => {
        const hasEmail = !!(
            (customer && customer.email) ||
            (billingAddress && billingAddress.email)
        );
        const isUsingWallet =
            checkout && checkout.payments
                ? checkout.payments.some(
                    (payment: CheckoutPayment) => SUPPORTED_METHODS.indexOf(payment.providerId) >= 0,
                  )
                : false;
        const isGuest = !!(customer && customer.isGuest);
        const isComplete = hasEmail || isUsingWallet;
        const isEditable = isComplete && !isUsingWallet && isGuest;
        const isUsingStripeLinkAndCheckoutPageIsReloaded = getStripeLinkAndCheckoutPageIsReloaded(
            isUsingWallet,
            hasEmail,
            isGuest,
            cart ? shouldUseStripeLinkByMinimumAmount(cart) : false,
            config?.checkoutSettings.providerWithCustomCheckout,
        );

        if (isUsingStripeLinkAndCheckoutPageIsReloaded) {
            return {
                type: CheckoutStepType.Customer,
                isActive: false,
                isComplete: paymentProviderCustomer?.stripeLinkAuthenticationState !== undefined,
                isEditable,
                isRequired: true,
            };
        }

        return {
            type: CheckoutStepType.Customer,
            isActive: false,
            isComplete,
            isEditable,
            isRequired: true,
        };
    },
);
```

**Customer Step Strategy:**
- **Email Validation**: Checks for email in customer or billing address
- **Wallet Integration**: Detects wallet payment methods
- **Guest Checkout**: Handles guest vs logged-in customers
- **Stripe Link**: Special handling for Stripe Link authentication
- **Editability**: Only editable for guest customers not using wallets

### Stripe Link Special Handling

```typescript
const getStripeLinkAndCheckoutPageIsReloaded = (
    isUsingWallet: boolean,
    hasEmail: boolean,
    isGuest: boolean,
    shouldUseStripeLinkByMinimumAmount: boolean,
    providerWithCustomCheckout?: string | null,
) => {
    return !isUsingWallet && 
           providerWithCustomCheckout === PaymentMethodId.StripeUPE && 
           hasEmail && 
           isGuest && 
           shouldUseStripeLinkByMinimumAmount;
}
```

**Stripe Link Strategy:**
- **Wallet Check**: Not using wallet payment
- **Provider Check**: Using Stripe UPE provider
- **Email Check**: Has email address
- **Guest Check**: Is guest customer
- **Amount Check**: Meets minimum amount requirement

## Billing Step Status Logic

### Billing Step Selector

```typescript
const getBillingStepStatus = createSelector(
    ({ data }: CheckoutSelectors) => data.getCheckout(),
    ({ data }: CheckoutSelectors) => data.getBillingAddress(),
    ({ data }: CheckoutSelectors) => {
        const billingAddress = data.getBillingAddress();
        return billingAddress
            ? data.getBillingAddressFields(billingAddress.countryCode)
            : EMPTY_ARRAY;
    },
    ({ data }: CheckoutSelectors) => data.getConfig(),
    (checkout, billingAddress, billingAddressFields, config) => {
        const hasAddress = billingAddress
            ? isValidAddress(billingAddress, billingAddressFields)
            : false;
        const isUsingWallet =
            checkout && checkout.payments
                ? checkout.payments.some(
                      (payment) => SUPPORTED_METHODS.indexOf(payment.providerId) >= 0,
                  )
                : false;
        const isComplete = hasAddress || isUsingWallet;
        const isUsingAmazonPay =
            checkout && checkout.payments
                ? checkout.payments.some((payment) => payment.providerId === 'amazonpay')
                : false;

        // Amazon Pay special handling
        if (isUsingAmazonPay) {
            const billingAddressCustomFields = billingAddressFields.filter(
                ({ custom }: { custom: boolean }) => custom,
            );
            const hasCustomFields = billingAddressCustomFields.length > 0;
            const isAmazonPayBillingStepComplete =
                billingAddress && hasCustomFields
                    ? isValidAddress(billingAddress, billingAddressCustomFields)
                    : true;

            return {
                type: CheckoutStepType.Billing,
                isActive: false,
                isComplete: isAmazonPayBillingStepComplete,
                isEditable: isAmazonPayBillingStepComplete && hasCustomFields,
                isRequired: true,
            };
        }

        // Google Pay special handling
        const isGooglePayBillingAddressEditingEnabled = isExperimentEnabled(
            config?.checkoutSettings,
            'STRIPE-546.allow_billing_address_editing_for_all_Google_Pay_providers',
        );
        const isUsingGooglePay =
            isGooglePayBillingAddressEditingEnabled && (checkout && checkout.payments
                ? checkout.payments.some((payment) => (payment?.providerId || '').startsWith('googlepay'))
                : false);

        if (isUsingGooglePay) {
            return {
                type: CheckoutStepType.Billing,
                isActive: false,
                isComplete: hasAddress,
                isEditable: hasAddress,
                isRequired: true,
            };
        }

        // PayPal special handling
        const isUsingPaypal =
            checkout && checkout.payments
                ? checkout.payments.some(
                    (payment) =>
                        [
                            'braintreepaypal',
                            'braintreepaypalcredit',
                            'braintreevenmo',
                            'paypalcommerce',
                            'paypalcommercecredit',
                            'paypalcommercevenmo'
                        ]
                            .includes(payment.providerId))
                : false;

        if (isUsingPaypal) {
            return {
                type: CheckoutStepType.Billing,
                isActive: false,
                isComplete: hasAddress,
                isEditable: hasAddress,
                isRequired: true,
            };
        }

        return {
            type: CheckoutStepType.Billing,
            isActive: false,
            isComplete,
            isEditable: isComplete && !isUsingWallet,
            isRequired: true,
        };
    },
);
```

**Billing Step Strategy:**
- **Address Validation**: Validates billing address against required fields
- **Wallet Integration**: Handles wallet payment methods
- **Payment Method Special Cases**: Different logic for Amazon Pay, Google Pay, PayPal
- **Custom Fields**: Handles custom address fields for specific payment methods
- **Feature Flags**: Uses experiment flags for payment method features

### Payment Method Special Handling

#### Amazon Pay
```typescript
if (isUsingAmazonPay) {
    const billingAddressCustomFields = billingAddressFields.filter(
        ({ custom }: { custom: boolean }) => custom,
    );
    const hasCustomFields = billingAddressCustomFields.length > 0;
    const isAmazonPayBillingStepComplete =
        billingAddress && hasCustomFields
            ? isValidAddress(billingAddress, billingAddressCustomFields)
            : true;

    return {
        type: CheckoutStepType.Billing,
        isActive: false,
        isComplete: isAmazonPayBillingStepComplete,
        isEditable: isAmazonPayBillingStepComplete && hasCustomFields,
        isRequired: true,
    };
}
```

**Amazon Pay Strategy:**
- **Custom Fields Only**: Only validates custom fields
- **Conditional Validation**: Only validates if custom fields exist
- **Editability**: Only editable if custom fields are present

#### Google Pay
```typescript
const isGooglePayBillingAddressEditingEnabled = isExperimentEnabled(
    config?.checkoutSettings,
    'STRIPE-546.allow_billing_address_editing_for_all_Google_Pay_providers',
);
const isUsingGooglePay =
    isGooglePayBillingAddressEditingEnabled && (checkout && checkout.payments
        ? checkout.payments.some((payment) => (payment?.providerId || '').startsWith('googlepay'))
        : false);

if (isUsingGooglePay) {
    return {
        type: CheckoutStepType.Billing,
        isActive: false,
        isComplete: hasAddress,
        isEditable: hasAddress,
        isRequired: true,
    };
}
```

**Google Pay Strategy:**
- **Feature Flag**: Controlled by experiment flag
- **Provider Detection**: Detects Google Pay providers
- **Standard Validation**: Uses standard address validation
- **Editability**: Editable when address is complete

#### PayPal
```typescript
const isUsingPaypal =
    checkout && checkout.payments
        ? checkout.payments.some(
            (payment) =>
                [
                    'braintreepaypal',
                    'braintreepaypalcredit',
                    'braintreevenmo',
                    'paypalcommerce',
                    'paypalcommercecredit',
                    'paypalcommercevenmo'
                ]
                    .includes(payment.providerId))
        : false;

if (isUsingPaypal) {
    return {
        type: CheckoutStepType.Billing,
        isActive: false,
        isComplete: hasAddress,
        isEditable: hasAddress,
        isRequired: true,
    };
}
```

**PayPal Strategy:**
- **Provider Detection**: Detects various PayPal providers
- **Standard Validation**: Uses standard address validation
- **Editability**: Editable when address is complete

## Shipping Step Status Logic

### Shipping Step Selector

```typescript
const getShippingStepStatus = createSelector(
    ({ data }: CheckoutSelectors) => data.getShippingAddress(),
    ({ data }: CheckoutSelectors) => data.getConsignments(),
    ({ data }: CheckoutSelectors) => data.getCart(),
    ({ data }: CheckoutSelectors) => {
        const shippingAddress = data.getShippingAddress();
        return shippingAddress
            ? data.getShippingAddressFields(shippingAddress.countryCode)
            : EMPTY_ARRAY;
    },
    ({ data }: CheckoutSelectors) => data.getConfig(),
    (shippingAddress, consignments, cart, shippingAddressFields, config) => {
        const hasAddress = shippingAddress
            ? isValidAddress(shippingAddress, shippingAddressFields)
            : false;
        const hasOptions = consignments ? hasSelectedShippingOptions(consignments) : false;
        const hasUnassignedItems =
            cart && consignments ? hasUnassignedLineItems(consignments, cart.lineItems) : true;
        const isComplete = hasAddress && hasOptions && !hasUnassignedItems;
        const isRequired = itemsRequireShipping(cart, config);
        const isCustomShippingSelected =
            isExperimentEnabled(
                config?.checkoutSettings,
                'PROJECT-5015.manual_order.display_custom_shipping',
            ) &&
            hasOptions &&
            consignments?.some(
                ({ selectedShippingOption }) => selectedShippingOption?.type === 'custom',
            );

        return {
            type: CheckoutStepType.Shipping,
            isActive: false,
            isComplete,
            isEditable: isComplete && isRequired && !isCustomShippingSelected,
            isRequired,
        };
    },
);
```

**Shipping Step Strategy:**
- **Address Validation**: Validates shipping address
- **Shipping Options**: Checks for selected shipping options
- **Unassigned Items**: Ensures all items are assigned to consignments
- **Custom Shipping**: Special handling for custom shipping options
- **Requirements**: Determines if shipping is required based on cart items

## Payment Step Status Logic

### Payment Step Selector

```typescript
const getPaymentStepStatus = createSelector(
    ({ data }: CheckoutSelectors) => data.getOrder(),
    (order) => {
        const isComplete = order ? order.isComplete : false;

        return {
            type: CheckoutStepType.Payment,
            isActive: false,
            isComplete,
            isEditable: isComplete,
            isRequired: true,
        };
    },
);
```

**Payment Step Strategy:**
- **Order Completion**: Based on order completion status
- **Editability**: Editable when order is complete
- **Requirements**: Always required

## Order Submit Status

### Order Submit Selector

```typescript
const getOrderSubmitStatus = createSelector(
    ({ statuses }: CheckoutSelectors) => statuses.isSubmittingOrder(),
    (status) => status,
);
```

**Order Submit Strategy:**
- **Submission Status**: Tracks order submission status
- **Busy State**: Used for busy indicators

## Main Step Status Selector

### Combined Step Status Logic

```typescript
const getCheckoutStepStatuses = createSelector(
    getCustomerStepStatus,
    getShippingStepStatus,
    getBillingStepStatus,
    getPaymentStepStatus,
    getOrderSubmitStatus,
    (customerStep, shippingStep, billingStep, paymentStep, orderStatus) => {
        const isSubmittingOrder = orderStatus;

        const steps = compact([customerStep, shippingStep, billingStep, paymentStep]);

        const orderDetailsStep: CheckoutStepStatus = {
            type: CheckoutStepType.OrderDetails,
            isActive: false,
            isBusy: false,
            isComplete: true,
            isEditable: false,
            isRequired: true,
        };

        /**
         * Combines the billing and payment steps into a single step status.
         */
        const billingAndPaymentStep: CheckoutStepStatus = {
            type: CheckoutStepType.BillingAndPayment,
            isActive: false,
            isBusy: false,
            isComplete: billingStep.isComplete && paymentStep.isComplete,
            isEditable: billingStep.isEditable && paymentStep.isEditable,
            isRequired: billingStep.isRequired || paymentStep.isRequired,
        };

        const defaultActiveStep =
            steps.find((step) => !step.isComplete && step.isRequired) || billingAndPaymentStep;

        return [...steps, orderDetailsStep, billingAndPaymentStep].map((step, index) => {
            const isPrevStepComplete = steps
                .slice(0, index)
                .every((prevStep) => prevStep.isComplete || !prevStep.isRequired);

            return {
                ...step,
                isActive: defaultActiveStep.type === step.type,
                isBusy: false,
                isEditable: isPrevStepComplete && step.isEditable && !isSubmittingOrder,
            };
        });
    },
);
```

**Combined Strategy:**
- **Step Combination**: Combines billing and payment into single step
- **Order Details**: Adds order details step
- **Active Step**: Determines which step should be active
- **Dependencies**: Ensures previous steps are complete before next steps are editable
- **Submission State**: Disables editing during order submission

## Performance Optimizations

### Reselect Selectors

```typescript
const getCustomerStepStatus = createSelector(
    // Input selectors
    ({ data }: CheckoutSelectors) => data.getCheckout(),
    ({ data }: CheckoutSelectors) => data.getCustomer(),
    // ... more selectors
    // Result function
    (checkout, customer, billingAddress, config, cart, paymentProviderCustomer) => {
        // Complex logic
    },
);
```

**Performance Strategy:**
- **Memoization**: Reselect provides automatic memoization
- **Input Selectors**: Only recomputes when inputs change
- **Result Caching**: Caches results until inputs change
- **Efficient Updates**: Prevents unnecessary recalculations

### Compact Array Handling

```typescript
const steps = compact([customerStep, shippingStep, billingStep, paymentStep]);
```

**Array Strategy:**
- **Null Filtering**: Removes null/undefined values
- **Efficient Processing**: Only processes valid steps
- **Type Safety**: Maintains type safety

## Business Logic Features

### Payment Method Detection

```typescript
const isUsingWallet =
    checkout && checkout.payments
        ? checkout.payments.some(
            (payment: CheckoutPayment) => SUPPORTED_METHODS.indexOf(payment.providerId) >= 0,
          )
        : false;
```

**Payment Method Strategy:**
- **Wallet Detection**: Identifies wallet payment methods
- **Provider Array**: Uses supported methods array
- **Some Method**: Efficient array searching

### Address Validation

```typescript
const hasAddress = billingAddress
    ? isValidAddress(billingAddress, billingAddressFields)
    : false;
```

**Address Strategy:**
- **Validation Function**: Uses dedicated validation function
- **Field Requirements**: Validates against required fields
- **Country-Specific**: Uses country-specific field requirements

### Feature Flag Integration

```typescript
const isShippingDiscountDisplayEnabled = isExperimentEnabled(
    config?.checkoutSettings,
    'PROJECT-6643.enable_shipping_discounts_in_orders',
);
```

**Feature Flag Strategy:**
- **Experiment Flags**: Uses experiment configuration
- **Conditional Logic**: Applies logic based on flags
- **A/B Testing**: Supports A/B testing scenarios

## Integration Points

The step status logic integrates with:
- **Checkout State**: Main checkout state management
- **Address Validation**: Address validation utilities
- **Payment Methods**: Payment method detection
- **Shipping Logic**: Shipping option validation
- **Feature Flags**: Experiment configuration
- **Reselect**: Performance optimization

## Source Files

- **Main Implementation**: `packages/core/src/app/checkout/getCheckoutStepStatuses.ts`
- **Test File**: `packages/core/src/app/checkout/getCheckoutStepStatuses.test.ts`
- **Dependencies**: Reselect, Address validation, Payment methods, Shipping logic
