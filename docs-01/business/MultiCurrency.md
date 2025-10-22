# Multi-Currency and Promotional Item Logic - Implementation Analysis

## Core Architecture

The BigCommerce POA React Checkout implements sophisticated multi-currency handling and promotional item logic through the BigCommerce SDK. The system manages currency conversion, promotional item conflicts, and multi-shipping restrictions.

## Multi-Currency System

### Currency Configuration

```typescript
// Currency configuration from store config
const config = getConfig();
const shopperCurrency = config.shopperCurrency;
const storeCurrency = config.currency;

// Currency object structure
interface Currency {
    name: string;           // "US Dollar"
    code: string;          // "USD"
    symbol: string;        // "$"
    decimalPlaces: number; // 2
}
```

**Currency Strategy:**
- Store currency: Base currency for the store
- Shopper currency: Customer's preferred currency
- Automatic conversion through SDK
- Consistent formatting across components

### Currency Display Logic

```typescript
// In OrderSummary component
<OrderSummaryTotal
    orderAmount={total}
    shopperCurrencyCode={shopperCurrency.code}
    storeCurrencyCode={storeCurrency.code}
/>
```

**Display Strategy:**
- Shows amounts in shopper currency
- Displays conversion rates when applicable
- Maintains currency consistency
- Handles decimal places correctly

### Currency Conversion

```typescript
// Currency conversion handled by SDK
const { data } = await loadCheckout(checkoutId, {
    params: {
        include: [
            'cart.lineItems.physicalItems.categoryNames',
            'cart.lineItems.digitalItems.categoryNames',
        ],
    },
});
```

**Conversion Strategy:**
- Server-side currency conversion
- Real-time exchange rates
- Consistent pricing across checkout
- Automatic fallback to store currency

## Promotional Item Logic

### Promotional Item Detection

```typescript
// In Shipping component
const cartHasPromotionalItems = hasPromotionalItems(cart);

if (cartHasPromotionalItems && isMultiShippingMode) {
    this.setState({ isMultiShippingUnavailableModalOpen: true });
}
```

**Detection Strategy:**
- Identifies promotional items in cart
- Checks for multi-shipping conflicts
- Shows modal when conflict detected
- Prevents multi-shipping with promotional items

### Promotional Item Conflict Resolution

```typescript
const handleSwitchToSingleShipping = async () => {
    this.setState({ isMultiShippingUnavailableModalOpen: false });
    await this.handleMultiShippingModeSwitch();
};

<ConfirmationModal
    action={handleSwitchToSingleShipping}
    actionButtonLabel={<TranslatedString id="common.ok_action" />}
    headerId="shipping.multishipping_unavailable_action"
    isModalOpen={isMultiShippingUnavailableModalOpen}
    messageId="shipping.checkout_switched_to_single_shipping"
    shouldShowCloseButton={false}
/>
```

**Conflict Resolution Strategy:**
- Forces switch to single shipping
- Shows confirmation modal
- Prevents user from proceeding with conflict
- Maintains checkout flow integrity

### Promotional Item Display

```typescript
// In cart summary
const isShippingDiscountDisplayEnabled = isExperimentEnabled(
    config.checkoutSettings,
    'PROJECT-6643.enable_shipping_discounts_in_orders',
);

return {
    isShippingDiscountDisplayEnabled,
    checkout,
    shopperCurrency: config.shopperCurrency,
    cartUrl: config.links.cartLink,
    storeCurrency: config.currency,
    storeCreditAmount: isStoreCreditApplied ? Math.min(grandTotal, storeCredit) : undefined,
    ...redeemableProps,
};
```

**Display Strategy:**
- Feature flag controlled display
- Shows shipping discounts when enabled
- Integrates with store credit
- Maintains promotional item visibility

## Multi-Shipping Restrictions

### Promotional Item Multi-Shipping Conflict

```typescript
// Multi-shipping mode detection
const isMultiShippingMode =
    !!cart &&
    !!consignments &&
    hasMultiShippingEnabled &&
    isUsingMultiShipping(consignments, cart.lineItems);

// Conflict detection
if (cartHasPromotionalItems && isMultiShippingMode) {
    this.setState({ isMultiShippingUnavailableModalOpen: true });
}
```

**Restriction Strategy:**
- Prevents multi-shipping with promotional items
- Shows conflict modal
- Forces single shipping mode
- Maintains promotional item benefits

### Multi-Shipping Mode Switching

```typescript
private handleMultiShippingModeSwitch: () => void = async () => {
    const {
        consignments,
        isMultiShippingMode,
        onToggleMultiShipping = noop,
        onUnhandledError = noop,
        updateShippingAddress,
        deleteConsignments,
    } = this.props;

    try {
        this.setState({ isInitializing: true });

        if (isMultiShippingMode && consignments.length) {
            // Collapse all consignments into one
            await updateShippingAddress(consignments[0].shippingAddress);
        } else {
            await deleteConsignments();
        }
    } catch (error) {
        onUnhandledError(error);
    } finally {
        this.setState({ isInitializing: false });
    }

    onToggleMultiShipping();
};
```

