---
**Title:** Spec for The Checkout Orchestrator Architecture
**Purpose:** To define the mandatory structure and content for `01-principal-architectural-schema.md`, ensuring it accurately documents the Orchestrator pattern.
---

# Spec for The Checkout Orchestrator Architecture

## 1. Objective
This document must define the role of the central `CheckoutPage` orchestrator, its architectural responsibilities, and its relationship with the feature modules it controls.

## 2. Core Components (Must be included)
The document must contain the following sections in this order:
1.  **The `CheckoutPage` Orchestrator:** Must define the component's role, its responsibility for flow control, and the principle that it contains no business logic itself.
2.  **The Orchestrated Modules ("Steps"):** Must list the feature modules managed by the orchestrator. This list must be separated into two subsections with tables: "Checkout Flow Modules" and "Order Confirmation Flow Module."
3.  **The Orchestrated Flow Diagram:** Must include the mandatory diagram specified below.

## 3. Mandatory Diagram
The document must include a Mermaid `graph` diagram that illustrates:
*   The `CheckoutPage` Orchestrator as the central controller.
*   The separation of the "Checkout flow" and the "Order Confirmation flow" as distinct subgraphs.
*   The hub-and-spoke relationship where the orchestrator has a direct control link to each of the primary feature modules within the checkout flow.
