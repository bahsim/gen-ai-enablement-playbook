---
**Title:** Checkout Steps - Component Trees
**Purpose:** Component rendering hierarchy trees for all checkout steps showing how components are nested and conditionally rendered.
**Audience:** All Developers
---

# Component Tree

## Customer Step

```
CheckoutStep (wrapper)
└── Customer.tsx (main component)
    ├── Conditional rendering based on CustomerViewType:
    │
    ├── [ViewType: Guest]
    │   ├── CheckoutButtonList.tsx (wallet buttons - top)
    │   │   └── CheckoutButtonContainer.tsx
    │   │       └── CheckoutButton.tsx (for each wallet method)
    │   ├── GuestForm.tsx
    │   │   ├── Email input
    │   │   ├── Newsletter subscription checkbox
    │   │   └── Abandoned cart emails checkbox
    │   └── CheckoutButtonList.tsx (wallet buttons - bottom)
    │
    ├── [ViewType: Login]
    │   ├── LoginForm.tsx
    │   │   ├── Email input
    │   │   ├── Password input
    │   │   ├── Sign in button
    │   │   ├── "Continue as guest" link
    │   │   └── "Create account" link
    │   └── EmailLoginForm.tsx (alternative)
    │       ├── Email input
    │       └── "Send login email" button
    │
    ├── [ViewType: CreateAccount]
    │   └── CreateAccountForm.tsx
    │       ├── Email input
    │       ├── Password input
    │       ├── Confirm password input
    │       ├── First name input
    │       └── Last name input
    │
    ├── [ViewType: SuggestedLogin]
    │   ├── LoginForm.tsx (with suggested login message)
    │   └── "Continue as guest" option
    │
    ├── [ViewType: EnforcedLogin]
    │   └── LoginForm.tsx (enforced, no guest option)
    │
    ├── [ViewType: CancellableEnforcedLogin]
    │   ├── LoginForm.tsx (enforced)
    │   └── Cancel button (conditional)
    │
    └── [Authenticated users]
        └── CustomerInfo.tsx
            └── Customer details display
```

## Shipping Step

```
CheckoutStep (wrapper)
└── Shipping.tsx (main component)
    ├── Conditional rendering based on mode and conditions:
    │
    ├── [StripeUPE special case]
    │   └── StripeShippingForm.tsx
    │       └── Stripe UPE shipping form
    │           └── (Rendered when: StripeUPE provider configured AND customer has no email AND countries loaded)
    │
    ├── [Single Shipping Mode]
    │   └── ShippingForm.tsx
    │       ├── AddressForm.tsx
    │       │   ├── Country selector (with country-specific fields)
    │       │   ├── State/province selector (conditional, based on country)
    │       │   ├── Address line 1 input
    │       │   ├── Address line 2 input (optional)
    │       │   ├── City input
    │       │   ├── Postal code input
    │       │   └── Phone number input (optional, based on config)
    │       │
    │       ├── ShippingMethodList.tsx
    │       │   ├── Loading state (while fetching options)
    │       │   └── ShippingOption.tsx (for each option)
    │       │       ├── Option name
    │       │       ├── Option description
    │       │       ├── Option price
    │       │       ├── Selection radio/checkbox
    │       │       └── onClick: Select shipping option via CheckoutService
    │       │
    │       ├── BillingSameAsShipping.tsx (optional)
    │       │   ├── "Use shipping address for billing" checkbox
    │       │   └── onChange: Update billing address if checked (if no remote billing)
    │       │
    │       ├── OrderComments.tsx (optional, if enabled in config)
    │       │   └── Customer message textarea
    │       │
    │       └── Submit button
    │           └── onClick: Update shipping address, update billing (if same), navigate to next step
    │
    ├── [Multi-Shipping Mode]
    │   └── MultiShippingForm.tsx
    │       ├── MultiShippingToggle.tsx
    │       │   ├── "Use multiple addresses" toggle
    │       │   └── onChange: Switch between single/multi mode
    │       │
    │       ├── ConsignmentList.tsx
    │       │   └── ConsignmentItem.tsx (for each consignment)
    │       │       ├── Consignment header (address label, item count)
    │       │       ├── AddressForm.tsx
    │       │       │   └── (Same fields as single shipping)
    │       │       ├── ShippingMethodList.tsx
    │       │       │   └── ShippingOption.tsx (for this consignment)
    │       │       ├── ItemAssignment.tsx
    │       │       │   ├── Cart items list (unassigned + assigned to this consignment)
    │       │       │   ├── Item checkboxes (assign/unassign)
    │       │       │   └── onChange: Assign items to consignment via CheckoutService
    │       │       └── Delete consignment button
    │       │           └── onClick: Delete consignment via CheckoutService
    │       │
    │       ├── UnassignedItems.tsx
    │       │   ├── Cart items not yet assigned to consignments
    │       │   └── "Create new consignment" button
    │       │       └── onClick: Create new consignment via CheckoutService
    │       │
    │       ├── OrderComments.tsx (optional)
    │       │   └── Customer message textarea
    │       │
    │       └── Submit button
    │           └── onClick: Update consignments, navigate to next step
    │
    ├── [Promotional items conflict]
    │   └── PromotionalItemsConflictModal.tsx
    │       ├── Conflict message (explains why multi-shipping unavailable)
    │       ├── "Switch to single shipping" button
    │       │   └── onClick: Force switch to single mode, delete consignments
    │       └── "Cancel" button
    │
    └── Lifecycle hooks:
        ├── useEffect: Load shipping address fields (country-specific)
        ├── useEffect: Load shipping options
        ├── useEffect: Load billing address fields (for "same as shipping")
        ├── useEffect: Detect promotional items conflict on mount
        └── useEffect: Show conflict modal if needed
```

