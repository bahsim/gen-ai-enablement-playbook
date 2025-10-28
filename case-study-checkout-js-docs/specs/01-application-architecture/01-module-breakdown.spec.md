# Spec: The Definitive Module Breakdown Guide

## 1. Objective

To analyze the `C:\learn\checkout-js` project and generate the complete, definitive content for the **`01-application-architecture/01-module-breakdown.md`** document.

## 2. Rationale

This document must be more than a simple inventory; it must be a practical, "Level 2" deep-dive guide that teaches developers the architectural philosophy and the practical rules for navigating and extending the monorepo. It provides the structural map of the codebase, explaining the *why* behind the separation of concerns.

## 3. Verification Criteria

The successful execution of this spec will result in the `01-application-architecture/01-module-breakdown.md` file being populated with the following sections:

1.  **Architectural Principles:**
    *   Must explain the core philosophies governing the codebase's structure:
        *   **Monorepo for Cohesion:** The rationale for using a monorepo.
        *   **Feature-Based Organization:** The principle of encapsulating domain logic.
        *   **Package-Based Separation of Concerns:** The strategy of using distinct packages for concerns like UI, testing, and integrations.
2.  **Monorepo Package Overview:**
    *   Must provide a high-level description of the major package categories (`core`, `payment-integrations`, `utility`, `testing`).
    *   Must include a **specific Mermaid diagram** that visualizes the `core` package as the central hub and the other categories as its dependents.
3.  **Core Application Feature Modules:**
    *   Must analyze the `packages/core/src/app` directory.
    *   Must present the findings in a **table** with columns for **Module**, **Primary Responsibility**, and **Key Components/Interactions**.
    *   Must cover the primary feature modules (e.g., `address`, `cart`, `billing`, `payment`, `customer`, `shipping`).
4.  **Shared Utility Packages:**
    *   Must describe the purpose of key shared packages, including `ui`, `locale`, `error-handling-utils`, and `test-utils`.
5.  **Developer's Guide: Practical Rules & Conventions:**
    *   Must provide a practical set of rules for developers.
    *   Must include a sub-section on **"The Principle of Co-location,"** explaining the `Component.tsx`, `Component.scss`, and `Component.test.tsx` file structure.
    *   Must include a sub-section on **"Inter-Package Communication Rules,"** explaining the role of `tsconfig.base.json` path aliases and the correct way to import between packages.
