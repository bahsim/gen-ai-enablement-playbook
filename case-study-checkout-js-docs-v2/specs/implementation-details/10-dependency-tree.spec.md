---
**Title:** Spec for: Checkout Steps - Component Trees
**Purpose:** To define the structure and mandatory content for component rendering hierarchy trees showing how components are nested and conditionally rendered for all checkout steps.
**Audience:** Internal
---

# Spec: Checkout Steps - Component Trees

## 1. Document Purpose

The document `10-dependency-tree.md` must serve as a **component rendering hierarchy reference** showing how components are nested and conditionally rendered for each checkout step, from a developer's perspective.

## 2. Mandatory Sections

The document must contain the following sections:

### 1. Introduction
*   Must state that this shows component rendering hierarchy
*   Must clarify this is from a developer's perspective (not user's visual perspective)

### 2. Customer Step
*   Must show complete component tree starting from `CheckoutStep` wrapper
*   Must show conditional rendering based on `CustomerViewType`
*   Must include all view types:
    *   Guest
    *   Login
    *   CreateAccount
    *   SuggestedLogin
    *   EnforcedLogin
    *   CancellableEnforcedLogin
    *   Authenticated users
*   Must show child components for each view type
*   Must use ASCII tree format with proper indentation

### 3. Shipping Step
*   Must show complete component tree starting from `CheckoutStep` wrapper
*   Must show conditional rendering based on mode and conditions:
    *   StripeUPE special case
    *   Single Shipping Mode
    *   Multi-Shipping Mode
    *   Promotional items conflict
*   Must show child components for each mode
*   Must include lifecycle hooks section
*   Must use ASCII tree format with proper indentation

### 4. Billing Step
*   Must show complete component tree starting from `CheckoutStep` wrapper
*   Must show conditional rendering based on payment method detection:
    *   Amazon Pay
    *   PayPal Fastlane
    *   Google Pay (with feature flag)
    *   Default payment method
    *   Wallet payment selected
*   Must show child components for each payment method variation
*   Must include lifecycle hooks section
*   Must use ASCII tree format with proper indentation

### 5. Payment Step
*   Must show complete component tree starting from `CheckoutStep` wrapper
*   Must show `PaymentContext.Provider` structure
*   Must show conditional rendering based on selected payment method:
    *   Credit Card / Stripe
    *   PayPal
    *   Braintree
    *   Apple Pay / Google Pay
    *   Buy Now Pay Later
    *   Other payment methods
*   Must show `PaymentContext` usage in payment method components
*   Must show error modal and order status indicator
*   Must include lifecycle hooks section
*   Must use ASCII tree format with proper indentation

## 3. Principles

*   **Rendering hierarchy:** Shows how components are nested, not file dependencies
*   **Conditional rendering:** Clearly shows when each component renders
*   **ASCII tree format:** Uses consistent tree structure with proper indentation
*   **Developer perspective:** Focuses on component structure, not visual appearance
*   **Complete coverage:** Includes all major conditional branches
*   **No implementation details:** Shows component names and structure, not code

## 4. Mandatory Content

### Tree Format
*   Must use ASCII tree characters: `└──`, `├──`, `│`
*   Must use consistent indentation (spaces or tabs)
*   Must show parent-child relationships clearly
*   Conditional branches must be marked with `[Condition: Description]`

### Component Names
*   Must use actual component names (e.g., `Customer.tsx`, `ShippingForm.tsx`)
*   Must include file extensions for clarity
*   Must show wrapper components (e.g., `CheckoutStep`)

### Conditional Rendering
*   Must clearly mark conditional branches with `[Condition]` format
*   Must explain when each branch renders
*   Must show all major conditional paths

### Lifecycle Hooks
*   Must list relevant `useEffect` hooks for each step
*   Must describe what each hook does
*   Must show cleanup hooks where applicable

## 5. Verification Criteria

*   Component names match actual codebase components
*   Conditional rendering logic matches step architectural docs
*   Tree structure is logically correct (parent-child relationships)
*   All major conditional branches are covered
*   ASCII tree formatting is consistent and readable
*   Lifecycle hooks match actual implementation patterns

## 6. Maintenance Note

This document must be updated when:
*   Component structure changes
*   New conditional rendering branches are added
*   Component names change
*   New lifecycle hooks are added
*   Step architectural logic changes

