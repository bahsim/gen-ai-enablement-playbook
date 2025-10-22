# TranslatedString Component

## Purpose
`TranslatedString` renders localized text by id, using the active language context and translation catalogs under `packages/locale/src/translations/*.json`.

## Usage
```tsx
import { TranslatedString } from '@bigcommerce/checkout/locale';

<Button>
    <TranslatedString id="order_confirmation.continue_shopping" />
    </Button>
```

## Notes
- Keys must exist in the active locale JSON (e.g., `en.json`, `de.json`).
- Prefer semantic keys scoped by feature (e.g., `payment.payment_methods_text`).
- Avoid concatenation; use interpolation features provided by the i18n layer where supported.


