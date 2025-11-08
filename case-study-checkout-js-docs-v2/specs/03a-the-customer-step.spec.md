---
**Title:** Spec for The Customer Step
**Purpose:** To define the structure and content for `03a-the-customer-step.md`, ensuring it provides a comprehensive architectural blueprint of the Customer step's business rules, logic, and behavior.
---

# Spec for The Customer Step

## 1. Objective

This document must provide a comprehensive architectural blueprint of the **Customer step**—its business rules, completion logic, view types, and integration patterns. It must explain how the Customer step determines completion, what view types it supports, and how it integrates with wallet payments and customer strategies.

## 2. Rationale

The step progression rules document (`02a-the-step-progression-rules.md`) explains the general pattern for all steps, but does not detail the specific business rules and logic for the Customer step. This document fills that gap by providing a deep dive into Customer step specifics.

## 3. Core Components (Must be included)

The document must be structured with the following top-level sections:

1. **Customer Step Overview:** The role of the Customer step in the checkout flow and its primary responsibilities.

2. **Completion Logic:** Detailed explanation of what makes the Customer step complete, including email requirements, wallet payment integration, and special cases (e.g., Stripe Link).

3. **View Types:** The different views the Customer step can display (Guest, Login, Create Account) and when each is shown.

4. **Wallet Payment Integration:** How wallet payments (Apple Pay, Google Pay, PayPal, etc.) affect Customer step completion and editability.

5. **Customer Strategies:** How customer strategies (Stripe Link, Bolt, etc.) integrate with the Customer step and affect its behavior.

6. **Editability Rules:** When and why the Customer step can be edited, including guest vs. authenticated user differences.

7. **Step Lifecycle and Actions:** Initialization, cleanup, user actions, business rule actions, error handling.

8. **Data Dependencies and Outputs:** What data the step reads and writes.

9. **Prerequisites:** What must be complete before this step.

10. **Integration Boundaries:** External systems integrated.

11. **Architectural Decisions and Trade-offs:** Key design decisions with rationale.

12. **Evolution and Extension Points:** How the step can be extended.

13. **Cross-Step Interactions:** How this step affects and is affected by other steps.

## 4. Mandatory Content

### Customer Step Overview
- First step in checkout flow for guest users
- Responsible for customer identification (email collection or authentication)
- Prerequisite for all other steps
- Always required

### Completion Logic
- Customer step is complete if: email exists (in customer object OR billing address) OR wallet payment is selected
- Email can come from: customer.email or billingAddress.email
- Wallet payments auto-complete the step (no email required)
- Special case - Stripe Link reload: When checkout page is reloaded and Stripe Link is being used (under specific conditions: not using wallet, StripeUPE provider, has email, guest user, meets minimum amount), completion is determined by Stripe Link authentication state being available
- Must include completion logic diagram

### View Types
- **Guest:** For users checking out without creating an account
- **Login:** For existing customers to sign in with email and password
- **SuggestedLogin:** Prompts user to log in but allows continuing as guest
- **EnforcedLogin:** Requires user to log in (cannot continue as guest)
- **CancellableEnforcedLogin:** Requires login but can be cancelled in certain conditions
- **CreateAccount:** For new customers to create an account during checkout
- View type determination:
  - Initial view: Based on authentication status and guest checkout configuration
  - User-initiated transitions: Triggered by user actions (clicking "Sign in", "Create account", "Continue as guest")
  - Business rule transitions:
    - SuggestedLogin: Triggered when customer has `shouldEncourageSignIn` flag set after continuing as guest (unless Stripe Link is authenticated)
    - EnforcedLogin: Triggered by error status 429 (rate limiting) when continuing as guest
    - CancellableEnforcedLogin: Triggered by error status 403 (forbidden) when continuing as guest

### Wallet Payment Integration
- Wallet payments (including Apple Pay, Google Pay variants, PayPal Commerce, Braintree PayPal, Amazon Pay, Bolt, Stripe Link, and other supported methods) auto-complete Customer step
- When wallet is used: step becomes non-editable
- Wallet button visibility: Wallet buttons are only displayed when all conditions are met:
  - Payment data is required for checkout
  - Customer is a guest user (not authenticated)
  - Supported wallet payment methods are available
- Wallet buttons can appear at top or bottom of step (configurable)
- Wallet selection triggers customer strategy initialization

### Customer Strategies
- Customer strategies are payment-provider-specific integrations that can automatically provide customer data
- Supported strategies:
  - Stripe Link V2 (StripeUPE)
  - Stripe UPE Customer Strategy
  - Bolt
  - BigCommerce Payments Fastlane
  - Braintree Fastlane
  - PayPal Commerce Fastlane
