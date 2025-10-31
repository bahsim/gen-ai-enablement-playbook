---
**Title:** Utility Packages Reference
**Purpose:** A comprehensive, Level 3 inventory of all shared utility packages in the monorepo.
**Audience:** All Developers
**Maintenance:** Update when new utility packages are added or their APIs change significantly.
---

# Utility Packages Reference

This document is the definitive inventory of all shared utility packages that provide common, reusable functionality to the `core` application and other packages.

| Package Name | Purpose / Key Exports | Primary Consumers |
| :--- | :--- | :--- |
| **`@bigcommerce/checkout/ui`** | The shared React component library. Provides a consistent look-and-feel. Exports `<Button>`, `<FormField>`, `<Modal>`, etc. | `core`, `payment-integrations` |
| **`@bigcommerce/checkout/locale`** | The internationalization (I18n) system. Exports `withLanguage` HOC and `useLanguage()` hook. | `core` |
| **`@bigcommerce/checkout/error-handling-utils`** | Centralized error handling infrastructure. Exports `<ErrorBoundary>` and `ErrorLogger` service. | `core` |
| **`@bigcommerce/checkout/contexts`** | Provides shared React Contexts for cross-cutting concerns. | `core`, various packages |
| **`@bigcommerce/checkout/dom-utils`** | A collection of helper functions for DOM manipulation and observation. | `core`, `ui` |
| **`@bigcommerce/checkout/instrument-utils`** | Utilities for instrumenting and storing payment data. | `payment-integrations` |
| **`@bigcommerce/checkout/legacy-hoc`** | Contains legacy Higher-Order Components for backward compatibility. | `core` |
| **`@bigcommerce/checkout/utility`** | A general-purpose utility belt with common helper functions (e.g., for arrays, objects, strings). | All packages |
| **`@bigcommerce/checkout/payment-integration-api`** | **CRITICAL:** Defines the core TypeScript interfaces and services that form the contract for all payment integrations. | `payment-integrations`, `core` |
| **`@bigcommerce/checkout/bigcommerce-payments-utils`** | Specific helper functions for the BigCommerce Payments provider. | `bigcommerce-payments-integration` |
| **`@bigcommerce/checkout/paypal-utils`** | Specific helper functions and types for various PayPal integrations. | `paypal-*` integrations |
| **`@bigcommerce/checkout/checkout-button-integration`** | A specialized utility for rendering standardized checkout buttons (e.g., Google Pay, Apple Pay). | `core` |
| **`@bigcommerce/checkout/wallet-button-integration`** | A specialized utility for rendering standardized wallet buttons (e.g., Masterpass). | `core` |

## Developer Cookbook

This section provides practical, copy-pasteable examples ("recipes") for common development tasks that use the utility packages.

### Recipe: Building a Form with Standard UI Components

This recipe shows how to build a simple login form using the standardized `Form`, `Fieldset`, `Legend`, and `Button` components from the `@bigcommerce/checkout/ui` package, and specialized field components.

```typescript
// In any component that needs a form...
import { Button, ButtonVariant } from '@bigcommerce/checkout/ui/button';
import { Fieldset, Form, Legend } from '@bigcommerce/checkout/ui/form';
import EmailField from './EmailField'; // A specialized component for the email input
import PasswordField from './PasswordField'; // A specialized component for the password input

const MyFormComponent = () => (
    <Form testId="checkout-customer-returning">
        <Fieldset
            legend={
                <Legend hidden>
                    <TranslatedString id="customer.returning_customer_text" />
                </Legend>
            }
        >
            <EmailField />
            <PasswordField />

            <div className="form-actions">
                <Button
                    testId="customer-continue-button"
                    type="submit"
                    variant={ButtonVariant.Primary}
                >
                    <TranslatedString id="customer.sign_in_action" />
                </Button>
            </div>
        </Fieldset>
    </Form>
);
```

### Recipe: Translating a String

This recipe shows how to use the `<TranslatedString>` component from the `@bigcommerce/checkout/locale` package to render a localized string. The component requires the `withLanguage` Higher-Order Component to be wrapped around the component that uses it.

```typescript
// In any component that needs translated text...
import { withLanguage, TranslatedString } from '@bigcommerce/checkout/locale';

const MyComponent = () => (
    <Button
        testId="customer-continue-button"
        type="submit"
        variant={ButtonVariant.Primary}
    >
        {/* This string will be automatically translated based on the user's locale */}
        <TranslatedString id="customer.sign_in_action" />
    </Button>
);

export default withLanguage(MyComponent);
```
