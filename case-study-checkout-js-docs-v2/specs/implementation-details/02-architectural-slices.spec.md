---
**Title:** Spec for Architectural Slice Discovery
**Purpose:** To define the mandatory structure and content for `02-architectural-slices.md`, ensuring it serves as a definitive, evidence-based list of the system's cross-cutting concerns.
---

# Spec for Architectural Slice Discovery

## 1. Objective
This document must formally identify and define the primary, evidence-based "architectural slices" (cross-cutting concerns) present in the `checkout-js` codebase. It must serve as the foundational, factual basis for all subsequent architectural classification.

## 2. Content Requirements
The document must contain a list of discovered architectural slices. For each slice, the following two subsections are mandatory:
1.  **Architectural Responsibility:** A concise definition of the slice's horizontal capability.
2.  **Primary Code Evidence:** A list of file paths that serve as verifiable, canonical examples of the slice's implementation.

## 3. Mandatory Slices
The document must identify and define the following seven (7) architectural slices:
1.  Step-Based View Management Slice
2.  State Management Slice
3.  Forms & Validation Slice
4.  UI & Component Slice
5.  Internationalization (I18n) Slice
6.  Error Handling & Logging Slice
7.  Integration & Extensibility Slice
