---
spec_version: 2
spec_id: checkout-js-view-management-guide
title: Spec: View Management Guide
description: To define the structure and content for the guide to the View Management slice.
objective: To generate a comprehensive "wiring diagram" that documents the View Management slice, a hybrid Cross-Cutting Concern responsible for orchestrating the user journey.
---

### 1. Architectural Principles

The documentation for the View Management slice **must** be framed by the following core principles:

*   **Principle of the Orchestration Slice:** View Management is a specialized, hybrid **Cross-Cutting Concern**. While it is a global slice that affects the entire application, its logic is tightly coupled to the business domain (the linear steps of a checkout). Its primary role is to **orchestrate** the user's journey through the various vertical feature slices.
*   **Principle of State-Driven Control:** The user's position in the flow is not managed by the components themselves. It is controlled exclusively by the global `CheckoutState`. This decouples the feature components from the overall application flow, allowing them to focus on their specific responsibilities.

### 2. Rationale

The checkout is a linear, multi-step journey. This guide is crucial for documenting how this "Orchestration Slice" is architected to provide a predictable, state-driven mechanism for controlling the user's journey and determining which "view" or "step" is currently active.

### 3. Verification Criteria

The successful execution of this spec will result in the `01-application-architecture/04-view-management-guide.md` file being populated with the following:

1.  **The State-Driven Model:**
    *   Must explain that the user's current position in the checkout flow is controlled by the active step property within the global `CheckoutState`, which is managed by the SDK.

2.  **The Full Lifecycle of a View:**
    *   Must provide a complete, end-to-end "wiring diagram" of the view management slice.
    *   Must include a **Mermaid sequence diagram** that illustrates the full lifecycle, from `renderCheckout.tsx` bootstrapping the app, through `CheckoutPage.tsx` loading state and rendering a step, to the user completing the step and triggering a re-render with the next step.

3.  **The Central Controller Pattern:**
    *   Must identify the `CheckoutPage.tsx` component as the central controller.
    *   Must explain the **Controller-View Callback Pattern** used for managing navigation between steps, where the controller passes callback functions as props to the view components.
    *   Must provide **two targeted code snippets** to illustrate the pattern:
        *   One showing the **Controller** (`CheckoutPage.tsx`) passing a navigation callback (e.g., `onContinueAsGuest`) as a prop to a **View** component (e.g., `<Customer />`).
        *   A second snippet showing the **View** component (`Customer.tsx`) invoking that callback after completing its work.

4.  **The Navigation Mechanism:**
    *   Must explain the final part of the navigation loop.
    *   Must include a code snippet showing the implementation of the navigation callback (e.g., `navigateToNextIncompleteStep`) inside the Controller, demonstrating how the next step is determined and the state change is triggered.
