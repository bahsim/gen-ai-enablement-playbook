# Props Mapping Logic - Implementation Analysis

## Core Architecture

The `mapToCheckoutProps.ts` file transforms checkout state into props for the Checkout component. It handles step combination logic, feature flags, error states, and provides a clean interface between the checkout state and the UI components.

### Props Interface

```typescript
interface WithCheckoutProps {
    billingAddress: Address | undefined;
    cart: Cart | undefined;
    clearError: (error: Error) => void;
    consignments: Consignment[] | undefined;
    hasCartChanged: boolean;
    isGuestEnabled: boolean;
    isLoadingCheckout: boolean;
    isShippingDiscountDisplayEnabled: boolean;
    isPending: boolean;
    isPriceHiddenFromGuests: boolean;
    isShowingWalletButtonsOnTop: boolean;
    loadCheckout: (checkoutId: string, options?: RequestOptions) => Promise<CheckoutSelectors>;
    loadPaymentMethodByIds: (methodIds: string[]) => Promise<CheckoutSelectors>;
    loginUrl: string;
    cartUrl: string;
    createAccountUrl: string;
    promotions: Promotion[];
    subscribeToConsignments: (subscriber: (state: CheckoutSelectors) => void) => () => void;
    steps: CheckoutStepStatus[];
}
```

## Main Mapping Function

### Function Signature

```typescript
export default function mapToCheckoutProps({
    checkoutService,
    checkoutState,
}: CheckoutContextProps): WithCheckoutProps {
    const { data, errors, statuses } = checkoutState;
    const { promotions = EMPTY_ARRAY } = data.getCheckout() || {};
    const submitOrderError = errors.getSubmitOrderError() as CustomError;
    const {
        checkoutSettings: {
            guestCheckoutEnabled: isGuestEnabled = false,
            checkoutUserExperienceSettings = {
                walletButtonsOnTop: false,
                floatingLabelEnabled: false,
            },
        } = {},
        links: {
            loginLink: loginUrl = '',
            createAccountLink: createAccountUrl = '',
            cartLink: cartUrl = '',
        } = {},
        displaySettings: { hidePriceFromGuests: isPriceHiddenFromGuests = false } = {},
    } = data.getConfig() || {};
```

**Mapping Strategy:**
- **State Extraction**: Extracts data, errors, and statuses from checkout state
- **Promotion Handling**: Provides empty array fallback for promotions
- **Error Extraction**: Gets submit order error for cart change detection
- **Configuration Extraction**: Extracts various configuration settings with defaults

### Feature Flag Processing

```typescript
const walletButtonsOnTopFlag = Boolean(checkoutUserExperienceSettings.walletButtonsOnTop); 
const isShippingDiscountDisplayEnabled = isExperimentEnabled(
    data.getConfig()?.checkoutSettings,
    'PROJECT-6643.enable_shipping_discounts_in_orders',
);
```

**Feature Flag Strategy:**
- **Wallet Buttons**: Boolean conversion for wallet button positioning
- **Shipping Discounts**: Experiment flag for shipping discount display
- **Configuration Access**: Safe access to nested configuration

### Step Status Processing

```typescript
// Overwrite the steps to ensure that Billing and Payment steps are not required if combined BillingAndPayment step is present.
const checkoutSteps = data.getCheckout() ? getCheckoutStepStatuses(checkoutState) : EMPTY_ARRAY;
const billingStep = checkoutSteps.find(step => step.type === CheckoutStepType.Billing);
const paymentStep = checkoutSteps.find(step => step.type === CheckoutStepType.Payment);
const billingAndPaymentStep = checkoutSteps.find(step => step.type === CheckoutStepType.BillingAndPayment);

let steps = checkoutSteps;

// In case of POA checkout, we need to combine Billing and Payment steps into a single step.
if (billingStep && paymentStep && billingAndPaymentStep) {
    steps = checkoutSteps.map(step => {
        switch (step.type) {
            case CheckoutStepType.Billing:
            case CheckoutStepType.Payment:
                return { ...step, isActive: false, isRequired: false };

            case CheckoutStepType.OrderDetails:
                return { ...step, isRequired: showOrderDetailsStep };

            default:
                return step;
        }
    });

    billingAndPaymentStep.isActive = billingStep.isActive || paymentStep.isActive;
    billingAndPaymentStep.isComplete = billingStep.isComplete && paymentStep.isComplete;
    billingAndPaymentStep.isEditable = billingStep.isEditable && paymentStep.isEditable;
    billingAndPaymentStep.isRequired = true;
}
```

