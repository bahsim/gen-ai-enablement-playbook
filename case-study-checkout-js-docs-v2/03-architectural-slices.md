---
**Title:** Architectural Slice Discovery
**Purpose:** To formally identify and define the primary, evidence-based "architectural slices" (cross-cutting concerns) present in the `checkout-js` codebase.
**Audience:** All Developers, Architects
**Maintenance:** Update whenever a new cross-cutting concern is introduced or an existing one is fundamentally changed.
---

# Architectural Slice Discovery

This document contains the definitive, evidence-based list of the primary architectural slices discovered through direct analysis of the `checkout-js` codebase. An "architectural slice" is a cross-cutting concern—a system that provides a specific, horizontal capability to the entire application.

This list serves as the foundational, factual basis for all subsequent architectural documentation and classification. Each slice identified here represents a major, verifiable building block of the system.

## The Discovered Architectural Slices

The following slices have been identified as the core cross-cutting concerns of the application:

*   **1. Step-Based View Management Slice**
    *   **Architectural Responsibility:** To manage the sequential flow of the user journey, orchestrating which primary feature module ("room") is active at any given time.
    *   **Primary Code Evidence:**
        *   `packages/core/src/app/checkout/CheckoutPage.tsx`: The orchestrator component that contains the primary step-rendering logic.
        *   `packages/core/src/app/checkout/CheckoutStepType.ts`: The enum that defines the distinct steps of the journey.

*   **2. State Management Slice**
    *   **Architectural Responsibility:** To provide a single, predictable source of truth for all shared application data, managed exclusively by the BigCommerce Checkout SDK.
    *   **Primary Code Evidence:**
        *   `packages/core/src/app/checkout/withCheckout.tsx`: The Higher-Order Component (HOC) used to inject state into class components.
        *   `packages/core/src/app/checkout/mapToCheckoutProps.ts`: The function that selects and maps state from the SDK to component props.

*   **3. Forms & Validation Slice**
    *   **Architectural Responsibility:** To provide a standardized, reusable system for managing form state, user input, and schema-based validation.
    *   **Primary Code Evidence:**
        *   `packages/core/src/app/address/AddressForm.tsx`: The canonical example of a form built with `Formik` for state management and `Yup` for the validation schema.
        *   `packages/ui/src/form/BasicFormField.tsx`: An example of a shared, reusable form component from the core UI library.

*   **4. UI & Component Slice**
    *   **Architectural Responsibility:** To provide the foundational, reusable visual building blocks of the user interface and enforce a consistent design system.
    *   **Primary Code Evidence:**
        *   `packages/ui/`: The dedicated monorepo package that houses the design system and all shared, presentation-only components (e.g., `Button`, `LoadingSpinner`).

*   **5. Internationalization (I18n) Slice**
    *   **Architectural Responsibility:** To manage and provide language translations for all user-facing text, ensuring the application can be localized.
    *   **Primary Code Evidence:**
        *   `packages/locale/src/withLanguage.tsx`: The HOC used to inject the language service into components.
        *   `packages/locale/src/TranslatedString.tsx`: The component used to render all translated text.

*   **6. Error Handling & Logging Slice**
    *   **Architectural Responsibility:** To provide a centralized, consistent system for catching, logging, and displaying application errors to the user.
    *   **Primary Code Evidence:**
        *   `packages/core/src/app/common/error/ErrorModal.tsx`: The primary UI component for displaying critical errors to the user.
        *   The `errorLogger` prop passed into `CheckoutPage.tsx`, originating from `packages/error-handling-utils/`.

*   **7. Integration & Extensibility Slice**
    *   **Architectural Responsibility:** To provide a decoupled "plugin" architecture that allows external systems, primarily payment providers, to be integrated into the checkout flow without modifying the core application.
    *   **Primary Code Evidence:**
        *   `packages/core/src/app/payment/Payment.tsx`: The component that implements the Inversion of Control (IoC) pattern for dynamically rendering and managing payment integrations.
        *   `packages/payment-integration-api/`: The dedicated package defining the contract and interface for all payment integrations.
