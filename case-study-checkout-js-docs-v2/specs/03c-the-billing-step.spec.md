---
**Title:** Spec for The Billing Step
**Purpose:** To define the structure and content for `03c-the-billing-step.md`, ensuring it provides a comprehensive architectural blueprint of the Billing step's business rules, completion logic, and behavior.
---

# Spec for The Billing Step

## 1. Objective

This document must provide a comprehensive architectural blueprint of the **Billing step**—its business rules, completion logic, requirement conditions, and special handling for different payment methods (wallet payments, Amazon Pay, Google Pay, PayPal).

## 2. Rationale

The step progression rules document (`02a-the-step-progression-rules.md`) explains the general pattern for all steps, but does not detail the specific business rules and logic for the Billing step. This document fills that gap by providing a deep dive into Billing step specifics.

## 3. Core Components (Must be included)

The document must be structured with the following top-level sections:

1. **Billing Step Overview:** The role of the Billing step in the checkout flow and its primary responsibilities.

2. **Completion Logic:** Detailed explanation of what makes the Billing step complete (billing address OR wallet payment).

3. **Requirement Logic:** When the Billing step is required (always required, but completion can be bypassed with wallet payments).

4. **Payment Method Variations:** How different payment methods (wallet payments, Amazon Pay, Google Pay, PayPal) affect Billing step completion and editability.

5. **Editability Rules:** When and why the Billing step can be edited, including payment method-specific restrictions.

6. **Step Lifecycle and Actions:** Initialization, user actions, error handling.

7. **Data Dependencies and Outputs:** What data the step reads and writes.

8. **Prerequisites:** What must be complete before this step.

9. **Integration Boundaries:** External systems integrated.

10. **Architectural Decisions and Trade-offs:** Key design decisions with rationale.

11. **Evolution and Extension Points:** How the step can be extended.

12. **Cross-Step Interactions:** How this step affects and is affected by other steps.

## 4. Mandatory Content

### Billing Step Overview
- Occurs after Shipping step (or Customer step if Shipping is not required)
- Responsible for collecting billing address information
- Always required, but completion can be bypassed with wallet payments
- Different payment methods have different completion and editability rules

### Completion Logic
- Billing step is complete when EITHER of these conditions is met:
  - Valid billing address exists
  - Wallet payment method is selected (wallet payments provide billing information)
- Wallet payments bypass the need for manual billing address entry
- Special cases: Amazon Pay, Google Pay (with feature flag), and PayPal use separate completion logic paths
- Must include completion logic diagram

### Requirement Logic
- Billing step is always required (isRequired: true)
- Cannot be skipped or filtered out

### Payment Method Variations

#### Wallet Payments
- Wallet payments (Apple Pay, Google Pay, PayPal Fastlane, etc.) provide billing information automatically
- When wallet payment is selected: Billing step is complete without address entry
- Editability: Step is NOT editable when using wallet payments (billing data comes from payment provider)

#### Amazon Pay
- Amazon Pay has special completion logic for custom billing address fields (does not follow the general address OR wallet payment rule)
- Completion:
  - If custom fields exist AND billing address exists: Complete if billing address is valid for custom fields
  - If no custom fields exist OR no billing address: Always complete (no validation needed)
- Editability: Editable only if step is complete AND custom fields exist
- If no custom fields exist: Not editable (no fields to edit)
- Amazon Pay renders billing address as static (read-only) when selected

#### Google Pay (with feature flag)
- When feature flag `STRIPE-546.allow_billing_address_editing_for_all_Google_Pay_providers` is enabled: Google Pay uses its own completion logic (does not follow the general address OR wallet payment rule)
- Completion: Valid billing address exists (does not check wallet payment status)
- Editability: Editable if address exists
- Feature flag controls whether this payment method-specific path is used

#### PayPal
- PayPal payment methods (Braintree PayPal, Braintree PayPal Credit, Braintree Venmo, PayPal Commerce, PayPal Commerce Credit, PayPal Commerce Venmo) use their own completion logic (does not follow the general address OR wallet payment rule)
- Completion: Valid billing address exists (does not check wallet payment status)
- Editability: Editable if address exists
- Allows users to modify billing address after completion

