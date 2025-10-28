# Spec: View Management Guide

## 1. Objective

To analyze the `C:\learn\checkout-js` project and generate the complete content for the **`01-application-architecture/04-view-management-guide.md`** document.

## 2. Rationale

The checkout is a linear, multi-step journey for the user. It is critical for developers to understand the state-driven mechanism that controls this journey, determining which "view" or "step" is currently active.

## 3. Verification Criteria

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
