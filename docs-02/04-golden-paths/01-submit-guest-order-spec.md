# Golden Path Spec: Submit a Guest Order

This document is an executable contract for implementing the "Guest Checkout" user journey. It serves as the single source of truth for both human developers and AI assistants.

## 1. Objective: The "What"

To implement a complete, single-page guest checkout flow that allows a user to:
1.  Provide shipping and billing addresses.
2.  Select a shipping option.
3.  Enter payment information (using a mock credit card).
4.  Submit the order.

This entire process will be orchestrated using the `@bigcommerce/checkout-sdk-js`.

## 2. Rationale: The "Why"

-   **Business Rationale:** The guest checkout is the most critical conversion path for new customers. A seamless and predictable flow is essential for maximizing sales.
-   **Technical Constraints:**
    -   Must use `@bigcommerce/checkout-sdk-js` for all state management and API interactions. **No direct API calls.**
    -   The implementation must be a single React component using class component syntax as per existing project patterns.
    -   All form state must be managed by `Formik`.
    -   All user-facing strings must use the `<TranslatedString />` component.
    -   Styling must adhere to the project's SCSS and BEM conventions.

## 3. Verification Criteria: The "Proof"

This is the checklist that defines a successful implementation.

### Step 1: Initialization
-   [ ] On component mount, a `CheckoutService` instance must be created using `createCheckoutService()`.
-   [ ] The current checkout state must be loaded by calling `service.loadCheckout()`.
-   [ ] A subscriber must be attached using `service.subscribe()` to listen for state changes. The component's local state must be updated in response to new data from the SDK.

### Step 2: Guest Sign-In & Shipping Address
-   [ ] The component must render a form (using `Formik`) for the user's email address.
-   [ ] On email submission, `service.continueAsGuest({ email })` must be called.
-   [ ] After guest sign-in, the component must render a shipping address form.
-   [ ] The fields for the shipping address form must be dynamically retrieved using `state.data.getShippingAddressFields()`.
-   [ ] On shipping address form submission, `service.updateShippingAddress()` must be called with the address payload.

### Step 3: Shipping Options
-   [ ] After a shipping address is successfully submitted, the component must display a list of available shipping options.
-   [ ] The shipping options must be retrieved from `state.data.getShippingOptions()`.
-   [ ] When a user selects an option, `service.selectShippingOption()` must be called with the `id` of the chosen option.

### Step 4: Billing Address & Payment
-   [ ] The component must render a billing address form. It can be pre-filled from the shipping address.
-   [ ] On billing address form submission, `service.updateBillingAddress()` must be called.
-   [ ] After the billing address is set, available payment methods must be loaded with `service.loadPaymentMethods()`.
-   [ ] The component must render a mock credit card payment form.
-   [ ] The chosen payment method must be initialized using `service.initializePayment({ methodId: 'braintree' })`.

### Step 5: Order Submission
-   [ ] A "Place Order" button must be present.
-   [ ] When clicked, `service.submitOrder()` must be called with the payment payload. The payload structure must match the SDK's requirements for a credit card payment.
-   [ ] While the order is submitting, the "Place Order" button must be disabled, and a loading indicator must be shown. The loading status can be checked via `state.statuses.isSubmittingOrder()`.

### Step 6: Error Handling
-   [ ] If `service.loadCheckout()` fails, an error message must be displayed. The error can be retrieved from `state.errors.getLoadCheckoutError()`.
-   [ ] If `service.submitOrder()` fails, a specific error message must be displayed. The error can be retrieved from `state.errors.getSubmitOrderError()`.
-   [ ] At any point, if the cart changes (e.g., an item becomes unavailable), a notification must be shown. This can be checked with `state.data.hasCartChanged()`.
