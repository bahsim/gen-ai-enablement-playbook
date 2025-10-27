# Golden Path Spec: Passwordless Sign-In

This document is an executable contract for implementing the passwordless sign-in flow.

## 1. Objective: The "What"

To implement a UI component that allows a customer to sign in by receiving a one-time login link to their email address.

## 2. Rationale: The "Why"

-   **Business Rationale:** Passwordless sign-in reduces friction for customers who may have forgotten their password, leading to higher rates of customer login and a smoother checkout experience.
-   **Technical Constraints:**
    -   Must use the `@bigcommerce/checkout-sdk-js`.
    -   The implementation must be a self-contained React component.
    -   The email form must be managed by `Formik`.
    -   All user-facing strings must use the `<TranslatedString />` component.

## 3. Verification Criteria: The "Proof"

### Step 1: Requesting the Sign-In Link
-   [ ] The component must render a form with an "Email" input field and a "Send Login Link" button.
-   [ ] When the button is clicked, the `service.sendSignInEmail()` method must be called with an object containing the `email`.
-   [ ] While the request is in progress, the form and button should be disabled. This status can be checked with `state.statuses.isSendingSignInEmail()`.
-   [ ] Upon successful execution, the component must display a confirmation message to the user, instructing them to check their email for the login link.
-   [ ] The success state can be verified by checking `state.data.isSignInEmailSent()`, which should return `true`.
-   [ ] If `service.sendSignInEmail()` fails, a specific error message must be displayed. The error can be retrieved from `state.errors.getSendSignInEmailError()`.

### Step 2: Handling the Redirect (Conceptual)
-   [ ] The spec must note that the user will click a link in their email and be redirected back to the checkout page.
-   [ ] Upon their return to the page, the main `Checkout.tsx` component (or equivalent) should automatically detect the signed-in state via `state.data.getCustomer().isGuest` being `false`, and the passwordless sign-in flow is complete. (This verification point belongs in the main checkout spec but is noted here for context).
