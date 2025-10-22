# Golden Path Spec: Customer Sign-In

This document is an executable contract for implementing the customer sign-in flow within the checkout.

## 1. Objective: The "What"

To implement a UI component that allows a returning customer to sign in with their email and password, which will then populate the checkout with their saved information.

## 2. Rationale: The "Why"

-   **Business Rationale:** Providing a simple sign-in process for returning customers is crucial for a fast and frictionless checkout experience, which increases conversion rates.
-   **Technical Constraints:**
    -   Must use `@bigcommerce/checkout-sdk-js` for all authentication actions.
    -   The implementation must be a self-contained React component.
    -   The sign-in form must be managed by `Formik`.
    -   All user-facing strings must use the `<TranslatedString />` component.

## 3. Verification Criteria: The "Proof"

### Step 1: Component State & Display
-   [ ] The component must render a form with "Email" and "Password" input fields and a "Sign In" button.
-   [ ] The component must subscribe to the `CheckoutService` state to access customer data.

### Step 2: Customer Sign-In
-   [ ] When the "Sign In" button is clicked, the `service.signInCustomer()` method must be called with an object containing `email` and `password`.
-   [ ] While the sign-in is in progress, the form and button should be disabled. This status can be checked with `state.statuses.isSigningIn()`.
-   [ ] Upon successful sign-in, the checkout state will automatically update with the customer's data (addresses, etc.). The component should ideally trigger a navigation or state change to move the user to the next step of the checkout. This can be verified by checking `state.data.getCustomer().isGuest` which should now be `false`.
-   [ ] If `service.signInCustomer()` fails, a specific error message (e.g., "Invalid credentials") must be displayed. The error can be retrieved from `state.errors.getSignInError()`.

### Step 3: Customer Sign-Out
-   [ ] The component must display the customer's email (from `state.data.getCustomer().email`) and a "Sign Out" button if the user is already signed in.
-   [ ] When the "Sign Out" button is clicked, the `service.signOutCustomer()` method must be called.
-   [ ] While signing out, the "Sign Out" button should be disabled. This can be checked with `state.statuses.isSigningOut()`.
-   [ ] Upon successful sign-out, the component should re-render to show the sign-in form again.
