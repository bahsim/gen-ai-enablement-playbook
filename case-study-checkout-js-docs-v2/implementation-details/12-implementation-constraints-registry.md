---
**Title:** Implementation Constraints Registry
**Purpose:** Universal constraint-focused registry capturing actual implementation realities across ALL areas (components, integrations, styling, logic, data, services) to narrow focus and eliminate unnecessary cases for ANY change task.
**Audience:** All Developers, Architects, AI Agents
**Maintenance:** Update when components, integrations, services, patterns, or data structures change.
---

# Implementation Constraints Registry

This document provides a **universal constraint filter** that eliminates unnecessary exploration for ANY change task (styling, logic, data flow, integrations, components, services) by documenting actual implementation realities, not theoretical possibilities.

**Critical Principle:** This document exists to **constrain and narrow**, not to expand possibilities. Every piece of information eliminates unnecessary exploration and focuses attention on actual implementation realities.

## 1. Component Registry

### Component Registry Table

| Component Name | File Path | Type | Parent Component | Child Components | SCSS File |
|---------------|-----------|------|------------------|------------------|-----------|
| Customer | packages/core/src/app/customer/Customer.tsx | (type not verified) | (parent not verified) | (child components not verified) | (SCSS file not verified) |
| Shipping | packages/core/src/app/shipping/Shipping.tsx | (type not verified) | (parent not verified) | (child components not verified) | (SCSS file not verified) |
| Billing | packages/core/src/app/billing/Billing.tsx | (type not verified) | (parent not verified) | (child components not verified) | (SCSS file not verified) |
| Payment | packages/core/src/app/payment/Payment.tsx | (type not verified) | (parent not verified) | (child components not verified) | (SCSS file not verified) |
| HostedCreditCardPaymentMethod (Internal) | packages/core/src/app/payment/paymentMethod/HostedCreditCardPaymentMethod.tsx | Class | (parent not verified) | (child components not verified) | (SCSS file not verified) |
| HostedCreditCardPaymentMethod (Public) | packages/hosted-credit-card-integration/src/HostedCreditCardPaymentMethod.tsx | Functional | (parent not verified) | (child components not verified) | (SCSS file not verified) |

**Component Relationships (Verified):**
- HostedCreditCardPaymentMethod exists in two locations (internal and public) - verified from `04-internal-vs-public-components.md`
- Payment step uses PaymentContext.Provider pattern - verified from Payment step docs
- No Redux components exist - verified (uses BigCommerce Checkout SDK)
- No Context API for checkout state - verified (uses withCheckout HOC)

## 2. Integration Registry

### Integration Registry Table

| Integration Type | Provider Name | Registration ID | Component File | Uses Generic Component? | Custom Implementation? |
|-----------------|---------------|-----------------|----------------|-------------------------|------------------------|
| Payment | Hosted Credit Card | "hosted-credit-card" | packages/hosted-credit-card-integration/src/HostedCreditCardPaymentMethod.tsx | Yes | No (shared) |
| Payment | Stripe | "stripev3" | packages/stripe-integration/src/ (exact file not verified) | No | Yes (custom) |

**Integration Patterns (Verified):**
- Payment methods register via PaymentContext (verified from Payment step docs)
- Payment methods use toResolvableComponent for registration (verified from integration architecture docs)

## 3. Service and API Registry

### Service Registry Table

| Service Name | File Path | Key Methods | Used By Components |
|-------------|-----------|-------------|-------------------|
| CheckoutService | packages/core/src/app/checkout/CheckoutService.ts | (methods not verified - only file path verified) | (used by components not verified) |

**Service Patterns (Verified):**
- Services accessed via checkoutService prop from withCheckout HOC (verified)
- No Redux actions/reducers for service calls (verified - uses BigCommerce Checkout SDK)
- Exact service methods not verified - must be checked in codebase

## 4. Styling System Constraints

### Styling System Constraints (Verified from visual design docs)

**Design System (Verified from visual design docs):**
- Uses Citadel design system
- Component layer: packages/ui and packages/core/src/app/ui
- Application layer: packages/core/src/scss

**Component-to-Style Mapping:**
- Component SCSS files co-located with component files (verified pattern)
- Exact CSS class names and SCSS file paths not verified - must be checked in codebase

## 5. Logic Constraints

### State Management Pattern Table

| State Type | Access Pattern | File/Pattern Location | Used By |
|-----------|----------------|------------------------|---------|
| Checkout State | withCheckout HOC | (file path not verified) | All step components |
| Form State | Formik | Formik library | All form components |
| Payment Method State | PaymentContext | Payment.tsx | Payment method components |

**State Management Patterns (Verified):**
- **Checkout State:** Accessed via withCheckout HOC, not Redux, not Context API (verified)
- **Form State:** Managed by Formik (verified from step docs)
- **Payment Method Registration:** PaymentContext for payment method submit functions (verified from Payment step docs)

**Business Rule Locations (Verified):**
- Step completion/editability/requirement logic: `packages/core/src/app/checkout/getCheckoutStepStatuses.ts` (verified from step docs)

## 6. Data Constraints

**Data Access Pattern (Verified):**
- Checkout state accessed via withCheckout HOC from BigCommerce Checkout SDK (verified)
- Exact state shape not verified - must be checked in codebase

**Data Structures:**
- Exact data structures not verified - must be checked in codebase

## 7. Configuration Constraints

**Configuration Access (Verified):**
- Configuration accessed via checkout object from withCheckout HOC (verified)
- Exact configuration structure not verified - must be checked in codebase

## 8. Eliminated Cases

### Components NOT Implemented

- **Redux store:** Not used - uses BigCommerce Checkout SDK instead
- **Context API for checkout state:** Not used - uses withCheckout HOC instead
- **React Query:** Not used for data fetching
- **Zustand:** Not used for state management
- **MobX:** Not used for state management

### Integrations NOT Implemented

**Payment Providers:**
- Payment providers NOT in codebase must be verified by checking actual integration packages

**Shipping/Address:**
- No separate shipping/address integration packages (verified - handled via CheckoutService)

### Patterns NOT Used

- **Redux state management:** Not used - uses BigCommerce Checkout SDK
- **React Context for checkout state:** Not used - uses withCheckout HOC
- **React Query:** Not used for API calls
- **SWR:** Not used for data fetching
- **GraphQL:** Not used (if applicable)
- **MobX:** Not used for state management
- **Zustand:** Not used for state management

### Features NOT Available

- Separate shipping provider SDKs: Shipping handled via BigCommerce API
- Separate address validation services: Validation handled by BigCommerce API
- Direct API calls: All API calls go through CheckoutService, not direct fetch/axios
- Client-side routing: No React Router - navigation handled by step progression

### Services NOT Used

- **Redux store service:** Not used (verified)
- **Context API service:** Not used for checkout state (verified)

### Data Structures NOT Used

- **Redux state shape:** Not used (verified)
- **Context state shape:** Not used for checkout (verified)

---

## Maintenance Instructions

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

**Critical Note:** This document contains only verified information from architectural documentation. All unverified details (CSS classes, SCSS file paths, data structures, configuration details) must be verified against the actual codebase before use. This document serves as a starting point for constraint identification, not a complete reference.

