---
**Title:** The Feature Module Guide (The Vertical Slices)
**Purpose:** A guide to the internal, feature-based architecture of the `core` application, which constitutes the project's **vertical (feature/domain) slices**.
**Audience:** All Developers
**Maintenance:** Update when new feature modules are added or the co-location strategy changes.
---

# The Feature Module Guide (The Vertical Slices)

This document provides the "micro" view of the `checkout-js` architecture. It explains how the `core` application package is organized internally into **feature modules**, which are the **vertical (feature/domain) slices** that encapsulate specific pieces of business functionality.

This architecture is the necessary counterpart to the **horizontal slicing** of the monorepo's shared packages. By combining both, we create a pragmatic **hybrid model** that supports both highly reusable infrastructure and highly cohesive business logic.

## 1. Architectural Principle: Feature-Based Organization

The `core` application follows a strict feature-based organization principle. All code is organized by **domain feature** rather than by technical type. Instead of having global `components/`, `hooks/`, and `styles/` folders, all the code required for a specific feature (like `address` or `cart`) is kept together in its own dedicated module.

This vertical slicing strategy provides several key benefits:

*   **High Cohesion:** All related code is in one place, making it easy to understand the full context of a feature.
*   **Low Coupling:** Feature modules are self-contained and interact with the rest of the application through well-defined interfaces (primarily by consuming state from the horizontal `State Management` slice), which reduces dependencies.
*   **Discoverability:** When a developer needs to work on the "cart" feature, they know to go to the `cart/` folder. This significantly reduces the cognitive load required to navigate the codebase.

## 2. Core Application Feature Modules

The following table inventories the primary feature modules found within the `packages/core/src/app` directory.

| Module | Primary Responsibility | Key Components |
| :--- | :--- | :--- |
| **`address`** | Manages shipping and billing address forms and logic. | `AddressForm.tsx` |
| **`billing`** | Handles the billing step, including billing address and payment. | `Billing.tsx` |
| **`cart`** | Displays the cart summary, coupons, and related logic. | `CartSummary.tsx` |
| **`checkout`** | The top-level orchestrator for the entire checkout process. | `Checkout.tsx` |
| **`common`** | A shared location for common utilities and hooks *within the core app*. | `utility.ts` |
| **`customer`** | Handles guest/login forms and customer-related logic. | `Customer.tsx`, `LoginForm.tsx` |
| **`order`** | Manages the order confirmation and thank you page. | `OrderConfirmation.tsx`|
| **`payment`** | Orchestrates the payment step and integrates payment methods. | `Payment.tsx` |
| **`shipping`** | Manages the shipping step, including shipping options and quotes.| `Shipping.tsx` |

## 3. Developer's Guide: The Principle of Co-location

A key part of the vertical slicing strategy is the strict **co-location** of related files within a feature module.

**Rule:** All files related to a single component—its logic, styles, and tests—**must** be kept in the same directory. This makes the component a self-contained, portable unit.

*   **Example:** The file structure for a hypothetical `AddressBox` component within the `address` module would look like this:

```
packages/core/src/app/address/AddressBox/
├── AddressBox.scss
├── AddressBox.test.tsx
└── AddressBox.tsx
```
