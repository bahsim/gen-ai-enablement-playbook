# OrderDetails Step - Implementation Analysis

## Core Architecture

The `OrderDetails` step is a custom POA (Point of Arrival) step that serves as a placeholder in the checkout flow. Unlike other steps, it doesn't render a complex component but rather acts as a step marker in the checkout sequence.

### Step Definition

The OrderDetails step is defined in the checkout step types:

```typescript
enum CheckoutStepType {
    Billing = 'billing',
    Customer = 'customer',
    Payment = 'payment',
    Shipping = 'shipping',
    // Custom POA steps:
    OrderDetails = 'order-details',
    BillingAndPayment = 'billing-and-payment',
}
```

### Step Status Configuration

The step status is configured in the checkout step statuses selector:

```typescript
const orderDetailsStep: CheckoutStepStatus = {
    type: CheckoutStepType.OrderDetails,
    isActive: false,
    isBusy: false,
    isComplete: true,
    isEditable: false,
    isRequired: true,
};
```

**Step Configuration:**
- Always complete (no user interaction required)
- Not editable (read-only step)
- Required in the checkout flow
- Never active (always completed)

### Step Rendering

The step is rendered in the main Checkout component:

```typescript
private renderOrderDetailsStep(step: CheckoutStepStatus): ReactNode {
    return (
        <CheckoutStep
            {...step}
            heading={<TranslatedString id="order_details.order_details_heading" />}
            key={step.type}
            onEdit={this.handleEditStep}
            onExpanded={this.handleExpanded}
        />
    );
}
```

**Rendering Strategy:**
- Uses generic CheckoutStep wrapper
- Displays localized heading
- Passes step configuration through props
- Handles edit and expand events

### Step Visibility Control

The step visibility is controlled by a feature flag:

```typescript
/**
 * showOrderDetailsStep is a flag to determine whether the Order Details step should be shown.
 * This can be based on the checkout flow (e.g., part-time vs full-time checkout flow).
 */
const showOrderDetailsStep = true;

// In step mapping
case CheckoutStepType.OrderDetails:
    return { ...step, isRequired: showOrderDetailsStep };
```

**Visibility Strategy:**
- Controlled by feature flag for A/B testing
- Can be toggled based on checkout flow type
- Allows for different checkout experiences

### Step Integration

The step is integrated into the checkout flow through the step statuses selector:

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

        // ... step processing logic
    }
);
```

**Integration Strategy:**
- Added to the steps array after other steps
- Processed through the same step status logic
- Maintains consistent step interface

### Step Purpose

The OrderDetails step serves several purposes:

1. **Flow Marker**: Provides a visual marker in the checkout flow
2. **A/B Testing**: Allows for different checkout flow configurations
3. **Future Expansion**: Placeholder for future order details functionality
4. **User Experience**: Gives users a sense of progress through checkout

### Step Behavior

The step behaves as follows:

- **Always Complete**: No user interaction required
- **Read-Only**: Cannot be edited or modified
- **Required**: Must be present in the checkout flow
- **Non-Active**: Never becomes the active step

### Localization

The step uses localized strings for display:

```typescript
heading={<TranslatedString id="order_details.order_details_heading" />}
```

**Localization Strategy:**
- Uses translation key for heading
- Supports multiple languages
- Consistent with other checkout steps

### Step Configuration

The step can be configured through the checkout props mapping:

```typescript
// In mapToCheckoutProps.ts
const showOrderDetailsStep = true;

// Step is made required based on configuration
case CheckoutStepType.OrderDetails:
    return { ...step, isRequired: showOrderDetailsStep };
```

**Configuration Strategy:**
- Feature flag controlled
- Can be toggled per checkout instance
- Allows for gradual rollout

### Future Considerations

The OrderDetails step is designed for future expansion:

1. **Order Summary**: Could display detailed order information
2. **Item Details**: Could show individual line items
3. **Pricing Breakdown**: Could display taxes, shipping, discounts
4. **Custom Fields**: Could include store-specific order details

### Performance Impact

The step has minimal performance impact:

- **No Rendering**: Only renders a simple step wrapper
- **No State Management**: No complex state or lifecycle methods
- **No API Calls**: No external data fetching
- **Minimal DOM**: Simple step structure

### Integration Points

The step integrates with:

- **Checkout Flow**: Part of the main checkout sequence
- **Step Management**: Uses standard step status system
- **Localization**: Supports multiple languages
- **Feature Flags**: Controlled by configuration

## Source Files

- **Step Type**: `packages/core/src/app/checkout/CheckoutStepType.ts`
- **Step Status**: `packages/core/src/app/checkout/getCheckoutStepStatuses.ts`
- **Step Rendering**: `packages/core/src/app/checkout/Checkout.tsx`
- **Props Mapping**: `packages/core/src/app/checkout/mapToCheckoutProps.ts`
- **Step Wrapper**: `packages/core/src/app/checkout/CheckoutStep.tsx`
