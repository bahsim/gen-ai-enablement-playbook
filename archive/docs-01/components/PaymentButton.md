# PaymentSubmitButton Component

Primary action for continuing/placing order in the payment step. Text varies by payment method and state.

## Props (excerpt)
```ts
interface PaymentSubmitButtonProps {
    methodGateway?: string;
    methodId?: string;
    methodName?: string;
    methodType?: string;
    isDisabled?: boolean;
    initialisationStrategyType?: string;
    brandName?: string;
    isComplete?: boolean;
    isPaymentDataRequired?: boolean;
}
```

## Behavior
- Disabled if initializing, submitting, or explicitly `isDisabled`.
- Uses translation ids per provider rules (AmazonPay, Bolt, PayPal, PayPalCredit, VisaCheckout, etc.).
- Defaults to `payment.place_order_action` when data is not required.

## Source
- packages/core/src/app/payment/PaymentSubmitButton.tsx
