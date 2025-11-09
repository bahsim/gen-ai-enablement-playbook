---
**Title:** Codebase Navigation Guide
**Purpose:** Quick scenario-based navigation to find exact files for common checkout changes.
**Audience:** All Developers
---

# Codebase Navigation Guide

Pick your scenario below to see which files to investigate, in order.

## Customer Step Scenarios

### Change Customer Step Completion Logic
**Files to check (in order):**
1. `packages/core/src/app/checkout/getCheckoutStepStatuses.ts` - Step-level completion rules
2. `packages/core/src/app/customer/Customer.tsx` - Step component completion detection
3. `packages/core/src/app/customer/useCustomer.ts` - Data/state for completion checks
4. `packages/core/src/app/customer/CustomerViewType.ts` - View type affects completion

### Change Customer View Types (Login/Guest/Create Account)
**Files to check (in order):**
1. `packages/core/src/app/customer/CustomerViewType.ts` - View type definitions
2. `packages/core/src/app/customer/Customer.tsx` - View type rendering and transitions
3. `packages/core/src/app/customer/useCustomer.ts` - View type state management

### Add/Modify Wallet Payment Integration
**Files to check (in order):**
1. `packages/core/src/app/customer/getSupportedMethods.ts` - Wallet payment method detection
2. `packages/core/src/app/customer/Customer.tsx` - Wallet payment UI rendering
3. `packages/core/src/app/customer/useCustomer.ts` - Wallet payment actions

### Change Guest Checkout Flow
**Files to check (in order):**
1. `packages/core/src/app/customer/Customer.tsx` - Guest checkout UI
2. `packages/core/src/app/customer/useCustomer.ts` - Guest checkout actions
3. `packages/core/src/app/checkout/getCheckoutStepStatuses.ts` - Guest completion rules

## Shipping Step Scenarios

### Change Shipping Step Completion Logic
**Files to check (in order):**
1. `packages/core/src/app/checkout/getCheckoutStepStatuses.ts` - Step-level completion rules
2. `packages/core/src/app/shipping/Shipping.tsx` - Step component completion detection
3. `packages/core/src/app/shipping/hooks/useShipping.ts` - Data/state for completion checks

### Modify Multi-Shipping Mode
**Files to check (in order):**
1. `packages/core/src/app/shipping/isUsingMultiShipping.ts` - Multi-shipping detection
2. `packages/core/src/app/shipping/Shipping.tsx` - Multi-shipping UI and logic
3. `packages/core/src/app/shipping/hooks/useShipping.ts` - Multi-shipping state/actions
4. `packages/core/src/app/checkout/getCheckoutStepStatuses.ts` - Multi-shipping completion rules

### Change Promotional Items Handling
**Files to check (in order):**
1. `packages/core/src/app/shipping/hasPromotionalItems.tsx` - Promotional items detection
2. `packages/core/src/app/shipping/Shipping.tsx` - Promotional items UI
3. `packages/core/src/app/checkout/getCheckoutStepStatuses.ts` - Promotional items editability rules

### Modify Custom Shipping Options
**Files to check (in order):**
1. `packages/core/src/app/shipping/Shipping.tsx` - Custom shipping UI
2. `packages/core/src/app/shipping/hooks/useShipping.ts` - Custom shipping actions
3. `packages/core/src/app/checkout/getCheckoutStepStatuses.ts` - Custom shipping completion/editability

## Billing Step Scenarios

### Change Billing Step Completion Logic
**Files to check (in order):**
1. `packages/core/src/app/checkout/getCheckoutStepStatuses.ts` - Step-level completion rules
2. `packages/core/src/app/billing/Billing.tsx` - Step component completion detection
3. `packages/core/src/app/billing/BillingForm.tsx` - Form validation affects completion

### Modify Payment Method Detection
**Files to check (in order):**
1. `packages/core/src/app/billing/getBillingMethodId.ts` - Payment method identification
2. `packages/core/src/app/billing/Billing.tsx` - Payment method rendering
3. `packages/core/src/app/billing/BillingForm.tsx` - Payment method form handling

### Change Billing Address Handling
**Files to check (in order):**
1. `packages/core/src/app/billing/Billing.tsx` - Billing address UI
2. `packages/core/src/app/billing/BillingForm.tsx` - Billing address form
3. `packages/core/src/app/address/` - Address validation/mapping utilities

## Payment Step Scenarios

### Change Payment Step Completion Logic
**Files to check (in order):**
1. `packages/core/src/app/checkout/getCheckoutStepStatuses.ts` - Step-level completion rules
2. `packages/core/src/app/payment/Payment.tsx` - Step component completion detection
3. `packages/core/src/app/payment/PaymentForm.tsx` - Payment form validation

### Modify Order Submission Flow
**Files to check (in order):**
1. `packages/core/src/app/payment/PaymentSubmitButton.tsx` - Submit button logic
2. `packages/core/src/app/payment/Payment.tsx` - Order submission coordination
3. `packages/core/src/app/payment/PaymentForm.tsx` - Payment form submission

