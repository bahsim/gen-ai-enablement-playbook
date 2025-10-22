# Payment Error Handling (Payment Step)

Errors during payment submit/finalization are rendered via an ErrorModal in `Payment`.

## Sources
- `Payment.tsx` `renderOrderErrorModal()` uses:
  - `mapSubmitOrderErrorMessage(error, translate, shouldLocalise)`
  - `mapSubmitOrderErrorTitle(error, translate)`
  - Skips benign types (e.g., `order_finalization_not_required`, `payment_cancelled`, `payment_invalid_form`, `spam_protection_not_completed`, `invalid_hosted_form_value`).

## Message Mapping
- `mapSubmitOrderErrorMessage.ts`: converts error types/bodies into translated strings, including localized provider codes under `payment.errors.*` when `shouldLocaliseErrorMessages` is true.

## User Flows
- Some errors trigger reloads or redirects in `handleCloseModal` (e.g., `provider_fatal_error`, `order_could_not_be_finalized_error`, tax provider retry, cart consistency reload).

## i18n Keys
- Keys live under `optimized_checkout.payment.*` in `packages/locale/src/translations/*.json`.