**Switching Strategy:**
- Collapses multiple consignments to single address
- Deletes consignments when switching to multi
- Handles initialization state
- Provides error recovery

## Store Credit Integration

### Store Credit Application

```typescript
// Store credit amount calculation
const storeCreditAmount = isStoreCreditApplied ? 
    Math.min(grandTotal, storeCredit) : undefined;

// Store credit change handling
private handleStoreCreditChange: (useStoreCredit: boolean) => void = async (useStoreCredit) => {
    const { applyStoreCredit, onUnhandledError = noop } = this.props;

    try {
        await applyStoreCredit(useStoreCredit);
    } catch (e) {
        onUnhandledError(e);
    }
};
```

**Store Credit Strategy:**
- Calculates maximum applicable store credit
- Handles store credit application
- Integrates with promotional items
- Maintains currency consistency

## Discount and Coupon Logic

### Discount Display

```typescript
// Discount configuration
const isShippingDiscountDisplayEnabled = isExperimentEnabled(
    config.checkoutSettings,
    'PROJECT-6643.enable_shipping_discounts_in_orders',
);

// Discount amount calculation
const discountAmount = cart.discountAmount;
const shippingCostBeforeDiscount = checkout.shippingCostBeforeDiscount;
```

**Discount Strategy:**
- Feature flag controlled display
- Shows shipping discounts when enabled
- Calculates discount amounts
- Maintains discount visibility

### Coupon Integration

```typescript
// Coupon data structure
interface Coupon {
    id: string;
    code: string;
    displayName: string;
    discountedAmount: number;
}

// Coupon application
const coupons = checkout.coupons;
const isCouponApplied = coupons.length > 0;
```

**Coupon Strategy:**
- Tracks applied coupons
- Shows coupon codes
- Calculates discount amounts
- Integrates with promotional items

## Tax Handling

### Tax Display Logic

```typescript
// Tax configuration
const isTaxIncluded = cart.isTaxIncluded;
const taxes = checkout.taxes;

// Tax display
const displayInclusiveTax = isTaxIncluded && taxes && taxes.length > 0;

{displayInclusiveTax && <OrderSummarySection>
    <h5 className="cart-taxItem cart-taxItem--subtotal">
        <TranslatedString id="tax.inclusive_label" />
    </h5>
    {(taxes || []).map((tax, index) => (
        <OrderSummaryPrice
            amount={tax.amount}
            key={index}
            label={tax.name}
            testId="cart-taxes"
        />
    ))}
</OrderSummarySection>}
```

**Tax Strategy:**
- Handles inclusive vs exclusive tax
- Shows tax breakdown when applicable
- Displays individual tax items
- Maintains currency consistency

## Feature Flag System

### Experiment Configuration

```typescript
// Feature flag checking
const isShippingDiscountDisplayEnabled = isExperimentEnabled(
    config.checkoutSettings,
    'PROJECT-6643.enable_shipping_discounts_in_orders',
);

// Default newsletter signup
const defaultNewsletterSignupOption =
    data.getConfig()?.shopperConfig.defaultNewsletterSignup ?? false;
```

**Feature Flag Strategy:**
- Server-side feature flag configuration
- A/B testing support
- Gradual feature rollout
- Fallback to default values

## Performance Optimizations

### 1. Currency Caching

```typescript
// Currency data cached in store config
const shopperCurrency = config.shopperCurrency;
const storeCurrency = config.currency;
```

**Caching Strategy:**
- Currency data cached in config
- Reduces API calls
- Improves performance
- Maintains consistency

### 2. Conditional Rendering

```typescript
// Conditional promotional item display
{isShippingDiscountDisplayEnabled && (
    <OrderSummarySection>
        {/* Discount content */}
    </OrderSummarySection>
)}
```

**Rendering Strategy:**
- Only renders when feature enabled
- Reduces DOM complexity
- Improves performance
- Maintains feature flags

### 3. State Management

```typescript
// Efficient state updates
this.setState({
    isBillingSameAsShipping: checkoutBillingSameAsShippingEnabled,
    isSubscribed: defaultNewsletterSignupOption,
});
```

**State Strategy:**
- Batched state updates
- Minimal state changes
- Efficient re-renders
- Performance optimization

## Integration Points

The multi-currency and promotional item system integrates with:
- **BigCommerce SDK**: Currency conversion and promotional item data
- **Store Configuration**: Feature flags and settings
- **Cart Management**: Promotional item detection
- **Shipping System**: Multi-shipping restrictions
- **Payment System**: Store credit integration

## Source Files

- **Currency Handling**: `packages/core/src/app/cart/mapToCartSummaryProps.ts`
- **Promotional Items**: `packages/core/src/app/shipping/Shipping.tsx`
- **Multi-Shipping**: `packages/core/src/app/shipping/hooks/useMultishippingConsignmentItems.ts`
- **Store Credit**: `packages/core/src/app/payment/Payment.tsx`
- **Feature Flags**: `packages/core/src/app/common/utility.ts`
