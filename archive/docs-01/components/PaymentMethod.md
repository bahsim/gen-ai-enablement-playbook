# PaymentMethod Components

This document covers the selection list and item renderers for payment methods in checkout.

## Components
- `PaymentMethodList`: Renders a `Checklist` of payment methods and manages selection via Formik's `paymentProviderRadio` field.
- `PaymentMethodListItem`: Wraps `PaymentMethodV2` with a `ChecklistItem` or `CustomChecklistItem` when the method declares `initializationData.isCustomChecklistItem`.

## PaymentMethodList Props (excerpt)
```typescript
interface PaymentMethodListProps {
    isEmbedded?: boolean;
    isInitializingPayment?: boolean;
    isUsingMultiShipping?: boolean;
    methods: PaymentMethod[];
    onSelect?(method: PaymentMethod): void;
    onUnhandledError?(error: Error): void;
}
```

## Key Behaviors
- Uses `getUniquePaymentMethodId` to map a method's `(id, gateway)` to a stable radio value; parsing is done with `parseUniquePaymentMethodId`.
- Honors `initializationData.showOnlyOnMobileDevices` to conditionally hide items on non-mobile.
- Calls `onSelect` with the full `PaymentMethod` object derived from the selected radio value.
- Disables selection while `isInitializingPayment` is true.
- Supports embedded checkout rendering via the `isEmbedded` flag propagated to `PaymentMethodV2`.

## Integration with PaymentForm
- `PaymentForm` renders `PaymentMethodList` inside a `Fieldset` and, upon selection, resets sensitive fields, clears submission state, and calls `onMethodSelect` so the parent `Payment` can update selection.

## Error Handling
- Downstream rendering errors are routed through `onUnhandledError` to unify error logging.

## Performance Notes
- `PaymentMethodListItem` memoizes the method content using `useMemo` to avoid re-rendering expensive method UIs when unrelated props change.


