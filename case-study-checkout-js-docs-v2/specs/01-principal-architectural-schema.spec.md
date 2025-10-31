---
spec_version: 2
spec_id: checkout-js-principal-schema
title: "Spec: The Principal Architectural Schema"
description: To define the structure and content for the foundational "Principal Architectural Schema" document.
objective: To generate a high-level architectural document that defines the foundational structure ("the building") of the application, serving as the primary reference before detailing any cross-cutting concerns ("the wiring").
---

### 1. Architectural Principles

The documentation for the Principal Architectural Schema **must** be framed by the following core principles:

*   **Principle of Structural Foundation:** This document must be the **first** and highest-level architectural description. It describes the static, structural "floor plan" of the application.
*   **Principle of the Engineering Metaphor:** The document must introduce and consistently use the "House and Rooms" metaphor from an internal, engineering perspective to explain the architecture.
*   **Principle of Architectural Governance:** The schema must formally establish itself as the foundational blueprint. The spec must mandate that the document explicitly states that all subsequent "wiring diagrams" must use the terminology and structure defined within it.

### 2. Rationale

To avoid confusion and ensure a layered, top-down understanding of the system, a foundational document outlining the primary, coarse-grained components is required. This document serves as the stable "map" to which all other, more dynamic "wiring" schematics will refer. It must also explain the core reasoning behind its structure to prevent architectural drift.

### 3. Verification Criteria

The successful execution of this spec will result in the `01-principal-architectural-schema.md` file being populated with the following four sections:

1.  **The Architectural Metaphor: A House and its Rooms:**
    *   Must define the "House" (the `core` package), the "Rooms" (the feature modules), and the "Hallway" (the `CheckoutPage` orchestrator).

2.  **Architectural Rationale: Why These Rooms?**
    *   Must include a brief section explaining that the module decomposition is based on the principle of Separation of Concerns, aligned directly with the distinct steps of the user's checkout journey.

3.  **The "Rooms" (Core Feature Modules):**
    *   Must include a Markdown table that lists the primary feature modules and their architectural responsibilities.
    *   The following modules must be included: `Customer`, `Shipping`, `Billing`, `Payment`, `OrderConfirmation`, and `Cart`.

4.  **The "Floor Plan" (Structural Diagram):**
    *   Must include a Mermaid `graph` diagram.
    *   The diagram must visually represent the "House," "Rooms," and "Orchestrator."
    *   The diagram must show the orchestration flow from the `CheckoutPage` orchestrator to the feature modules, with `Billing` and `Payment` shown as distinct, sequential steps.