- Strategy initialization:
  - Strategies are initialized when a payment provider with custom checkout is configured
  - Initialization happens when the Customer step is displayed
  - Exception: StripeUPE provider is handled separately and does not trigger standard strategy initialization
- Payment method checkout execution:
  - When continuing as guest, if a payment provider with custom checkout is configured (except StripeUPE), the system executes payment method checkout before proceeding
  - This allows wallet payments to complete customer identification during the guest flow
  - StripeUPE follows a different flow and does not execute payment method checkout at this stage
- Special handling for Stripe Link reload scenario

### Editability Rules
- Customer step is editable only if: (1) step is complete, (2) user is guest, (3) NOT using wallet payment
- Editability is computed based on: isComplete && !isUsingWallet && isGuest

### Step Lifecycle and Actions
- Initialization: Customer strategies initialization, step ready state
- Cleanup: Customer strategies deinitialization
- User actions: Sign in, continue as guest, create account, send login email, email entry, view type changes
- Business rule actions: SuggestedLogin transition, EnforcedLogin transition, CancellableEnforcedLogin transition, payment method checkout execution
- Error handling: Empty cart, update subscriptions, payment method client invalid, rate limiting (429), forbidden (403), other errors

### Data Dependencies and Outputs
- Reads: Customer object, billing address, checkout object, configuration, cart, payment provider customer, sign-in email, errors
- Writes: Customer data, authentication, account creation, login email, payment provider customer, payment method checkout

### Prerequisites
- No prerequisites (first step)

### Integration Boundaries
- Wallet payment providers, customer strategies, authentication service, analytics service

### Architectural Decisions and Trade-offs
- View Type State Machine Pattern
- Customer Strategy Pattern
- Wallet Payment Auto-Completion
- Stripe Link Special Handling
- Error-Driven View Transitions

### Evolution and Extension Points
- Adding new view types, customer strategies, wallet payments
- Changing completion logic

### Cross-Step Interactions
- Impact on subsequent steps: Shipping (email), Billing (email), Payment (auth status)
- Dependencies: None (first step)

## 5. Prohibited Content

- Implementation details (React hooks, component structure, file paths)
- Code examples or pseudo-code
- UI rendering details
- Form validation specifics (covered in forms architecture document)
- Error handling specifics (covered in error handling architecture document)
- Redundant explanations that restate what's already clear (e.g., "Why these rules", "Edit behavior")
- Obvious statements (e.g., "This prevents users from...", "This preserves UX...")
- Implementation details disguised as architecture (e.g., "Prevents memory leaks")
- Speculative "Future:" statements or performance considerations
- Redundant "Completion logic:" sections that just restate the formula

## 6. Mandatory Diagrams

The document must include at least two (2) Mermaid diagrams:

1. **Completion Logic Flow:** Diagram showing the decision flow for determining Customer step completion (email OR wallet payment, with Stripe Link special case).

2. **Editability Decision Tree:** Diagram showing when the Customer step is editable (complete + guest + not using wallet).

Optional but recommended:
3. **View Type Transitions:** Diagram showing how view types transition based on user actions and context.
4. **Sequence Diagram:** Continue as Guest Flow showing interaction between user, step, service, and payment provider.

## 7. Verification Criteria

The document is complete when:
- ✅ Developer understands what makes Customer step complete
- ✅ Developer understands all view types (Guest, Login, SuggestedLogin, EnforcedLogin, CancellableEnforcedLogin, CreateAccount) and when each is used
- ✅ Developer understands view type transitions (user-initiated and business rule/error-driven)
- ✅ Developer understands wallet button visibility conditions
- ✅ Developer understands how wallet payments affect Customer step
- ✅ Developer understands customer strategy integration and StripeUPE exception
- ✅ Developer understands payment method checkout execution flow
- ✅ Developer understands editability rules for Customer step
- ✅ Developer understands step lifecycle, actions, and error handling
- ✅ Developer understands data dependencies and integration boundaries
- ✅ Developer understands architectural decisions and trade-offs
- ✅ Developer understands cross-step interactions
- ✅ No implementation-specific terminology
- ✅ No redundant explanations or obvious statements
- ✅ No speculative "Future:" statements
- ✅ Includes at least 2 diagrams showing completion logic and editability rules
- ✅ Optional: View type transitions diagram, sequence diagrams

## 8. Maintenance Note

**IMPORTANT:** This spec must be updated whenever the corresponding document (`03a-the-customer-step.md`) is modified. The spec serves as the contract for the document's content. When updating the document:

1. Review the document changes against this spec
2. Update the spec to reflect any new content, removed content, or changed requirements
3. Ensure all mandatory content sections in the spec match what's in the document
4. Update verification criteria if new understanding goals are added

This ensures the spec remains an accurate blueprint for the document's intended content.

