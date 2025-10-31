---
**Title:** The Async Loading Guide
**Purpose:** The deep-dive guide for the Asynchronous Loading & Code-Splitting slice, a performance-focused Cross-Cutting Concern (CCC).
**Audience:** All Developers
**Maintenance:** Review quarterly, or on major architectural change.
---

# The Async Loading Guide

This document is the guide for the **Asynchronous Loading & Code-Splitting slice**. This is a **governed horizontal slice** focused on performance. Its purpose is to improve the application's initial load time and overall responsiveness by deferring the loading of non-critical code until it is actually needed.

## 1. Architectural Principles

This slice is architected according to two core principles:

*   **Performance Optimization:** The primary goal is to reduce the size of the initial JavaScript bundle that a user must download. By code-splitting the application and lazily loading components, we significantly improve the Time to Interactive (TTI).
*   **Progressive Enhancement:** The slice enables a strategy of progressive enhancement. The core, critical-path user experience (e.g., the main checkout form) is delivered as quickly as possible, while secondary or heavy components (e.g., a complex payment method provider's UI) are loaded on demand.

## 2. Core Technologies

This slice is implemented using a combination of a standard bundler feature (dynamic `import()`) and two core React APIs:

*   **`React.lazy()`**: This is a function that lets you render a dynamically imported component as a regular component. It automatically handles the loading of the component's code bundle when it is first rendered.
*   **`<Suspense>`**: This is a React component that lets you specify a loading indicator (a "fallback" UI) for a part of the component tree that is not yet ready to be rendered. When `React.lazy()` is loading a component, the `<Suspense>` boundary above it will render the fallback.

## 3. Developer Cookbook: Lazily Loading a Component

The following recipe shows the standard "golden path" for code-splitting a component and loading it asynchronously.

```typescript
import React, { Suspense } from 'react';
import { LoadingSpinner } from '@bigcommerce/checkout/ui'; // Assuming a spinner component exists

// 1. Use React.lazy() to declare a dynamically imported component.
// The dynamic import() statement is what tells the bundler (e.g., Webpack)
// to create a separate code chunk for this component.
const HeavyPaymentComponent = React.lazy(() => import(
    /* webpackChunkName: "heavy-payment-component" */
    './HeavyPaymentComponent'
));


const MyParentComponent = ({ shouldShowPayment }) => {
    // This component will not load the code for HeavyPaymentComponent
    // until `shouldShowPayment` is true and it attempts to render it.

    return (
        <div>
            <h1>Checkout</h1>
            
            {/* 2. Wrap the lazy component in a <Suspense> boundary. */}
            {/* 3. Provide a fallback UI to show while the component is loading. */}
            {shouldShowPayment && (
                <Suspense fallback={<LoadingSpinner />}>
                    <HeavyPaymentComponent />
                </Suspense>
            )}

            {/* Other checkout components... */}
        </div>
    );
};
```
