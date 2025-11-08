---
**Title:** Spec for The Shipping Step
**Purpose:** To define the structure and content for `03b-the-shipping-step.md`, ensuring it provides a comprehensive architectural blueprint of the Shipping step's business rules, completion logic, and behavior.
---

# Spec for The Shipping Step

## 1. Objective

This document must provide a comprehensive architectural blueprint of the **Shipping step**—its business rules, completion logic, requirement conditions, and special handling for multi-shipping and custom shipping options.

## 2. Rationale

The step progression rules document (`02a-the-step-progression-rules.md`) explains the general pattern for all steps, but does not detail the specific business rules and logic for the Shipping step. This document fills that gap by providing a deep dive into Shipping step specifics.

## 3. Core Components (Must be included)

The document must be structured with the following top-level sections:

1. **Shipping Step Overview:** The role of the Shipping step in the checkout flow and its primary responsibilities.

2. **Completion Logic:** Detailed explanation of what makes the Shipping step complete (address, shipping options, item assignment).

3. **Requirement Logic:** When the Shipping step is required (only for physical goods that need shipping).

4. **Multi-Shipping Mode:** How multi-shipping mode affects Shipping step completion and behavior.

5. **Promotional Items Conflict:** How promotional items conflict with multi-shipping mode and conflict resolution.

6. **Custom Shipping Options:** Special handling for custom shipping options and how they affect editability.

7. **Editability Rules:** When and why the Shipping step can be edited, including custom shipping restrictions.

8. **Step Lifecycle and Actions:** Initialization, user actions, error handling.

9. **Data Dependencies and Outputs:** What data the step reads and writes.

10. **Prerequisites:** What must be complete before this step.

11. **Integration Boundaries:** External systems integrated.

12. **Architectural Decisions and Trade-offs:** Key design decisions with rationale.

13. **Evolution and Extension Points:** How the step can be extended.

14. **Cross-Step Interactions:** How this step affects and is affected by other steps.

## 4. Mandatory Content

### Shipping Step Overview
- Occurs after Customer step
- Responsible for collecting shipping address and shipping method selection
- Required only for physical goods that need shipping
- Supports both single-address and multi-address shipping

### Completion Logic
- Shipping step is complete when ALL of these conditions are met:
  - Valid shipping address exists
  - Shipping options are selected for all consignments
  - All cart items are assigned to consignments (no unassigned items)
- Item assignment check: Only counts physical items that are NOT added by promotion (promotional items are excluded from assignment requirements)
- Shipping options check: Verifies all consignments have selected options (custom shipping options are always considered valid)
- Must include completion logic diagram

### Requirement Logic
- Shipping step is required only if cart contains items that need shipping
- Items that require shipping:
  - Physical items with `isShippingRequired` flag set to true
  - Custom items (if custom items exist in cart)
- Digital goods only: Shipping step is not required (skipped)
- Requirement is determined by cart composition and configuration

### Multi-Shipping Mode
- Multi-shipping allows assigning different items to different shipping addresses
- Multi-shipping visibility conditions: Multi-shipping option is shown when ALL of these are met:
  - Multi-shipping feature is enabled in configuration
  - No payment method with custom checkout is configured
  - Cart has more than one shippable item
- Multi-shipping detection: System detects multi-shipping mode when:
  - More than one consignment exists, OR
  - Consignments have items assigned AND there are unassigned items
- In multi-shipping mode: each consignment must have address and shipping option
- Completion requires: all consignments have addresses AND all have shipping options selected AND all items assigned
- Multi-shipping mode affects UI and completion logic but not the fundamental completion criteria

### Promotional Items Conflict
- Promotional items conflict with multi-shipping mode
- When promotional items exist AND multi-shipping mode is active:
  - System shows a modal indicating multi-shipping is unavailable
  - Forces switch to single shipping mode
  - Prevents user from proceeding with multi-shipping when promotional items are present
- This is a business rule to maintain promotional item benefits and prevent conflicts

### Custom Shipping Options
- Custom shipping options are special shipping methods (e.g., manual order processing)
- When custom shipping is selected: Shipping step becomes non-editable
- This prevents users from modifying shipping information after custom shipping is selected
- Custom shipping is determined by feature flag and shipping option type

