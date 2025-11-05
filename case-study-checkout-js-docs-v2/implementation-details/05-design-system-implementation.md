# Implementation Details: The Design System

This document provides the low-level, evidence-based implementation details for the **UI & Component Slice**. It provides concrete code examples for the key architectural patterns described in the high-level `04-the-design-system.md` document.

## 1. The Layered Styling Architecture

The application's visual appearance is controlled by a three-layered SCSS architecture. Each layer builds upon the one below it, creating a cascade from a generic base to application-specific overrides.

### Layer 1: The Base Layer (`@bigcommerce/citadel`)
This is the foundational layer, providing a generic set of brand-agnostic styles and CSS variables for colors, fonts, and spacing.

*   **Code Evidence (`packages/core/src/scss/base.scss`):** The base layer is imported at the very top of the application's primary stylesheet.
    ```scss
    // packages/core/src/scss/base.scss
    @import "~@bigcommerce/citadel/src/scss/index";
    ```

### Layer 2: The Shared Component Layer (`packages/ui`)
This layer consumes the base styles and applies them to a set of shared, reusable React components.

*   **Code Evidence (`packages/ui/src/button/Button.scss`):** The component's stylesheet imports the base and applies citadel variables.
    ```scss
    // packages/ui/src/button/Button.scss
    @import "~@bigcommerce/citadel/src/scss/variables";

    .button {
        color: citadel-color(color-textSecondary);
        // ...
    }
    ```

### Layer 3: The Application-Specific Layer (`packages/core/src/scss`)
This is the final layer, which contains overrides and new styles that are specific to the `checkout-js` core application.

*   **Code Evidence (`packages/core/src/scss/components.scss`):** This file imports the shared UI component styles and can add application-specific overrides.
    ```scss
    // packages/core/src/scss/components.scss
    @import "~@bigcommerce/checkout/ui/src/scss/components";

    // Application-specific overrides would go here
    .optimizedCheckout-form-input {
        // ...
    }
    ```

## 2. The Forked Design System: An Architectural Inconsistency

As noted in the high-level architecture, the `core` application does not always consume the canonical design system from `packages/ui`. Instead, it often uses a **local, forked copy** located at `packages/core/src/app/ui`.

This is an architectural inconsistency that creates maintenance overhead and the potential for visual drift between the core application and other packages that might consume the canonical design system.

*   **Code Evidence (`packages/core/src/app/customer/GuestForm.tsx`):** This component imports its `Button` and `Fieldset` components from the local, forked directory, not the shared `packages/ui` package.
    ```typescript
    // packages/core/src/app/customer/GuestForm.tsx
    import { Button, ButtonVariant } from '../ui/button';
    import { Fieldset } from '../ui/form';
    ```
