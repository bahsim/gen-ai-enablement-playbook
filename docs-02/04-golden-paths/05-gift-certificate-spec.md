# Golden Path Spec: Apply and Remove Gift Certificates

This document is an executable contract for implementing the gift certificate feature.

## 1. Objective: The "What"

To implement a UI component that allows a user to:
1.  Enter a gift certificate code into a text input.
2.  Submit the code to apply it to the current checkout.
3.  See the successfully applied gift certificate and the updated order total.
4.  Remove a previously applied gift certificate.

This entire process will be orchestrated using the `@bigcommerce/checkout-sdk-js`.

## 2. Rationale: The "Why"

-   **Business Rationale:** Gift certificates are a common payment method and promotional tool. A clear and functional interface is essential for a good user experience.
-   **Technical Constraints:**
    -   Must use `@bigcommerce/checkout-sdk-js` for all gift certificate actions.
    -   The implementation must be a self-contained React component.
    -   The input form must be managed by `Formik`.
    -   All user-facing strings must use the `<TranslatedString />` component.

## 3. Verification Criteria: The "Proof"

### Step 1: Component State & Display
-   [ ] The component must subscribe to the `CheckoutService` state.
-   [ ] It must display a text input field for the gift certificate code and an "Apply" button.
-   [ ] If `state.data.getGiftCertificates()` returns an array with one or more gift certificates, the component must display the applied gift certificate(s) and a "Remove" button for each.
-   [ ] If there are no applied gift certificates, the input field should be visible.

### Step 2: Applying a Gift Certificate
-   [ ] When the "Apply" button is clicked, the `service.applyGiftCertificate()` method must be called with the code from the input field.
-   [ ] While the gift certificate is being applied, the form and button should be disabled. This status can be checked with `state.statuses.isApplyingGiftCertificate()`.
-   [ ] Upon successful application, the component should re-render to display the applied gift certificate as described in Step 1.
-   [ ] If `service.applyGiftCertificate()` fails, a specific error message must be displayed. The error can be retrieved from `state.errors.getApplyGiftCertificateError()`.

### Step 3: Removing a Gift Certificate
-   [ ] When a user clicks the "Remove" button next to an applied gift certificate, the `service.removeGiftCertificate()` method must be called with that certificate's code.
-   [ ] While the certificate is being removed, the "Remove" button should be disabled. This can be checked with `state.statuses.isRemovingGiftCertificate()`.
-   [ ] Upon successful removal, the component should re-render to show the input field again.
-   [ ] If `service.removeGiftCertificate()` fails, a specific error message must be displayed. The error can be retrieved from `state.errors.getRemoveGiftCertificateError()`.
