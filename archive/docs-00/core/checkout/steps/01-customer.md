# Customer Step - Explained

## Core Responsibility & Design

The Customer step is responsible for handling all aspects of customer identification, including **guest checkout, user login, and new account creation**. It is the first interactive step for most users.

Its primary design is to act as a container for the complex `Customer` component, which is rendered within the generic `CheckoutStep` component. The `Checkout` orchestrator determines which view (Guest, Login, etc.) is active via the `customerViewType` state property and passes it down.

## Implementation Details

-   **Component Rendered**: The `Customer` component (`packages/core/src/app/customer/Customer.tsx`) is the main feature component for this step.
-   **Lazy Loading**: This step is **eagerly loaded** (not lazy-loaded) because it is on the critical path of the checkout flow.
-   **State Management**: It is largely controlled by the parent `Checkout` component's state. It receives the `customerViewType` and step status as props. When a user takes an action (like logging in or continuing as a guest), it calls prop functions (`onSignIn`, `onContinueAsGuest`) to notify the parent orchestrator.
-   **Key Feature**: It often includes wallet buttons (like Google Pay, Apple Pay) as an accelerated login/checkout option, which is also configured by the `Checkout` orchestrator.

## Role in the Checkout Flow

The Customer step is the **first step** in the checkout sequence. Its completion is a prerequisite for proceeding to the `Shipping` step. The main goals are to either associate the checkout with an existing customer account or collect the necessary contact information for a guest checkout.

## Key Takeaway

The Customer step's logic is mostly encapsulated within the dedicated `Customer` component. The `Checkout.tsx` orchestrator's role is to render it with the correct state and handle the events that signal completion or a change in customer status.
