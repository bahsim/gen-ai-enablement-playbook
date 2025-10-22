# Golden Path Spec: Finalizing an Off-site Payment

This document is an executable contract for handling the return of a customer from an off-site payment provider (like PayPal or a 3D Secure verification).

## 1. Objective: The "What"

To implement the logic that correctly finalizes an order after a customer is redirected back to the checkout page from a third-party payment website.

## 2. Rationale: The "Why"

-   **Business Rationale:** Many popular payment methods require redirecting the user. Failing to handle their return correctly results in lost orders and a terrible customer experience. This flow is critical for supporting a wide range of payment options.
-   **Technical Constraints:**
    -   Must use the `@bigcommerce/checkout-sdk-js`.
    -   This logic should be executed as early as possible in the application's lifecycle, before the main checkout UI is rendered. It is a prerequisite for showing the order confirmation.

## 3. Verification Criteria: The "Proof"

This logic typically resides in a root-level component (like `Checkout.tsx` or `App.tsx`) and executes on every page load.

### Step 1: Check for Finalization Requirement
-   [ ] On application load, immediately after `service.loadCheckout()` completes successfully, the application must call `service.finalizeOrderIfNeeded()`.
-   [ ] This call should be wrapped in a `try...catch` block.

### Step 2: Handling a Successful Finalization
-   [ ] If `service.finalizeOrderIfNeeded()` resolves without throwing an error, it means the order was successfully finalized.
-   [ ] Upon successful finalization, the application must immediately redirect the user to the dedicated order confirmation page (e.g., `/order-confirmation`).

### Step 3: Handling Errors
-   [ ] If `service.finalizeOrderIfNeeded()` throws an error, the `catch` block must inspect the error `type`.
-   [ ] **Scenario A (Not Required):** If the error `type` is `'order_finalization_not_required'`, this is not a true error. It simply means the user loaded the page without being redirected back from a payment gateway. The application should ignore this "error" and proceed to render the normal checkout page.
-   [ ] **Scenario B (True Error):** If the error `type` is anything else (e.g., `'payment_cancelled'`, `'payment_failed'`), it signifies a real problem with the payment.
-   [ ] In the case of a true error, the application must display a clear error message to the user, instructing them to try the payment process again.
-   [ ] The application should then render the checkout page at the payment step, allowing the user to select a different payment method or retry.
