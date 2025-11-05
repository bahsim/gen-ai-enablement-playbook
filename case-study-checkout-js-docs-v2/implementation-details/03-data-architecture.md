---
**Title:** Data Architecture & Room Patterns
**Purpose:** To define the complete architectural picture of the application's data entities, persistence mechanisms, and the unified form-as-pattern that connects all feature modules.
**Audience:** All Developers, Architects
**Maintenance:** Update when new data entities are introduced or the persistence mechanism fundamentally changes.
---

# Implementation Details: Data Architecture

This document provides a low-level, evidence-based guide to the data architecture patterns used within the `checkout-js` application. It connects the high-level architectural concepts to concrete implementation patterns and code evidence.

## 1. The Core Data Entities

The application's data architecture is centered around a set of core entities provided by the BigCommerce Checkout SDK. These entities represent the state of the checkout process.

*   **`Checkout`**: The primary entity, containing information about the cart, consignments, and promotions.
*   **`Consignment`**: Represents a shipment of one or more line items to a specific address.
*   **`Customer`**: The customer associated with the checkout.
*   **`BillingAddress`**: The billing address for the order.
*   **`Order`**: The final order object, created upon successful checkout completion.

**Code Evidence:** These interfaces are defined within the `@bigcommerce/checkout-sdk` package.

## 2. The Feature-Level Data Pattern: Container/Presentational

While the high-level data flow is a hybrid model, the internal structure of each "smart" Feature Module follows the classic **Container/Presentational Pattern**. This pattern separates data-fetching and logic from the rendering of UI.

*   **The "Container" Component:**
    *   **Responsibility:** To be the "smart" entry point for the feature. It connects directly to the Data Layer (via the `useCheckout` hook or `withCheckout` HOC), fetches all necessary data, and contains all the business logic, state management, and event handlers for that feature.
    *   **Code Evidence (`Shipping.tsx`):**
        ```typescript
        // packages/core/src/app/shipping/Shipping.tsx
        const Shipping: FunctionComponent<ShippingProps> = ({
            isBillingSameAsShipping,
            // ... other props from Orchestrator
        }) => {
            const { checkoutState } = useCheckout(); // Direct data connection
            const { data: { getShippingAddress } } = checkoutState;
            
            // ... business logic, event handlers ...

            return (
                <ShippingForm
                    // ... passes data and callbacks down as props ...
                />
            );
        };
        ```

*   **The "Presentational" Component(s):**
    *   **Responsibility:** To be "dumb" components that are only responsible for rendering UI. They receive all the data and callbacks they need as props from their container parent. They have no direct knowledge of the Data Layer or business logic.
    *   **Code Evidence (`ShippingForm.tsx`):**
        ```typescript
        // packages/core/src/app/shipping/ShippingForm.tsx
        const ShippingForm: FunctionComponent<ShippingFormProps> = ({
            isMultiShippingMode,
            onUseNewAddress,
            shippingAddress,
            // ... many other props ...
        }) => (
            // ... purely presentational JSX ...
        );
        ```

This two-part structure creates a clear separation of concerns within each feature, making the components more reusable, testable, and easier to reason about.