**Step Processing Strategy:**
- **Step Retrieval**: Gets step statuses from checkout state
- **Step Identification**: Finds specific step types
- **POA Logic**: Combines billing and payment steps for POA checkout
- **Step Override**: Disables individual billing/payment steps
- **Combined Step**: Creates combined billing and payment step
- **Order Details**: Controls order details step visibility

### Consignment Subscription

```typescript
const subscribeToConsignmentsSelector = createSelector(
    ({ checkoutService: { subscribe } }: CheckoutContextProps) => subscribe,
    (subscribe) => (subscriber: (state: CheckoutSelectors) => void) => {
        return subscribe(subscriber, ({ data: { getConsignments } }) => getConsignments());
    },
);
```

**Subscription Strategy:**
- **Reselect Selector**: Uses Reselect for memoization
- **Service Extraction**: Extracts subscribe function from checkout service
- **Consignment Focus**: Subscribes specifically to consignment changes
- **Return Function**: Returns unsubscribe function

### Return Object Construction

```typescript
return {
    billingAddress: data.getBillingAddress(),
    cart: data.getCart(),
    clearError: checkoutService.clearError,
    consignments: data.getConsignments(),
    hasCartChanged: submitOrderError && submitOrderError.type === 'cart_changed',
    isGuestEnabled,
    isLoadingCheckout: statuses.isLoadingCheckout(),
    isShippingDiscountDisplayEnabled,
    isPending: statuses.isPending(),
    isPriceHiddenFromGuests,
    isShowingWalletButtonsOnTop: walletButtonsOnTopFlag,
    loadCheckout: checkoutService.loadCheckout,
    loadPaymentMethodByIds: checkoutService.loadPaymentMethodByIds,
    loginUrl,
    cartUrl,
    createAccountUrl,
    promotions,
    subscribeToConsignments: subscribeToConsignmentsSelector({
        checkoutService,
        checkoutState,
    }),
    steps,
};
```

**Return Strategy:**
- **Data Mapping**: Maps checkout data to props
- **Service Methods**: Provides checkout service methods
- **Error States**: Maps error states to boolean flags
- **Configuration**: Maps configuration to props
- **URLs**: Maps configuration URLs to props
- **Subscriptions**: Provides subscription functions

## Step Combination Logic

### POA Checkout Step Handling

The mapping function implements special logic for POA (Point of Access) checkout:

```typescript
// In case of POA checkout, we need to combine Billing and Payment steps into a single step.
if (billingStep && paymentStep && billingAndPaymentStep) {
    steps = checkoutSteps.map(step => {
        switch (step.type) {
            case CheckoutStepType.Billing:
            case CheckoutStepType.Payment:
                return { ...step, isActive: false, isRequired: false };

            case CheckoutStepType.OrderDetails:
                return { ...step, isRequired: showOrderDetailsStep };

            default:
                return step;
        }
    });

    billingAndPaymentStep.isActive = billingStep.isActive || paymentStep.isActive;
    billingAndPaymentStep.isComplete = billingStep.isComplete && paymentStep.isComplete;
    billingAndPaymentStep.isEditable = billingStep.isEditable && paymentStep.isEditable;
    billingAndPaymentStep.isRequired = true;
}
```

**POA Strategy:**
- **Step Disabling**: Disables individual billing and payment steps
- **Combined Step**: Creates combined billing and payment step
- **State Combination**: Combines state from both steps
- **Order Details**: Controls order details step visibility

### Step State Combination

```typescript
billingAndPaymentStep.isActive = billingStep.isActive || paymentStep.isActive;
billingAndPaymentStep.isComplete = billingStep.isComplete && paymentStep.isComplete;
billingAndPaymentStep.isEditable = billingStep.isEditable && paymentStep.isEditable;
billingAndPaymentStep.isRequired = true;
```

**State Combination Strategy:**
- **Active State**: Active if either step is active
- **Complete State**: Complete only if both steps are complete
- **Editable State**: Editable only if both steps are editable
- **Required State**: Always required

## Error Handling

