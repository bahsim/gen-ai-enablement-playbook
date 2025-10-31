---
**Title:** Embedded Checkout Architecture
**Purpose:** A comprehensive, Level 3 deep-dive into the patterns for embedding the checkout in third-party sites.
**Audience:** All Developers
**Maintenance:** Update if the embedded checkout communication patterns change.
---

# Embedded Checkout Architecture

This document describes the architectural patterns that allow the checkout application to be securely embedded within a third-party website (e.g., a BigCommerce storefront).

The entire architecture is designed to solve the challenges of running a modern, single-page application inside an `<iframe>` while maintaining a seamless user experience.

## 1. Messenger Communication

The core of the embedded architecture is a **message-based communication system** that facilitates secure, cross-frame communication between the checkout application (the "child") and the parent window.

*   **Pattern:** Uses the browser's standard `window.postMessage` API. A dedicated "messenger" service listens for incoming messages, validates their origin for security, and then dispatches corresponding actions within the checkout application. It also provides a method for the checkout to send messages back to the parent window.
*   **Key Use Cases:**
    *   Notifying the parent window of navigation events.
    *   Resizing the `<iframe>` to fit the content of the checkout dynamically.
    *   Handling the final order confirmation and redirecting the parent page.

## 2. Style Injection

To ensure the embedded checkout matches the look and feel of the parent site, a **dynamic styling and theming** mechanism is used.

*   **Pattern:** The parent window can pass a set of theme or style configurations to the embedded checkout via the messenger. A dedicated service within the checkout then takes these configurations and injects them into the application's styling system, allowing for real-time changes to colors, fonts, and other visual elements.

## 3. Cross-Frame Event Handling

The system manages events that need to be coordinated between the child frame and the parent window.

*   **Pattern:** Events originating in the checkout (e.g., "order completed") are broadcast to the parent window using the messenger. Conversely, the parent window can send events to the checkout (e.g., "close checkout modal") to control its behavior.
