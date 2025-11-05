---
**Title:** Spec for The Architectural Structure: Core & Supplementary Slices
**Purpose:** To define the mandatory structure and content for `03-the-architectural-structure.md`, ensuring it accurately documents the "Core vs. Supplementary" architectural model.
---

# Spec for The Architectural Structure

## 1. Objective
This document must define the application's high-level structure using the **"Core & Supplementary"** model. It must establish a clear conceptual separation between the central business logic (the Core Application) and the foundational, horizontal capabilities that support it (the Supplementary Slices).

## 2. Core Components (Must be included)
The document must contain the following sections in this order:
1.  **Introduction:** Must introduce the "Core & Supplementary" model as the key to understanding the codebase.
2.  **High-Level Structural Diagram:** Must include the mandatory diagram specified below, updated to reflect the Hybrid Data Flow model.
3.  **The Architectural Layers:** Must be a single section containing the following three mandatory, ordered subsections:
    *   `### 1. The Core Application (Flow Control & Persistent UI)`
    *   `### 2. The Feature Modules (The Steps)`
    *   `### 3. The Supplementary Slices (Foundational Capabilities)`

## 3. Mandatory Diagram
The document must include a Mermaid `graph TD` diagram that illustrates the three-tiered architectural model, consistent with the Hybrid Data Flow pattern. The diagram must:
*   Contain three distinct subgraphs: `"Core Application"`, `"Feature Modules (Steps)"`, and `"Supplementary Slices (Foundational Capabilities)"`.
*   Show the primary relationships: The Core Application manages the sequence of Feature Modules.
*   Show the parallel data connections: Both the Core Application and the Feature Modules must be shown to independently connect to the Supplementary Slices.