### Editability Rules
- Shipping step is editable only when ALL of these conditions are met:
  - Step is complete
  - Step is required
  - Custom shipping option is NOT selected
- Editability formula: Editable if complete AND required AND not custom shipping

### Step Lifecycle and Actions
- Initialization: Load shipping address fields, shipping options, billing address fields, detect promotional items conflict
- User actions: Single shipping submit, multi shipping submit, multi-shipping mode switch, promotional items conflict resolution
- Special rendering: Stripe shipping form when StripeUPE configured
- Error handling: All errors delegated to parent component

### Data Dependencies and Outputs
- Reads: Shipping address, consignments, cart, shipping address fields, billing address, configuration, customer, customer message, countries, payment method
- Writes: Shipping address, consignments, billing address, customer message, item assignments

### Prerequisites
- Customer step must be complete

### Integration Boundaries
- Shipping providers, address validation services, payment providers (remote billing detection), Stripe integration

### Architectural Decisions and Trade-offs
- Multi-Shipping as Mode, Not Separate Component
- Promotional Items Conflict Resolution
- Custom Shipping Locks Editability
- Billing Address Update from Shipping
- Consignment-Based Multi-Shipping

### Evolution and Extension Points
- Adding new shipping modes, changing item assignment rules, custom shipping options, shipping option validation

### Cross-Step Interactions
- Impact on subsequent steps: Billing (address updates), Payment (multi-shipping filtering)
- Dependencies: Customer step (email)

## 5. Mandatory Diagrams

The document must include at least two (2) Mermaid diagrams:

1. **Completion Logic Flow:** Diagram showing the decision flow for determining Shipping step completion (address AND options AND no unassigned items).

2. **Editability Decision Tree:** Diagram showing when the Shipping step is editable (complete AND required AND not custom shipping).

Optional but recommended:
3. **Multi-Shipping Flow:** Diagram showing how multi-shipping mode affects completion requirements.
4. **Sequence Diagram:** Multi-Shipping Mode Switch Flow showing interaction between user, step, service, and form.

## 6. Prohibited Content

- Implementation details (React hooks, component structure, file paths)
- Code examples or pseudo-code
- UI rendering details
- Form validation specifics (covered in forms architecture document)
- Error handling specifics (covered in error handling architecture document)
- Shipping option expiration handling (covered in error recovery rules)
- Redundant explanations that restate what's already clear (e.g., "Why these rules", "Edit behavior")
- Obvious statements (e.g., "This prevents users from...", "Step state is preserved during error recovery")
- Implementation details disguised as architecture (e.g., "Prevents memory leaks")
- Speculative "Future:" statements or performance considerations
- Redundant "Completion logic:" sections that just restate the formula

## 7. Verification Criteria

The document is complete when:
- ✅ Developer understands what makes Shipping step complete
- ✅ Developer understands when Shipping step is required (physical items with shipping required OR custom items)
- ✅ Developer understands item assignment requirements (promotional items excluded)
- ✅ Developer understands multi-shipping visibility conditions
- ✅ Developer understands multi-shipping detection logic
- ✅ Developer understands promotional items conflict with multi-shipping
- ✅ Developer understands how multi-shipping affects completion
- ✅ Developer understands custom shipping option restrictions
- ✅ Developer understands editability rules for Shipping step
- ✅ Developer understands step lifecycle, actions, and error handling
- ✅ Developer understands data dependencies and integration boundaries
- ✅ Developer understands architectural decisions and trade-offs
- ✅ Developer understands cross-step interactions
- ✅ No implementation-specific terminology
- ✅ No redundant explanations or obvious statements
- ✅ No speculative "Future:" statements
- ✅ Includes at least 2 diagrams showing completion logic and editability rules

## 8. Maintenance Note

**IMPORTANT:** This spec must be updated whenever the corresponding document (`03b-the-shipping-step.md`) is modified. The spec serves as the contract for the document's content. When updating the document:

1. Review the document changes against this spec
2. Update the spec to reflect any new content, removed content, or changed requirements
3. Ensure all mandatory content sections in the spec match what's in the document
4. Update verification criteria if new understanding goals are added

This ensures the spec remains an accurate blueprint for the document's intended content.

