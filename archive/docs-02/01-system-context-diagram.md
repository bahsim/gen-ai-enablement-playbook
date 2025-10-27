# System Context Diagram

This document provides a high-level visual map of the BigCommerce React Checkout system, its boundaries, and its interactions with external systems and users.

```mermaid
graph TD
    subgraph "BigCommerce Ecosystem"
        A[Customer Browser]
        B(BigCommerce React Checkout)
        C{BigCommerce Storefront API}
    end

    subgraph "External Systems"
        D[Payment Gateway (e.g., Stripe, PayPal)]
        E[Shipping Provider API (e.g., ShipStation)]
        F[Analytics Service (e.g., Google Analytics)]
    end

    A -- Interacts with --> B
    B -- Fetches Cart/Product Data --> C
    B -- Submits Order Data --> C
    B -- Processes Payments Via --> D
    B -- Fetches Shipping Rates --> E
    B -- Sends Tracking Events --> F

    style B fill:#bbf,stroke:#333,stroke-width:2px
```

## Diagram Components

### BigCommerce Ecosystem

-   **Customer Browser:** The end-user's web browser where the checkout application is rendered and interacted with.
-   **BigCommerce React Checkout:** **(Our System)** The single-page application responsible for orchestrating the entire checkout flow.
-   **BigCommerce Storefront API:** The primary API our system uses to fetch cart data, customer information, and submit the final order.

### External Systems

-   **Payment Gateway:** Third-party services (e.g., Stripe, PayPal, Braintree) that securely process the customer's payment information. The React Checkout application communicates with these gateways via the BigCommerce Checkout SDK.
-   **Shipping Provider API:** External services that provide real-time shipping quotes based on the customer's address and cart contents.
-   **Analytics Service:** External services that receive tracking events (e.g., "Checkout Started," "Order Completed") to monitor user behavior and business performance.
