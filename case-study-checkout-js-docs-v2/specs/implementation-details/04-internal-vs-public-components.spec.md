---
**Title:** Spec for: Internal vs. Public Components: A Case Study
**Purpose:** To define the structure and mandatory content for a new implementation detail document that analyzes a concrete example of the "internal vs. public API" architectural pattern.
**Audience:** Internal
---

# Spec: Internal vs. Public Components: A Case Study

## 1. Document Purpose

The new document, `04-internal-vs-public-components.md`, must serve as a detailed, evidence-based analysis of the architectural pattern where two components with the same name serve different roles: one for internal orchestration and one as a public, extensible API.

## 2. Mandatory Sections

The document must contain the following sections:

### 1. The Core Thesis
*   Must begin with a clear, concise statement summarizing the architectural principle: that the codebase deliberately contains separate components for internal orchestration and for public-facing extension, even when they share a name.

### 2. The Primary Evidence: A Side-by-Side Comparison
*   Must present a side-by-side analysis of the two key files:
    *   `packages/core/src/app/payment/paymentMethod/HostedCreditCardPaymentMethod.tsx`
    *   `packages/hosted-credit-card-integration/src/HostedCreditCardPaymentMethod.tsx`
*   For each file, the analysis must focus on:
    *   **Key Imports:** Highlighting the difference between internal, `core`-relative imports vs. public `payment-integration-api` imports.
    *   **Architectural Role:** Defining one as an "Internal Orchestrator" and the other as a "Public Extensibility Endpoint."
    *   **Implementation Details:** Briefly contrasting the wrapper-based approach of the internal component with the self-contained nature of the public one.

### 3. The "Why": Architectural Justification
*   Must explain *why* this pattern is used.
*   The explanation must clearly state that this separation:
    *   Protects the internal default checkout flow from breaking changes.
    *   Provides a stable, versioned, public API for external developers and customizers.
    *   Is the foundation of the "plugin" and extensibility architecture.

## 3. Principles
*   All claims must be verifiable by direct code evidence.
*   The document should be concise and focused exclusively on this specific architectural pattern.
