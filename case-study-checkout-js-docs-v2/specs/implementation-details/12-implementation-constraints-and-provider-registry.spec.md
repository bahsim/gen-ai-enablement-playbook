---
**Title:** Spec for Implementation Constraints Registry
**Purpose:** To define the structure and content for a universal constraint-focused registry that captures actual implementation realities across ALL areas (components, integrations, styling, logic, data, services) to narrow focus and eliminate unnecessary cases for ANY change task.
**Audience:** All Developers, Architects, AI Agents
---

# Spec for Implementation Constraints Registry

## 1. Objective

This document must provide a **universal constraint-focused architectural registry** that captures actual implementation realities across ALL areas of the codebase:
- Actual components implemented (not theoretical possibilities)
- Actual integrations registered (payment providers, shipping providers, address validators, etc.)
- Actual services and APIs used
- Actual data structures and state management patterns
- Actual styling system constraints and override points
- Actual business logic patterns and constraints
- Actual configuration options available
- Explicit elimination of non-existent implementations

The document serves as a **universal constraint filter** that eliminates unnecessary exploration for ANY change task (styling, logic, data flow, integrations, components, etc.) and focuses attention on actual implementation realities.

## 2. Rationale

Generic documentation that describes "all possible" implementations creates unnecessary cognitive load and leads to:
- AI considering non-existent components, integrations, or patterns
- Attempts to modify code that doesn't exist
- Exploration of code paths that aren't used
- Suggestions for features/patterns not in the system
- Wasted effort on eliminated cases

This document provides **non-obvious information** that narrows focus to actual implementation, enabling precise, constraint-aware changes for ANY task type (styling changes, logic changes, data flow changes, integration changes, component changes, etc.).

## 3. Core Components (Must be included)

The document must be structured with the following top-level sections:

1. **Component Registry:** Actual components implemented, their file paths, and relationships
2. **Integration Registry:** Actual integrations (payment providers, shipping providers, address validators, analytics, etc.) with registration IDs and mappings
3. **Service and API Registry:** Actual services, APIs, and external systems integrated
4. **Styling System Constraints:** Design system hierarchy, override points, component-to-style mappings
5. **Logic Constraints:** State management patterns, data flow patterns, business rule implementations
6. **Data Constraints:** Actual data structures, state shapes, API response formats
7. **Configuration Constraints:** Actual configuration options available
8. **Eliminated Cases:** Explicit lists of what's NOT implemented across all areas

## 4. Mandatory Content

### Component Registry

**MUST include:**
- **Actual components implemented:** List only components that exist in the codebase
  - Format: `ComponentName → File Path → Parent Component → Child Components`
  - Include all step components, form components, UI components, utility components
- **Component relationships:** Parent-child hierarchies, composition patterns
- **Component types:** Class components vs functional components, HOCs, hooks
- **Component grouping:** Which components share patterns, which are unique
- **Component file locations:** Exact paths organized by package/domain

**MUST NOT include:**
- Theoretical components that could be added
- Components mentioned in comments but not implemented
- Generic descriptions like "various components"

### Integration Registry

**MUST include:**
- **Payment providers:** Actual payment providers with registration IDs and component mappings
- **Shipping providers:** Actual shipping providers integrated
- **Address validators:** Actual address validation services used
- **Analytics services:** Actual analytics integrations
- **Third-party SDKs:** Actual SDKs integrated (Stripe, PayPal, etc.)
- **External APIs:** Actual external APIs called
- **Integration patterns:** How each integration is registered and used
- **Integration availability:** Conditional availability rules for each integration

**MUST NOT include:**
- Theoretical integrations that could be added
- Integrations mentioned but not implemented
- Generic descriptions like "various integrations"

### Service and API Registry

**MUST include:**
- **Checkout service:** Actual checkout service methods and their purposes
- **Order service:** Actual order service methods
- **Payment service:** Actual payment processing services
- **Shipping service:** Actual shipping calculation services
- **Address service:** Actual address validation/formatting services
- **API endpoints:** Actual API endpoints used (not theoretical)
- **Service patterns:** How services are structured and used

### Styling System Constraints

**MUST include:**
- **Design system hierarchy:** 
  - Level 1: Citadel design system variables (exact file paths)
  - Level 2: Application-level overrides (exact file paths)
  - Level 3: Component-level styles (exact file paths)
  - Level 4: Integration-specific overrides (if any)
- **Override rules:** What can be overridden at each level
- **Variable naming convention:** Exact variable names for all styling aspects
- **!default flag usage:** Which variables use !default and can be overridden
- **Component isolation:** Which components have isolated styles vs. inherit from design system
- **Component-to-style mapping:** Direct mapping from component files to SCSS files and CSS selectors

### Logic Constraints

**MUST include:**
- **State management pattern:** Actual pattern used (BigCommerce Checkout SDK, not Redux)
- **State access patterns:** How components access state (HOCs, hooks, context)
- **Data flow patterns:** How data flows through the system
- **Business rule locations:** Where business rules are implemented (components, services, utilities)
- **Validation patterns:** How validation is implemented (Formik, Yup, custom)
- **Error handling patterns:** How errors are handled and where
- **Side effect patterns:** How side effects are managed (useEffect, subscriptions, etc.)

### Data Constraints

**MUST include:**
- **Checkout state shape:** Actual structure of checkout state object
- **Component prop interfaces:** Actual prop types for major components
- **API response formats:** Actual response structures from APIs
- **Form data structures:** Actual form value shapes
- **Error data structures:** Actual error object shapes
- **Configuration data:** Actual configuration object structure

### Configuration Constraints

