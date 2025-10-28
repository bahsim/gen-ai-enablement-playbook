# Spec: State Management Guide

## 1. Objective

To synthesize the deep architectural knowledge from the project's source code and legacy archives to generate the complete content for the **`01-application-architecture/02-state-management-guide.md`** document.

## 2. Rationale

State management is arguably the most complex and critical subsystem in this application. A detailed, authoritative guide is essential for any developer to work effectively and avoid introducing bugs. This document must serve as the "Level 2" deep-dive for the State Management slice, leveraging the rich, pre-existing documentation to ensure accuracy and depth.

## 3. Verification Criteria

The successful execution of this spec will result in the `01-application-architecture/02-state-management-guide.md` file being populated with the following sections, based on patterns found in the project's source code and validated by legacy documents like `archive/docs-00/core/architecture/core-data-flow-architecture.md`:

1.  **Guiding Philosophy:**
    *   Must explicitly state the project's philosophy of **avoiding a global state library like Redux**.
    *   Must establish the **BigCommerce Checkout SDK** as the single source of truth for all server-side checkout state.

2.  **The Five-Layer State Model:**
    *   Must describe the sophisticated, **five-layer state management model**: Application, Global, Context, HOC, and Component.
    *   For each layer, it must explain its purpose and provide a code reference or example from the `checkout-js` codebase.

3.  **Accessing State: The `withCheckout` HOC & `useCheckout` Hook:**
    *   Must provide practical guides on how to consume state in both class components (using the `withCheckout` Higher-Order Component) and functional components (using the modern `useCheckout()` hook).
    *   Must explain the role of `mapToCheckoutProps` for HOCs.

4.  **Multi-Directional Data Flow:**
    *   Must explain that data flow is not a simple one-way street.
    *   Must describe the four directions of data flow: **Top-Down** (props/context), **Bottom-Up** (events/callbacks), **Cross-Module**, and **External** (API calls).
    *   Must include **at least one Mermaid diagram** to visualize this multi-directional flow.

5.  **State Synchronization Patterns:**
    *   Must explain the common patterns for synchronizing state between the different layers, providing a concrete example of how local Formik state is synchronized with the global SDK state during form submission.
