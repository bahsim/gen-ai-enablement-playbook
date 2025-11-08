---
**Title:** Spec for The Step Progression Rules
**Purpose:** To define the structure and content for `02a-the-step-progression-rules.md`, ensuring it provides a concise architectural blueprint of how checkout step states are determined, without implementation details.
---

# Spec for The Step Progression Rules

## 1. Objective

This document must provide a concise architectural blueprint of the **patterns** and **principles** that govern checkout step progression. It must explain how the system determines step states to enable flow control, focusing on architectural patterns rather than implementation specifics.

## 2. Rationale

The checkout flow (documented in `02-the-checkout-flow.md`) describes *what* paths users can take, but does not explain *how* the system determines step states. This document fills that gap by providing the architectural model for step state determination.

## 3. Core Components (Must be included)

The document must be structured with the following top-level sections:

1. **The Derived State Pattern:** Step progression is computed from authoritative checkout data, not stored separately. Explains why this ensures consistency and eliminates synchronization issues.

2. **The Business Rules Architecture:** Business rules are centralized and isolated by step type. Explains the principle of isolation to enable independent evolution.

3. **The Conditional Logic Architecture:** Conditional logic (payment methods, user context, cart composition) is co-located with business rules, not scattered in UI.

4. **System Boundaries:** Clear separation between computation (pure, read-only) and rendering (consumes computed information, controls UI).

## 4. Mandatory Content

### The Derived State Pattern
- Step progression is computed from checkout data when needed
- Benefits: eliminates sync issues, enables state restoration, single source of truth
- Computation is pure (no side effects, predictable)

### The Business Rules Architecture
- Rules are centralized in one layer, organized by step type
- Each step's rules are isolated (changes don't affect other steps)
- Must define the four dimensions of step state before listing decisions:
  - Completion: Whether step has sufficient data to be considered finished
  - Requirement: Whether step must be completed for checkout to proceed
  - Editability: Whether a completed step can be modified
  - Activation: Which step is currently the user's focus
- Must state concrete decisions made for each step:
  - Completion: What makes each step complete (e.g., "Customer step complete if email exists OR wallet payment selected")
  - Requirement: When each step is required (e.g., "Shipping required only if cart contains physical goods")
  - Editability: When steps can be edited (e.g., "Step editable only if previous required steps complete")
  - Activation: Which step becomes active (e.g., "First incomplete required step")
- Must include global checkout-level rules that apply to the flow as a whole:
  - Step visibility (only required steps rendered)
  - Empty cart handling
  - Error recovery navigation rules
  - Navigation to next incomplete step

### The Conditional Logic Architecture
- Rules vary by payment method, user context, cart composition, feature flags
- Conditional logic is co-located with rules (not in UI layer)
- Trade-off: more complex rules layer, but maintains separation of concerns

### System Boundaries
- Computation layer: pure, computes from data, no side effects
- Rendering layer: consumes computed information, controls UI
- Unidirectional flow: data → computation → rendering → UI

## 5. Mandatory Diagram

Include one Mermaid diagram showing: Data Layer → Computation Layer → Rendering Layer → UI, with unidirectional flow.

## 6. Prohibited Content

- Implementation details (memoized selectors, Reselect, React hooks, etc.)
- Code examples or pseudo-code
- File paths or module names
- Property names from implementation (use conceptual language instead)

## 7. Verification Criteria

The document is complete when:
- ✅ Developer understands architectural patterns without implementation details
- ✅ Developer understands how rendering uses computed information
- ✅ No implementation-specific terminology
- ✅ Includes diagram showing architectural flow
- ✅ Document is concise (target: ~100 lines)
