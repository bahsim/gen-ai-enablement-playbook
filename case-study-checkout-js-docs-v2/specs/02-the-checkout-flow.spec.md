---
**Title:** Spec for The Checkout Flow
**Purpose:** To define the mandatory structure and content for `02-the-checkout-flow.md`, ensuring it serves as a comprehensive and accurate single source of truth for the checkout flow logic.
---

# Spec for The Checkout Flow

## 1. Objective

This document must provide a complete, high-level map of the checkout flow. It must combine a high-level diagram of all possible user-driven state transitions with a detailed breakdown and diagram of the orchestrated business logic that guides the user through the "happy paths."

## 2. Core Components (Must be included)

The document must be structured with the following top-level sections in this order:

1.  **The Map of Possibilities (User State Transitions):** This section must explain and illustrate all valid, user-initiated navigations between the checkout steps.
2.  **The Rules of the Road (Orchestrated Journeys):** This section must explain and illustrate the conditional business logic that determines the sequence of steps for a user.

## 3. Mandatory Diagrams

The document must include two (2) Mermaid `graph` diagrams:

1.  **State Transition Diagram:** This diagram must model `Guest` and `Customer` as distinct actors and show all possible, user-driven transitions between the checkout steps (`Customer`, `Shipping`, `Billing`, `Payment`, `OrderConfirmation`).
2.  **Combined Journey State Diagram:** This diagram must visualize the orchestrated flow, including the key conditional branches (`IsLoggedIn`, `CartContainsPhysical`, `IsBillingSame`) and the resulting "happy paths."

## 4. Content Requirements for "The Rules of the Road"

This section must contain subsections detailing the primary conditional branches that govern the checkout journey. The following branches must be explicitly defined:

*   **Branch 1 (Entry Point):** Guest vs. Registered User
*   **Branch 2 (Mid-Journey):** Physical vs. Digital Goods
*   **Branch 3 (Post-Shipping):** Billing Address
*   **Branch 4 (In-Step Fork):** Multi-Shipping
