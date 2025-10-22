# Golden Path Spec: Loading the Order Confirmation Page

This document is an executable contract for implementing the order confirmation page.

## 1. Objective: The "What"

To implement a UI component that fetches and displays the details of a completed order to the customer immediately after a successful checkout.

## 2. Rationale: The "Why"

-   **Business Rationale:** The order confirmation page is a critical component for providing customers with the assurance that their order was successfully processed. It must be accurate and reliable.
-   **Technical Constraints:**
    -   Must use the `@bigcommerce/checkout-sdk-js`.
    -   The component should expect an `orderId` to be available (e.g., from the URL).
    -   All user-facing strings must use the `<TranslatedString />` component.

## 3. Verification Criteria: The "Proof"

### Step 1: Fetching the Order Details
-   [ ] On component mount, the component must retrieve the `orderId` from its props or from the page URL.
-   [ ] The `service.loadOrder()` method must be called with the `orderId`.
-   [ ] While the order details are loading, a loading indicator should be displayed. This status can be checked with `state.statuses.isLoadingOrder()`.

### Step 2: Displaying the Order
-   [ ] Upon successful loading, the component must display key details of the order.
-   [ ] The order data must be retrieved from `state.data.getOrder()`.
-   [ ] The following order details must be displayed:
    -   Order number (`order.orderId`)
    -   Order total (`order.orderAmount`)
    -   Shipping and billing addresses
    -   Line items (products purchased)
-   [ ] The component should also provide a "Continue Shopping" link or button.

### Step 3: Handling Errors
-   [ ] If `service.loadOrder()` fails (e.g., the order ID is invalid or the user does not have permission to view it), a clear error message must be displayed.
-   [ ] The error details can be retrieved from `state.errors.getLoadOrderError()`.
-   [ ] The component should not display any order information if the fetch fails.