**MUST include:**
- **Actual configuration options:** Only options that exist in the system
- **Configuration file locations:** Where configuration is defined
- **Feature flags:** Actual feature flags available
- **Environment variables:** Actual environment variables used
- **Configuration access patterns:** How configuration is accessed in code

### Eliminated Cases

**MUST include:**
- **Components NOT implemented:** Explicit list of common components that don't exist
- **Integrations NOT implemented:** Explicit list of common integrations not in the system
- **Patterns NOT used:** Explicit list of common patterns not used (e.g., Redux, Context API for state)
- **Features NOT available:** Explicit list of common features not implemented
- **Services NOT used:** Explicit list of common services not in the system
- **Data structures NOT used:** Explicit list of common data patterns not implemented

**Format:**
```
NOT IMPLEMENTED:
Components:
- Redux store - uses BigCommerce Checkout SDK instead
- Context API for state - uses withCheckout HOC instead

Integrations:
- Stripe (direct integration) - uses hosted-credit-card instead
- PayPal Express - not configured
- Apple Pay - not enabled

Patterns:
- Redux state management - not used
- React Context for checkout state - not used
```

## 5. Prohibited Content

**MUST NOT include:**
- Generic descriptions like "various components", "various integrations", "various services"
- Theoretical implementations that could be added
- "All possible" language - only actual implementations
- Implementation details that don't affect constraints (e.g., detailed business logic explanations)
- Step-by-step change instructions (that's for task-specific guides)
- Redundant information already in other architectural docs
- Obvious information that doesn't narrow focus
- Examples of how to use components (that's for usage docs)

## 6. Mandatory Tables/Structures

### Component Registry Table
| Component Name | File Path | Type | Parent Component | Child Components | SCSS File |
|---------------|-----------|------|------------------|------------------|-----------|
| Customer | packages/core/src/app/customer/Customer.tsx | Class | CheckoutStep | GuestForm, LoginForm | Customer.scss |
| ... | ... | ... | ... | ... | ... |

### Integration Registry Table
| Integration Type | Provider Name | Registration ID | Component File | Uses Generic Component? | Custom Implementation? |
|-----------------|---------------|-----------------|----------------|-------------------------|------------------------|
| Payment | Cybersource | "hosted-credit-card" | packages/hosted-credit-card-integration/src/HostedCreditCardPaymentMethod.tsx | Yes | No |
| Shipping | ... | ... | ... | ... | ... |
| Address | ... | ... | ... | ... | ... |

### Component-to-Style Mapping Table
| Component File | SCSS File | Main CSS Class | Key Selectors | Design System Variables |
|---------------|-----------|----------------|---------------|------------------------|
| Customer.tsx | Customer.scss | .customer | .customer-form, .customer-input | $input-background-color |
| ... | ... | ... | ... | ... |

### Service Registry Table
| Service Name | File Path | Key Methods | Used By Components |
|-------------|-----------|-------------|-------------------|
| CheckoutService | packages/core/src/app/checkout/CheckoutService.ts | loadCheckout, submitOrder | Payment, Shipping |
| ... | ... | ... | ... |

### State Management Pattern Table
| State Type | Access Pattern | File/Pattern Location | Used By |
|-----------|----------------|------------------------|---------|
| Checkout State | withCheckout HOC | packages/core/src/app/checkout/withCheckout.tsx | All step components |
| Form State | Formik | Formik library | All form components |
| Local State | useState/useReducer | Component files | Various |

### Styling Hierarchy Diagram
```
Citadel Variables (_citadel-variables.scss)
  └─> Application Overrides (_variables.scss)
      └─> Component Styles (component.scss)
          └─> Integration Overrides (if any)
```

## 7. Verification Criteria

The document is complete when:
- [ ] Every major component in the codebase is listed with exact file paths
- [ ] Every integration (payment, shipping, address, analytics) is listed with registration IDs
- [ ] Every service is listed with key methods and usage
- [ ] Every component has SCSS file mapping (if it has styles)
- [ ] Design system variable names are exact (not descriptions)
- [ ] State management patterns are explicitly documented
- [ ] Eliminated cases section explicitly lists what's NOT implemented across all areas
- [ ] No generic "various" or "all possible" language exists
- [ ] All information narrows focus rather than expands possibilities
- [ ] A developer can identify exact files to modify for ANY type of change (styling, logic, data, integration)
- [ ] An AI agent can eliminate non-existent components, integrations, patterns, and services from consideration
- [ ] The document covers ALL areas: components, integrations, services, styling, logic, data, configuration

## 8. Maintenance Note

**This document must be updated when:**
- A new component is added to the system
- A component is removed or refactored
- A new integration is added (payment, shipping, address, analytics)
- An integration is removed
- A new service is added
- Service methods change
- Component file paths change
- CSS class names change
- SCSS file locations change
- Design system variable structure changes
- Styling override patterns change
- State management patterns change
- Data structures change
- Configuration options change

**Update process:**
1. Verify actual components in codebase (grep for component files, check imports)
2. Verify actual integrations (grep for registration IDs, check integration packages)
3. Verify actual services (grep for service files, check service usage)
4. Verify component file paths exist
5. Verify SCSS file paths exist
6. Verify design system variable names match actual variables
7. Update eliminated cases if components/integrations/patterns are removed
8. Verify state management patterns match actual implementation
9. Verify data structures match actual code

---

**Critical Principle:** This document exists to **constrain and narrow**, not to expand possibilities. Every piece of information should eliminate unnecessary exploration and focus attention on actual implementation realities. It must cover ALL areas of the codebase (components, integrations, services, styling, logic, data, configuration) to serve as a universal constraint filter for ANY change task, not just specific areas like payment providers.

