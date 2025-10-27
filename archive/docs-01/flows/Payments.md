# Payments Flow

## Overview
This document describes the end-to-end payment flow across checkout steps, focusing on how `BillingAndPayment`, `Payment`, and `PaymentForm` collaborate to submit an order.

## Sequence
```mermaid
sequenceDiagram
    participant Checkout
    participant BillingAndPayment
    participant Billing
    participant Payment
    participant PaymentForm
    participant SDK as checkoutService

    Checkout->>BillingAndPayment: render (onReady, onSubmit, onFinalize)
    BillingAndPayment->>Billing: render (navigateNextStep=submitPayment)
    BillingAndPayment->>Payment: render (callbacks, flags)
    BillingAndPayment->>BillingAndPayment: handlePlaceOrderClick()
    BillingAndPayment->>Billing: submitBilling()
    Billing-->>BillingAndPayment: success -> trigger submitPayment()
    BillingAndPayment->>PaymentForm: programmatic submit when combined

    Payment->>Payment: componentDidMount()
    Payment->>SDK: loadPaymentMethods()
    SDK-->>Payment: methods loaded
    Payment->>SDK: finalizeOrderIfNeeded()
    SDK-->>Payment: order? (may be already finalized)

    PaymentForm->>PaymentForm: validate via getPaymentValidationSchema
    PaymentForm->>Payment: onSubmit(values)
    Payment->>SDK: submitOrder(mapToOrderRequestBody(values))
    SDK-->>Payment: CheckoutSelectors (contains order)
    Payment->>Checkout: onSubmit(orderId)
    Payment->>Checkout: onFinalize(orderId)
```

## Key Responsibilities
- `BillingAndPayment`: couples Billing and Payment UX, exposes a single "Place Order" action; wires readiness via context.
- `Payment`: loads methods, handles spam thresholds, finalization, modal errors, and orchestrates `submitOrder`.
- `PaymentForm`: renders methods, store credit, terms, spam, and submits sanitized `PaymentFormValues`.

## Error Handling
- Cart changed: surfaced via `onCartChangedError` for host remediation.
- Provider/validation errors: mapped to localized messages; may trigger reloads or redirects based on headers.
- Spam protection: toggles `didExceedSpamLimit` and reloads checkout when necessary.

## Multi-shipping and Embedded
- Multi-shipping filters incompatible methods.
- Embedded flows check `checkEmbeddedSupport` and can raise an embedded support modal.

## Analytics Hooks
- `clickPayButton`, `paymentComplete`, `paymentRejected`, and `selectedPaymentMethod` emitted from `Payment`.