## Billing Step

```
CheckoutStep (wrapper)
└── Billing.tsx (main component)
    ├── Conditional rendering based on payment method detection:
    │   (Payment method detected via getBillingMethodId.ts)
    │
    ├── [Amazon Pay]
    │   └── BillingForm.tsx
    │       ├── StaticAddressDisplay.tsx (read-only billing address)
    │       │   └── Address fields (display only, from Amazon Pay)
    │       ├── CustomFields.tsx (if Amazon Pay custom fields exist)
    │       │   └── Amazon Pay custom fields (editable)
    │       ├── OrderComments.tsx (optional)
    │       │   └── Customer message textarea
    │       └── Submit button
    │           └── onClick: Update custom fields (if changed), navigate to next step
    │
    ├── [PayPal Fastlane]
    │   └── BillingForm.tsx
    │       ├── PayPalAddressSelector.tsx
    │       │   ├── PayPal saved addresses list
    │       │   ├── Address selection radio/checkbox
    │       │   └── "Use new address" option
    │       ├── AddressForm.tsx (if "new address" selected)
    │       │   └── Standard address fields
    │       ├── OrderComments.tsx (optional)
    │       │   └── Customer message textarea
    │       └── Submit button
    │           └── onClick: Update billing address, navigate to next step
    │
    ├── [Google Pay with feature flag]
    │   └── BillingForm.tsx
    │       ├── AddressForm.tsx
    │       │   ├── Country selector
    │       │   ├── State/province selector
    │       │   ├── Address line 1 input
    │       │   ├── Address line 2 input
    │       │   ├── City input
    │       │   ├── Postal code input
    │       │   └── Phone number input
    │       ├── OrderComments.tsx (optional)
    │       │   └── Customer message textarea
    │       └── Submit button
    │           └── onClick: Update billing address, navigate to next step
    │
    ├── [Default payment method]
    │   └── BillingForm.tsx
    │       ├── AddressForm.tsx
    │       │   ├── Country selector (with country-specific fields)
    │       │   ├── State/province selector (conditional)
    │       │   ├── Address line 1 input
    │       │   ├── Address line 2 input (optional)
    │       │   ├── City input
    │       │   ├── Postal code input
    │       │   └── Phone number input (optional)
    │       ├── PaymentMethodSelector.tsx (if multiple methods available)
    │       │   └── PaymentMethodOption.tsx (for each method)
    │       │       ├── Method name
    │       │       ├── Method icon
    │       │       ├── Selection radio/checkbox
    │       │       └── onChange: Select payment method, re-render form if needed
    │       ├── BillingSameAsShipping.tsx (optional, if shipping step exists)
    │       │   ├── "Use shipping address for billing" checkbox
    │       │   └── onChange: Copy shipping address to billing
    │       ├── OrderComments.tsx (optional)
    │       │   └── Customer message textarea
    │       └── Submit button
    │           └── onClick: Update billing address (if changed), navigate to next step
    │
    ├── [Wallet payment selected]
    │   └── BillingForm.tsx
    │       ├── StaticAddressDisplay.tsx (read-only, from wallet)
    │       │   └── Address fields (display only, from payment provider)
    │       ├── OrderComments.tsx (optional)
    │       │   └── Customer message textarea
    │       └── Submit button
    │           └── onClick: Navigate to next step (address from wallet, no update needed)
    │
    └── Lifecycle hooks:
        ├── useEffect: Load billing address fields (country-specific)
        └── useEffect: Detect payment method on mount/change
```

