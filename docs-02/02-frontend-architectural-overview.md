# Frontend Architectural Overview

This document provides a high-level map of the frontend application's structure, major components, and data flow patterns.

## 1. Core Principles
- **Modularity:** The application is built as a monorepo with distinct packages for different concerns (core logic, UI components, payment integrations).
- **Component-Based:** The UI is composed of small, reusable React components.
- **State Management:** All checkout-related state is managed centrally by the `@bigcommerce/checkout-sdk-js`. Component-level UI state is handled locally.
- **Type Safety:** The entire codebase is written in TypeScript to ensure type safety and improve developer experience.

## 2. High-Level Diagram

```mermaid
graph TD
    subgraph "Browser"
        App[React Application Root]
    end

    subgraph "Core Application Logic"
        SDK[CheckoutService (@bigcommerce/checkout-sdk-js)]
        State[CheckoutState]
        HOCs[withCheckout HOC]
    end
    
    subgraph "UI Components"
        Checkout[Checkout.tsx]
        Customer[CustomerStep.tsx]
        Shipping[ShippingStep.tsx]
        Payment[PaymentStep.tsx]
    end

    SDK -- Manages --> State
    State -- Injected into --> HOCs
    HOCs -- Wraps --> Checkout
    App -- Renders --> Checkout
    Checkout -- Composes --> Customer
    Checkout -- Composes --> Shipping
    Checkout -- Composes --> Payment

    style SDK fill:#bbf,stroke:#333,stroke-width:2px
```

## 3. Key Architectural Patterns

### State Management: `withCheckout` HOC
The primary pattern for accessing checkout state is the `withCheckout` Higher-Order Component (HOC).

- **Purpose:** To inject the `CheckoutState` and `CheckoutService` as props into any component that needs them.
- **Mechanism:** It connects to the `CheckoutContext` provided by the SDK and maps the state to the component's props. This avoids prop-drilling and centralizes the logic for state access.
- **Example:** A component that needs the cart details would be wrapped like `export default withCheckout(CartSummary);`. Inside `CartSummary`, it can then access `this.props.cart`.

### Component Structure: Feature-Based Organization
Components are organized by feature or checkout step within the `src/app/` directory.

- **`src/app/checkout/`:** Contains the main `Checkout.tsx` component that orchestrates the overall flow.
- **`src/app/customer/`:** Components related to guest/customer sign-in.
- **`src/app/shipping/`:** Components for displaying and selecting shipping options.
- **`src/app/billing/` & `src/app/payment/`:** Components for address forms and payment method integration.

### Payment Integrations: Modular Packages
Each payment provider (Stripe, PayPal, etc.) is implemented in its own separate package within the monorepo.

- **Benefit:** This isolates the complexity of each integration and allows them to be loaded on demand. The core checkout application interacts with a standardized payment method interface, without needing to know the specific details of each provider.

## 4. Data Flow
1.  **Initialization:** The main `App` component initializes the `CheckoutService` and loads the initial `CheckoutState`.
2.  **User Interaction:** A user interacts with a component (e.g., fills out the shipping address form).
3.  **Action Dispatch:** The component (wrapped by `withCheckout`) calls a method on the `checkoutService` prop (e.g., `this.props.checkoutService.updateShippingAddress(address)`).
4.  **SDK Handles Logic:** The Checkout SDK sends the request to the BigCommerce API, handles the response, and updates its internal `CheckoutState`.
5.  **State Propagation:** The `CheckoutContext` is updated with the new state.
6.  **Re-render:** The `withCheckout` HOC receives the new state and re-renders the wrapped component with updated props (e.g., the component now has access to `this.props.consignments` containing the new shipping options).