### Change Payment Method Registration
**Files to check (in order):**
1. `packages/core/src/app/payment/Payment.tsx` - Payment method registration logic
2. `packages/core/src/app/payment/PaymentForm.tsx` - Payment method form handling
3. `packages/core/src/app/checkout/getCheckoutStepStatuses.ts` - Registration affects completion

## Cross-Step Scenarios

### Change Step Editability Rules
**Files to check (in order):**
1. `packages/core/src/app/checkout/getCheckoutStepStatuses.ts` - Step-level editability rules
2. `packages/core/src/app/checkout/CheckoutPage.tsx` - Global editability constraints
3. Step component (e.g., `Customer.tsx`, `Shipping.tsx`) - Step-specific editability logic

### Modify Step Navigation/Progression
**Files to check (in order):**
1. `packages/core/src/app/checkout/CheckoutPage.tsx` - Step navigation logic
2. `packages/core/src/app/checkout/getCheckoutStepStatuses.ts` - Step activation rules
3. Step components - Step completion triggers navigation

### Change Step Filtering (Required/Optional Steps)
**Files to check (in order):**
1. `packages/core/src/app/checkout/getCheckoutStepStatuses.ts` - Step requirement logic
2. `packages/core/src/app/checkout/CheckoutPage.tsx` - Step filtering and rendering

### Modify Data Flow Between Steps
**Files to check (in order):**
1. `packages/core/src/app/checkout/mapToCheckoutProps.ts` - Data transformation
2. `packages/core/src/app/checkout/CheckoutPage.tsx` - Step coordination
3. Step components - Step-to-step data interactions

## Orchestration Scenarios

### Change Step Orchestration Logic
**Files to check (in order):**
1. `packages/core/src/app/checkout/CheckoutPage.tsx` - Main orchestration component
2. `packages/core/src/app/checkout/getCheckoutStepStatuses.ts` - Step status computation
3. `packages/core/src/app/checkout/CheckoutStep.tsx` - Step wrapper component

### Modify Step Lazy Loading
**Files to check (in order):**
1. `packages/core/src/app/checkout/CheckoutPage.tsx` - Lazy loading implementation
2. Step components - Component exports for lazy loading

## Entry Points

**Application initialization:**
- `packages/core/src/app/index.ts` - Main exports
- `packages/core/src/app/checkout/renderCheckout.tsx` - React app mounting
- `packages/core/src/app/checkout/CheckoutApp.tsx` - Service initialization, providers

**Component hierarchy:**
```
CheckoutApp (providers)
  └─> Checkout
       └─> CheckoutPage
            └─> CheckoutStep (for each step)
                 ├─> Customer
                 ├─> Shipping
                 ├─> Billing
                 └─> Payment
```

## Quick File Reference by Category

**Orchestration:**
- `packages/core/src/app/checkout/CheckoutPage.tsx` - Step rendering, navigation, filtering
- `packages/core/src/app/checkout/getCheckoutStepStatuses.ts` - Step status computation
- `packages/core/src/app/checkout/mapToCheckoutProps.ts` - Data transformation
- `packages/core/src/app/checkout/CheckoutStep.tsx` - Step wrapper

**Customer step:**
- `packages/core/src/app/customer/Customer.tsx` - Main component
- `packages/core/src/app/customer/useCustomer.ts` - Data and actions hook
- `packages/core/src/app/customer/CustomerViewType.ts` - View type enum
- `packages/core/src/app/customer/getSupportedMethods.ts` - Wallet payment methods

**Shipping step:**
- `packages/core/src/app/shipping/Shipping.tsx` - Main component
- `packages/core/src/app/shipping/hooks/useShipping.ts` - Data and actions hook
- `packages/core/src/app/shipping/isUsingMultiShipping.ts` - Multi-shipping detection
- `packages/core/src/app/shipping/hasPromotionalItems.tsx` - Promotional items check

**Billing step:**
- `packages/core/src/app/billing/Billing.tsx` - Main component
- `packages/core/src/app/billing/BillingForm.tsx` - Form rendering
- `packages/core/src/app/billing/getBillingMethodId.ts` - Payment method detection

**Payment step:**
- `packages/core/src/app/payment/Payment.tsx` - Main component
- `packages/core/src/app/payment/PaymentForm.tsx` - Form wrapper
- `packages/core/src/app/payment/PaymentSubmitButton.tsx` - Submit button logic

**Utilities:**
- `packages/core/src/app/address/` - Address validation, mapping
- `packages/core/src/app/formFields/` - Form field validation
- `packages/core/src/app/common/error/` - Error handling utilities

## Search Strategies

**Find function/component:**
- `grep -r "functionName" packages/core/src/app`
- `grep -r "<ComponentName" packages/core/src/app`

**Find data access:**
- `grep -r "getCustomer\|getShipping\|getBilling\|getOrder" packages/core/src/app`

**Understand behavior:**
- Semantic search: "How does X work?"
- Semantic search: "Where is Y handled?"

**Find tests:**
- Component tests: `*.test.tsx` next to component files
- Utility tests: `*.test.ts` next to utility files
- Integration tests: `checkout/Checkout.test.tsx`
