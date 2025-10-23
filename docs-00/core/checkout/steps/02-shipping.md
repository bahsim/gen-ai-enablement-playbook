# Shipping Step - Explained

## Core Responsibility & Design

The Shipping step is responsible for collecting the **shipping destination address and the selected shipping method**. It's a highly complex step that supports both single-address checkouts and multi-shipping scenarios.

Its design follows the same pattern as the other steps: it acts as a container that renders the lazy-loaded `Shipping` component within the generic `CheckoutStep`. The `Checkout` orchestrator manages its state and passes down all necessary data and callbacks.

## Implementation Details

-   **Component Rendered**: The `Shipping` component (`packages/core/src/app/shipping/Shipping.tsx`) is the main feature component for this step.
-   **Lazy Loading**: This component is **lazy-loaded** with a `webpackChunkName: "shipping"` to optimize the initial page load.
-   **State Management**: It's a controlled component. The `isBillingSameAsShipping` and `isMultiShippingMode` flags are passed down from the `Checkout` orchestrator. When the user completes the step, it calls the `navigateNextStep` prop to inform the parent.
-   **Key Feature**: It contains significant logic to handle multi-shipping, where users can assign different line items to different shipping addresses.

## Role in the Checkout Flow

The Shipping step occurs **after** the `Customer` step and **before** the `OrderDetails` step. For physical products, its completion is mandatory. The checkout flow cannot proceed until a valid shipping address and shipping option have been selected for all shippable items in the cart.

## Key Takeaway

The Shipping step encapsulates all address and shipping method logic within the `Shipping` component. The `Checkout.tsx` orchestrator is responsible for lazy-loading this component, providing it with the current cart and consignment data, and handling the completion event to navigate to the next step.