### Cart Change Detection

```typescript
hasCartChanged: submitOrderError && submitOrderError.type === 'cart_changed',
```

**Error Detection Strategy:**
- **Error Type Check**: Checks for specific error type
- **Cart Change Flag**: Maps to boolean flag
- **Error Context**: Uses error context for detection

### Error Clearing

```typescript
clearError: checkoutService.clearError,
```

**Error Clearing Strategy:**
- **Service Method**: Uses checkout service method
- **Direct Mapping**: Maps service method directly
- **Error Management**: Provides error clearing capability

## Configuration Mapping

### Checkout Settings

```typescript
const {
    checkoutSettings: {
        guestCheckoutEnabled: isGuestEnabled = false,
        checkoutUserExperienceSettings = {
            walletButtonsOnTop: false,
            floatingLabelEnabled: false,
        },
    } = {},
    links: {
        loginLink: loginUrl = '',
        createAccountLink: createAccountUrl = '',
        cartLink: cartUrl = '',
    } = {},
    displaySettings: { hidePriceFromGuests: isPriceHiddenFromGuests = false } = {},
} = data.getConfig() || {};
```

**Configuration Strategy:**
- **Nested Destructuring**: Extracts nested configuration
- **Default Values**: Provides sensible defaults
- **Safe Access**: Uses optional chaining and defaults
- **Type Safety**: Maintains type safety

### Feature Flags

```typescript
const walletButtonsOnTopFlag = Boolean(checkoutUserExperienceSettings.walletButtonsOnTop); 
const isShippingDiscountDisplayEnabled = isExperimentEnabled(
    data.getConfig()?.checkoutSettings,
    'PROJECT-6643.enable_shipping_discounts_in_orders',
);
```

**Feature Flag Strategy:**
- **Boolean Conversion**: Converts to boolean values
- **Experiment Flags**: Uses experiment flag system
- **Configuration Access**: Safe access to configuration
- **Feature Control**: Controls feature visibility

## Performance Optimizations

### Reselect Integration

```typescript
const subscribeToConsignmentsSelector = createSelector(
    ({ checkoutService: { subscribe } }: CheckoutContextProps) => subscribe,
    (subscribe) => (subscriber: (state: CheckoutSelectors) => void) => {
        return subscribe(subscriber, ({ data: { getConsignments } }) => getConsignments());
    },
);
```

**Performance Strategy:**
- **Memoization**: Reselect provides automatic memoization
- **Input Selectors**: Only recomputes when inputs change
- **Result Caching**: Caches results until inputs change
- **Efficient Updates**: Prevents unnecessary recalculations

### Safe Access Patterns

```typescript
const { promotions = EMPTY_ARRAY } = data.getCheckout() || {};
const submitOrderError = errors.getSubmitOrderError() as CustomError;
```

**Safe Access Strategy:**
- **Default Values**: Provides fallback values
- **Null Checks**: Handles null/undefined values
- **Type Assertions**: Safe type assertions
- **Error Handling**: Graceful error handling

## Integration Points

The props mapping integrates with:
- **Checkout State**: Main checkout state management
- **Checkout Service**: Service methods and operations
- **Step Status Logic**: Step status calculation
- **Configuration**: Store configuration
- **Feature Flags**: Experiment configuration
- **Error Handling**: Error state management

## Type Safety

### Interface Definitions

```typescript
interface WithCheckoutProps {
    billingAddress: Address | undefined;
    cart: Cart | undefined;
    clearError: (error: Error) => void;
    // ... more props
}
```

**Type Safety Strategy:**
- **Interface Definition**: Clear interface definition
- **Type Annotations**: Proper type annotations
- **Optional Types**: Handles optional values
- **Function Types**: Proper function type definitions

### Safe Type Assertions

```typescript
const submitOrderError = errors.getSubmitOrderError() as CustomError;
```

**Type Assertion Strategy:**
- **Safe Assertions**: Only when type is known
- **Error Context**: Uses error context for type safety
- **Fallback Handling**: Handles type assertion failures

## Source Files

- **Main Implementation**: `packages/core/src/app/checkout/mapToCheckoutProps.ts`
- **Test File**: `packages/core/src/app/checkout/mapToCheckoutProps.test.ts`
- **Dependencies**: Checkout state, Step status logic, Reselect
