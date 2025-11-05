---
**Title:** Spec for The Design System: The UI & Component Slice
**Purpose:** To define the mandatory structure and content for `04-the-design-system.md`, ensuring it accurately documents the three-layered styling architecture and component usage patterns.
---

# Spec for The Design System

## 1. Objective
This document must provide a high-level guide to the application's Design System. It must explain the intended architecture of the canonical `packages/ui` design system, but it must also expose the reality of the forked, duplicate design system in `packages/core/src/app/ui` and document this as a source of architectural inconsistency and technical debt.

## 2. Core Components (Must be included)
The document must contain the following sections in this order:
1.  **Introduction:** Must introduce the Design System and immediately state the architectural inconsistency between the intended and true architectures.
2.  **The Intended Architecture:** Must describe the canonical `packages/ui` library and its principles.
3.  **The True Architecture:** Must describe the forked `packages/core/src/app/ui` design system and include the mandatory diagram specified below. Must explicitly label this a source of technical debt.
4.  **The Styling Architecture:** Must describe the role of `packages/core/src/scss` as a theming and layout layer, clarifying that it does not provide the base styles for the components themselves.
5.  **Practical Usage Pattern & The Architectural Anomaly:** Must use a code example to show how `core` feature modules import from the local, forked design system.

## 3. Mandatory Diagram
The document must include a Mermaid `graph TD` diagram that illustrates the true, forked architecture. The diagram must:
*   Contain two subgraphs: `"packages/ui"` and `"packages/core"`.
*   Show that the `core` package contains a "Forked/Duplicate Design System".
*   Show that the Application Feature Modules consume the forked system, not the canonical one.
