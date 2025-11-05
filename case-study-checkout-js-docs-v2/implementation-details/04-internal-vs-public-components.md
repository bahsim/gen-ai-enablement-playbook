---
**Title:** Internal vs. Public Components: A Case Study
**Purpose:** To analyze a concrete, evidence-based example of the architectural pattern where two components with the same name serve different roles: one for internal orchestration and one as a public, extensible API.
**Audience:** All Developers, Architects
**Maintenance:** Update only if the fundamental separation between these components changes.
---

# Internal vs. Public Components: A Case Study

## 1. The Core Thesis

The `checkout-js` codebase deliberately and strategically contains components that share the same name but serve two fundamentally different architectural purposes. One version of a component is designed for **internal orchestration**—as a deeply integrated part of the default checkout flow—while the other serves as a **public extensibility endpoint**, providing a stable, self-contained API for customizers.

This pattern is a cornerstone of the application's extensibility, and understanding it is critical to understanding the overall architecture.

## 2. The Primary Evidence: A Side-by-Side Comparison

The clearest example of this pattern is the `HostedCreditCardPaymentMethod` component, which exists in two distinct packages.

---

### A. The **Internal** Orchestrator
**File:** `packages/core/src/app/payment/paymentMethod/HostedCreditCardPaymentMethod.tsx`

This component is part of the internal wiring of the default checkout.

*   **Key Imports:** It imports its dependencies from other modules *within the `core` package*, such as `../hostedCreditCard` and `./CreditCardPaymentMethod`. It is tightly coupled to the internal structure of the default application.
    ```typescript
    import {
        withHostedCreditCardFieldset,
    } from '../hostedCreditCard';

    import CreditCardPaymentMethod from './CreditCardPaymentMethod';
    ```

*   **Architectural Role:** Its purpose is to orchestrate a complex set of behaviors *within* the default UI. It's a wrapper that pulls together other internal components and HOCs to present a generic "Credit Card" option that can be backed by many different payment gateways (Stripe, CyberSource, SagePay, etc.).

---

### B. The **Public** Extensibility Endpoint
**File:** `packages/hosted-credit-card-integration/src/HostedCreditCardPaymentMethod.tsx`

This component is a self-contained, public-facing integration.

*   **Key Imports:** It has no knowledge of the `core` application's internal structure. Its dependencies are exclusively from the public `@bigcommerce/checkout/payment-integration-api` package. This package defines the stable "contract" that all external payment integrations must adhere to.
    ```typescript
    import {
        type PaymentMethodProps,
        toResolvableComponent,
    } from '@bigcommerce/checkout/payment-integration-api';
    ```

*   **Architectural Role:** Its purpose is to be a clean, reusable, and stable API endpoint for developers who are customizing the checkout. It is wrapped in `toResolvableComponent`, which formally registers it with the system under a set of specific IDs (e.g., `'hosted-credit-card'`). It is designed to be imported directly via its package alias.

---

## 3. The "Why": Architectural Justification

This deliberate separation is not code duplication; it is a critical architectural choice that provides two key benefits:

1.  **Stability for Customizers:** By providing a dedicated, public-facing component that relies only on a stable API contract, the system gives external developers a safe and predictable integration point. They can build their customizations against this component without worrying that an internal refactor of the default checkout's UI will break their code.

2.  **Flexibility for the Core Team:** By using a separate, internal version of the component, the core team retains the freedom to refactor, change, and optimize the default checkout experience at any time. Because customizers do not depend on this internal component, these changes will not break third-party integrations.

This pattern is the foundation of the checkout's robust and flexible "plugin" architecture.
