# Golden Path Spec: Apply and Remove a Coupon

This document is an executable contract for implementing the feature of applying a coupon code to the checkout. It serves as the single source of truth for both human developers and AI assistants.

## 1. Objective: The "What"

To implement a UI component that allows a user to:
1.  Enter a coupon code into a text input.
2.  Submit the code to apply it to the current checkout.
3.  See the successfully applied coupon and the updated order total.
4.  Remove a previously applied coupon.

This entire process will be orchestrated using the `@bigcommerce/checkout-sdk-js`.

## 2. Rationale: The "Why"

-   **Business Rationale:** Promotions and discounts are a key driver of sales and customer loyalty. Providing a clear and functional interface for applying coupon codes is essential for the checkout experience.
-   **Technical Constraints:**
    -   Must use `@bigcommerce/checkout-sdk-js` for all coupon-related actions. **No direct API calls.**
    -   The implementation must be a self-contained React component.
    -   Form input for the coupon code must be managed by `Formik`.
    -   All user-facing strings must use the `<TranslatedString />` component.

## 3. Verification Criteria: The "Proof"

This is the checklist that defines a successful implementation.

### Step 1: Component State & Display
-   [ ] The component must subscribe to the `CheckoutService` state.
-   [ ] It must display a text input field for the coupon code and a "Apply" button.
-   [ ] If `state.data.getCheckout().coupons` contains one or more coupons, the component must display the applied coupon code(s) and a "Remove" button for each.
-   [ ] If there are no applied coupons, the input field should be visible.

### Step 2: Applying a Coupon
-   [ ] When the "Apply" button is clicked, the `service.applyCoupon()` method must be called with the code from the input field.
-   [ ] While the coupon is being applied, the "Apply" button and input field should be disabled. This status can be checked with `state.statuses.isApplyingCoupon()`.
-   [ ] Upon successful application, the component should re-render to display the applied coupon code as described in Step 1.
-   [ ] If `service.applyCoupon()` fails, a specific error message must be displayed to the user. The error can be retrieved from `state.errors.getApplyCouponError()`.

### Step 3: Removing a Coupon
-   [ ] When a user clicks the "Remove" button next to an applied coupon, the `service.removeCoupon()` method must be called with that coupon's code.
-   [ ] While the coupon is being removed, the "Remove" button for that coupon should be disabled. This status can be checked with `state.statuses.isRemovingCoupon()`.
-   [ ] Upon successful removal, the component should re-render to show the input field again.
-   [ ] If `service.removeCoupon()` fails, a specific error message must be displayed. The error can be retrieved from `state.errors.getRemoveCouponError()`.
