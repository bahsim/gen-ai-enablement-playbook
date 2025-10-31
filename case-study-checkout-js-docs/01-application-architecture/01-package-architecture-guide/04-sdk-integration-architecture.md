---
**Title:** SDK Integration Architecture
**Purpose:** A comprehensive, Level 3 deep-dive into the patterns for interacting with the BigCommerce SDK.
**Audience:** All Developers
**Maintenance:** Update if the core SDK interaction patterns change.
---

# SDK Integration Architecture

This document provides a deep-dive into the primary architectural pattern for integrating the UI with the `@bigcommerce/checkout-sdk-js`.

## 1. The Core Pattern: `withCheckout` Higher-Order Component (HOC)

The primary pattern for accessing checkout state is the **`withCheckout` Higher-Order Component (HOC)**. This is the designated "front door" for any component that needs to read from or write to the checkout's state machine.

*   **Purpose:** To inject the `CheckoutState` and the `CheckoutService` as props into any React component.
*   **Mechanism:** It connects to the `CheckoutContext` provided by the SDK at the root of the application and maps the state and service to the wrapped component's props. This architectural choice provides two key benefits:
    1.  **Decoupling:** It avoids prop-drilling, meaning components deep in the tree can access global state without their parents needing to know about it.
    2.  **Centralization:** It centralizes the logic for state access, ensuring components get state in a consistent and predictable way.
*   **Example:** A component that needs the cart details would be wrapped like this: `export default withCheckout(CartSummary);`. Inside the `CartSummary` component, it can then access `this.props.cart`.

## 2. The Reactive Data Flow

The integration architecture is a classic reactive data flow model. The UI does not modify state directly; instead, it dispatches actions to the SDK and "reacts" to the resulting state changes.

This flow has five distinct steps:

1.  **User Interaction:** A user interacts with a UI component (e.g., fills out the shipping address form).
2.  **Action Dispatch:** The component, which has been wrapped by `withCheckout`, calls a method on its `checkoutService` prop (e.g., `this.props.checkoutService.updateShippingAddress(address)`).
3.  **SDK Handles Logic:** The Checkout SDK receives the action and payload. It orchestrates the necessary business logic, such as sending the request to the BigCommerce API.
4.  **State Propagation:** The SDK handles the API response and updates its internal `CheckoutState`. The central `CheckoutContext` is then updated with this new, immutable state object.
5.  **Re-render:** The `withCheckout` HOC detects the change in the `CheckoutContext` and triggers a re-render of the wrapped component, passing in the updated state as new props (e.g., the component now has access to `this.props.consignments` containing the new shipping options).