## Payment Step

```
CheckoutStep (wrapper)
└── Payment.tsx (main component)
    ├── PaymentContext.Provider
    │   ├── Context value: { registerSubmitFunction, registerValidationSchema, setSubmitButtonState }
    │   │
    │   └── PaymentForm.tsx
    │       ├── PaymentMethodList.tsx
    │       │   └── PaymentMethodOption.tsx (for each method)
    │       │       ├── Method name
    │       │       ├── Method icon
    │       │       ├── Selection radio/checkbox
    │       │       └── onClick: Select payment method, initialize component
    │       │
    │       ├── Conditional rendering based on selected payment method:
    │       │   (Payment method components register submit functions via PaymentContext)
    │       │
    │       ├── [Credit Card / Stripe]
    │       │   └── StripePaymentForm.tsx
    │       │       ├── Uses PaymentContext: registerSubmitFunction, registerValidationSchema
    │       │       ├── Card number input (Stripe Elements)
    │       │       ├── Expiry date input (Stripe Elements)
    │       │       ├── CVV input (Stripe Elements)
    │       │       ├── Cardholder name input
    │       │       ├── Billing postal code input
    │       │       └── Submit function: Stripe payment processing
    │       │
    │       ├── [PayPal]
    │       │   └── PayPalPaymentForm.tsx
    │       │       ├── Uses PaymentContext: registerSubmitFunction
    │       │       ├── PayPal button/iframe
    │       │       ├── PayPal SDK integration
    │       │       └── Submit function: PayPal payment processing
    │       │
    │       ├── [Braintree]
    │       │   └── BraintreePaymentForm.tsx
    │       │       ├── Uses PaymentContext: registerSubmitFunction, registerValidationSchema
    │       │       ├── Hosted fields container
    │       │       │   ├── Card number (hosted field)
    │       │       │   ├── Expiry date (hosted field)
    │       │       │   └── CVV (hosted field)
    │       │       └── Submit function: Braintree payment processing
    │       │
    │       ├── [Apple Pay / Google Pay]
    │       │   └── WalletPaymentForm.tsx
    │       │       ├── Uses PaymentContext: registerSubmitFunction
    │       │       ├── Wallet payment button
    │       │       ├── Wallet SDK integration
    │       │       └── Submit function: Wallet payment processing
    │       │
    │       ├── [Buy Now Pay Later]
    │       │   └── BNPLPaymentForm.tsx
    │       │       ├── Uses PaymentContext: registerSubmitFunction, registerValidationSchema
    │       │       ├── BNPL provider form
    │       │       └── Submit function: BNPL payment processing
    │       │
    │       ├── [Other payment methods]
    │       │   └── GenericPaymentForm.tsx
    │       │       ├── Uses PaymentContext: registerSubmitFunction, registerValidationSchema
    │       │       └── Payment method-specific form fields
    │       │
    │       ├── StoreCreditDisplay.tsx (if store credit applied)
    │       │   ├── Store credit amount display
    │       │   ├── "Remove store credit" button
    │       │   └── onClick: Remove store credit via CheckoutService
    │       │
    │       └── PaymentSubmitButton.tsx
    │           ├── Uses PaymentContext: Submit button state (disabled/hidden)
    │           ├── Submit button
    │           ├── Loading state (during order submission)
    │           ├── onClick: Submit order (calls registered submit function or standard submission)
    │           └── Error display (if submission fails)
    │
    ├── ErrorModal.tsx (if error occurs)
    │   ├── Error message (localized)
    │   ├── Recovery actions:
    │   │   ├── "Reload checkout" button
    │   │   ├── "Go to cart" button
    │   │   └── "Close" button
    │   └── onClick handlers for recovery actions
    │
    ├── OrderStatusIndicator.tsx (when order is processing/complete)
    │   ├── Processing indicator
    │   ├── Order completion status
    │   └── Order confirmation details
    │
    └── Lifecycle hooks:
        ├── useEffect: Apply store credit on mount (if available)
        ├── useEffect: Subscribe to cart total changes
        ├── useEffect: Setup beforeunload handler
        └── useEffect cleanup: Unsubscribe, remove handlers
```