#### Default (non-wallet payments)
- Completion: Valid billing address exists
- Editability: Editable if step is complete AND not using wallet payment

### Editability Rules
- Billing step editability varies by payment method:
  - **Wallet payments:** NOT editable (billing data comes from payment provider)
  - **Amazon Pay:** Editable if complete AND has custom fields
  - **Google Pay (with flag):** Editable if address exists
  - **PayPal:** Editable if address exists
  - **Default:** Editable if complete AND not using wallet payment
- General rule: Editable only if step is complete AND not using wallet payment (unless payment method has specific override)

### Step Lifecycle and Actions
- Initialization: Load billing address fields, set step ready state
- User actions: Form submit (updates billing address and customer message if changed)
- Error handling: All errors delegated to parent component

### Data Dependencies and Outputs
- Reads: Checkout object, billing address, billing address fields, configuration, customer, cart
- Writes: Billing address, customer message

### Prerequisites
- Customer step must be complete
- Shipping step must be complete (if required)

### Integration Boundaries
- Payment providers (wallet payments), address validation services, PayPal Fastlane

### Architectural Decisions and Trade-offs
- Payment Method-Specific Completion Logic
- Wallet Payment Bypass
- Amazon Pay Static Address Rendering
- Feature Flag for Google Pay Editability
- PayPal Address-Only Completion

### Evolution and Extension Points
- Adding new payment method variations, changing editability rules, feature flag management, address validation

### Cross-Step Interactions
- Impact on subsequent steps: Payment (billing address required)
- Dependencies: Customer step (email), Shipping step (billing address update)

## 5. Mandatory Diagrams

The document must include at least two (2) Mermaid diagrams:

1. **Completion Logic Flow:** Diagram showing the decision flow for determining Billing step completion (address OR wallet payment).

2. **Editability Decision Tree:** Diagram showing when the Billing step is editable based on payment method and completion status.

Optional but recommended:
3. **Payment Method Variations:** Diagram showing how different payment methods affect completion and editability.
4. **Sequence Diagram:** Payment Method Detection and Form Rendering Flow showing interaction between user, step, checkout state, and form.

## 6. Prohibited Content

- Implementation details (React hooks, component structure, file paths)
- Code examples or pseudo-code
- UI rendering details
- Form validation specifics (covered in forms architecture document)
- Error handling specifics (covered in error handling architecture document)
- Specific payment provider implementation details
- Redundant explanations that restate what's already clear (e.g., "Why these rules", "Edit behavior")
- Obvious statements (e.g., "This prevents users from...", "Step state is preserved during error recovery")
- Implementation details disguised as architecture (e.g., "Prevents memory leaks")
- Speculative "Future:" statements or performance considerations
- Redundant "Completion logic:" sections that just restate the formula

## 7. Verification Criteria

The document is complete when:
- ✅ Developer understands what makes Billing step complete
- ✅ Developer understands when Billing step is required
- ✅ Developer understands how wallet payments affect completion
- ✅ Developer understands payment method-specific variations (Amazon Pay, Google Pay, PayPal)
- ✅ Developer understands that Amazon Pay, Google Pay (with flag), and PayPal use separate completion logic paths that bypass the general wallet payment check
- ✅ Developer understands Amazon Pay static address rendering behavior
- ✅ Developer understands editability rules for Billing step
- ✅ Developer understands step lifecycle, actions, and error handling
- ✅ Developer understands data dependencies and integration boundaries
- ✅ Developer understands architectural decisions and trade-offs
- ✅ Developer understands cross-step interactions
- ✅ No implementation-specific terminology
- ✅ No redundant explanations or obvious statements
- ✅ No speculative "Future:" statements
- ✅ Includes at least 2 diagrams showing completion logic and editability rules

## 8. Maintenance Note

**IMPORTANT:** This spec must be updated whenever the corresponding document (`03c-the-billing-step.md`) is modified. The spec serves as the contract for the document's content. When updating the document:

1. Review the document changes against this spec
2. Update the spec to reflect any new content, removed content, or changed requirements
3. Ensure all mandatory content sections in the spec match what's in the document
4. Update verification criteria if new understanding goals are added

This ensures the spec remains an accurate blueprint for the document's intended content.

