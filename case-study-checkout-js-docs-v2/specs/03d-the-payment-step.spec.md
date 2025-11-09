---
**Title:** Spec for The Payment Step
**Purpose:** To define the structure and content for `03d-the-payment-step.md`, ensuring it provides a comprehensive architectural blueprint of the Payment step's business rules, completion logic, and behavior.
---

# Spec for The Payment Step

## 1. Objective

This document must provide a comprehensive architectural blueprint of the **Payment step**—its business rules, completion logic, requirement conditions, and editability rules.

## 2. Rationale

The step progression rules document (`02a-the-step-progression-rules.md`) explains the general pattern for all steps, but does not detail the specific business rules and logic for the Payment step. This document fills that gap by providing a deep dive into Payment step specifics.

## 3. Core Components (Must be included)

The document must be structured with the following top-level sections:

1. **Payment Step Overview:** The role of the Payment step in the checkout flow and its primary responsibilities.

2. **Completion Logic:** Detailed explanation of what makes the Payment step complete (order completion status).

3. **Requirement Logic:** When the Payment step is required (always required).

4. **Editability Rules:** When and why the Payment step can be edited, including global constraints.

5. **Step Lifecycle and Actions:** Initialization, cleanup, user actions, error handling.

6. **Data Dependencies and Outputs:** What data the step reads and writes.

7. **Prerequisites:** What must be complete before this step.

8. **Integration Boundaries:** External systems integrated.

9. **Architectural Decisions and Trade-offs:** Key design decisions with rationale.

10. **Evolution and Extension Points:** How the step can be extended.

11. **Cross-Step Interactions:** How this step affects and is affected by other steps.

## 4. Mandatory Content

### Payment Step Overview
- Occurs after Billing step
- Responsible for payment method selection and order submission
- Always required for all checkout flows
- Final step in checkout flow before order confirmation
- Completion is determined by order completion status

### Completion Logic
- Payment step is complete when: order is complete
- Completion is determined by the order object's completion status
- If order does not exist: step is incomplete
- Must include completion logic diagram

### Requirement Logic
- Payment step is always required (isRequired: true)
- Cannot be skipped or filtered out
- Required for all checkout flows regardless of payment method

### Editability Rules
- Payment step is editable only when ALL of these conditions are met:
  - Step is complete (order is complete)
  - All previous required steps are complete (Customer, Shipping if required, Billing)
  - Order is not being submitted
- Editability formula: Editable if complete AND all previous required steps complete AND order not being submitted
- Step-level editability: Payment step itself is editable if order is complete
- Global constraints applied at checkout orchestration level:
  - Previous steps must be complete (or not required)
  - Order submission must not be in progress
  - These constraints apply to all steps, not just Payment step

### Step Lifecycle and Actions
- Initialization: Apply store credit, load payment methods, finalize order, subscribe to cart total changes, set up beforeunload handler
- Cleanup: Unsubscribe from cart total changes, remove beforeunload event listener
- User actions: Payment method selection, order submission, store credit toggle, error modal interaction
- Payment method management: Submit functions, validation schemas, submit button state
- Error handling: Payment method invalid, cart changed, order submission errors, finalization errors, spam protection errors, provider errors

### Data Dependencies and Outputs
- Reads: Order object, checkout object, customer, cart, payment methods, consignments, payment provider customer, errors
- Writes: Order, store credit, payment method selection, order finalization, checkout reload

### Prerequisites
- Customer step must be complete
- Shipping step must be complete (if required)
- Billing step must be complete
- Order must not be complete

### Integration Boundaries
- Payment processors, order service, payment method providers, analytics service, store credit service

### Architectural Decisions and Trade-offs
- Order Completion as Step Completion
- Payment Method Registration Pattern
- Cart Total Change Subscription
- Store Credit Auto-Application
- Global Editability Constraints
- Error Recovery via Checkout Reload

### Evolution and Extension Points
- Adding new payment methods, changing order submission flow, payment method validation, error recovery strategies

### Cross-Step Interactions
- Impact on subsequent steps: None (final step)
- Dependencies: Customer step (identification, auth), Shipping step (address, multi-shipping mode), Billing step (billing address)

## 5. Mandatory Diagrams

The document must include at least two (2) Mermaid diagrams:

1. **Completion Logic Flow:** Diagram showing the decision flow for determining Payment step completion (order completion status).

2. **Editability Decision Tree:** Diagram showing when the Payment step is editable (complete AND all previous steps complete AND order not being submitted).

Optional but recommended:
3. **Sequence Diagram:** Order Submission Flow showing interaction between user, step, payment method, service, and analytics.

## 6. Prohibited Content

- Implementation details (React hooks, component structure, file paths)
- Code examples or pseudo-code
- UI rendering details
- Payment processing specifics (covered in payment architecture document)
- Error handling specifics (covered in error handling architecture document)
- Order submission process details (covered in order submission architecture document)
- Redundant explanations that restate what's already clear (e.g., "Why these rules", "Edit behavior")
- Obvious statements (e.g., "This prevents users from...", "Step state is preserved during error recovery")
- Implementation details disguised as architecture (e.g., "Prevents memory leaks")
- Speculative "Future:" statements or performance considerations
- Redundant "Completion logic:" sections that just restate the formula

## 7. Verification Criteria

The document is complete when:
- ✅ Developer understands what makes Payment step complete (order completion status)
- ✅ Developer understands when Payment step is required (always required)
- ✅ Developer understands that completion checks if order exists before checking isComplete
- ✅ Developer understands editability rules for Payment step (complete AND previous steps complete AND order not being submitted)
- ✅ Developer understands that editability constraints are enforced at checkout orchestration level
- ✅ Developer understands global constraints (previous steps, order submission) affect all steps
- ✅ Developer understands step lifecycle, actions, and error handling
- ✅ Developer understands data dependencies and integration boundaries
- ✅ Developer understands architectural decisions and trade-offs
- ✅ Developer understands cross-step interactions
- ✅ No implementation-specific terminology
- ✅ No redundant explanations or obvious statements
- ✅ No speculative "Future:" statements
- ✅ Includes at least 2 diagrams showing completion logic and editability rules

## 8. Maintenance Note

**IMPORTANT:** This spec must be updated whenever the corresponding document (`03d-the-payment-step.md`) is modified. The spec serves as the contract for the document's content. When updating the document:

1. Review the document changes against this spec
2. Update the spec to reflect any new content, removed content, or changed requirements
3. Ensure all mandatory content sections in the spec match what's in the document
4. Update verification criteria if new understanding goals are added

This ensures the spec remains an accurate blueprint for the document's intended content.

