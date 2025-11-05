---
**Title:** Architectural Slices: Code Evidence
**Purpose:** To provide a definitive, evidence-based list of the primary code examples that implement the architectural slices defined in `03-the-architectural-structure.md`.
**Audience:** All Developers, Architects
**Maintenance:** Update when the primary implementation of a slice changes.
---

# Architectural Slices: Code Evidence

This document provides the primary code evidence for the architectural slices defined in the high-level `03-the-architectural-structure.md` document. Its purpose is to serve as a direct, verifiable map from the conceptual architecture to the physical codebase.

## The Core Application

*   **1. Step-Based View Management Slice**
    *   **Primary Code Evidence:**
        *   `packages/core/src/app/checkout/CheckoutPage.tsx`: The orchestrator component that contains the primary step-rendering logic.
        *   `packages/core/src/app/checkout/CheckoutStepType.ts`: The enum that defines the distinct steps of the journey.

## The Supplementary Slices

*   **2. State Management Slice**
    *   **Primary Code Evidence:**
        *   `packages/payment-integration-api/src/contexts/checkout-context.ts`: The source of the `useCheckout` hook (modern access pattern) and the `CheckoutContext` (the underlying provider).
        *   `packages/core/src/app/checkout/withCheckout.tsx`: The Higher-Order Component (HOC) used for the legacy state access pattern.

*   **3. Forms & Validation Slice**
    *   **Primary Code Evidence:**
        *   `packages/core/src/app/address/AddressForm.tsx`: The canonical example of a form built to be driven by `Formik` for state management and `Yup` for the validation schema.
        *   `packages/ui/src/form/BasicFormField.tsx`: An example of a shared, reusable form component from the core UI library.

*   **4. UI & Component Slice**
    *   **Primary Code Evidence:**
        *   `packages/ui/`: The dedicated monorepo package that houses the **canonical** design system.
        *   `packages/core/src/app/ui`: The **forked/duplicate** design system, which is an architectural inconsistency.

*   **5. Internationalization (I18n) Slice**
    *   **Primary Code Evidence:**
        *   `packages/locale/src/withLanguage.tsx`: The HOC used to inject the language service into components.
        *   `packages/locale/src/TranslatedString.tsx`: The component used to render all translated text.

*   **6. Error Handling & Logging Slice**
    *   **Primary Code Evidence:**
        *   `packages/core/src/app/common/error/ErrorModal.tsx`: The primary UI component for displaying critical errors to the user.
        *   The `onUnhandledError` prop passed from `CheckoutPage.tsx` down to the feature modules, demonstrating the Error Delegation pattern.

*   **7. Integration & Extensibility Slice**
    *   **Primary Code Evidence:**
        *   `packages/core/src/app/payment/paymentMethod/PaymentMethod.tsx`: The component that implements the **Strategy Pattern** for dynamically rendering payment integrations.
        *   `packages/payment-integration-api/`: The dedicated package defining the public contract and interface (the Façade) for all payment integrations.
