---
**Title:** Testing Packages Reference
**Purpose:** A comprehensive, Level 3 inventory of all shared testing packages in the monorepo.
**Audience:** All Developers
**Maintenance:** Update when new testing packages are added or their APIs change significantly.
---

# Testing Packages Reference

This document is the definitive inventory of all shared packages that provide infrastructure for creating a consistent testing environment across the monorepo.

| Package Name | Purpose / Key Exports | Primary Consumers |
| :--- | :--- | :--- |
| **`@bigcommerce/checkout/test-utils`** | Shared utilities for component tests. Exports `<TestWrapper>` to provide necessary context/providers. | All packages with component tests. |
| **`@bigcommerce/checkout/test-framework`** | Provides the core configuration and setup for the Jest testing environment. | The root workspace `jest.config.js` |
| **`@bigcommerce/checkout/test-mocks`** | A library of mock objects and data for use in unit and integration tests (e.g., `getCheckout()`, `getStoreConfig()`). | All packages |

## Core Testing Components & Their Purpose

While the codebase currently lacks a single, canonical "golden path" test file to use as a recipe, the testing architecture is built on two fundamental components that work together. Understanding their purpose is key to writing effective tests.

### 1. The `<TestWrapper>` Component

*   **Package:** `@bigcommerce/checkout/test-utils`
*   **Purpose:** To solve the most common problem in component testing: providing the necessary **context**. Modern React applications are wrapped in a tree of providers (`ThemeProvider`, `LanguageProvider`, `CheckoutProvider`, etc.). A component rendered in isolation during a test will crash if it or one of its children tries to access a context that isn't there.
*   **Mechanism:** The `<TestWrapper>` component is a single, convenient wrapper that provides mock versions of all the essential providers that a component might need to render successfully. In a test, you simply wrap the component you are testing with `<TestWrapper>`, and it handles the rest.

### 2. The `test-mocks` Library

*   **Package:** `@bigcommerce/checkout/test-mocks`
*   **Purpose:** To provide a centralized, consistent, and realistic set of **mock data** for use in tests. When testing a component, you need to pass it props that mimic the real data it would receive from the application state (e.g., a mock `cart` object, a mock `config` object).
*   **Mechanism:** This package exports a suite of factory functions (e.g., `getCheckout()`, `getStoreConfig()`, `getCart()`) that return pre-populated, realistic mock objects. Using these factories ensures that all tests across the entire codebase are running against the same, predictable data shapes, which makes tests more reliable and easier to maintain.
