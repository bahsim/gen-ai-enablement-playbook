# Implementation Details: Error Handling

This document provides the low-level, evidence-based implementation details for the **Error Handling & Logging Slice**. It provides concrete code examples for the Dual-Flow Model described in the high-level `09-the-error-handling-architecture.md` document.

## 1. Flow 1: Asynchronous Action Errors (The Error Delegation Model)

This flow handles predictable errors from asynchronous operations like API calls.

### 1.1. The Catcher (`Shipping.tsx`)
Feature Modules use a standard `try/catch` block to catch errors and delegate them upwards via the `onUnhandledError` prop.

*   **Code Evidence (`packages/core/src/app/shipping/Shipping.tsx`):**
    ```typescript
    // packages/core/src/app/shipping/Shipping.tsx
    const handleSelectAddress = useCallback(async (address: Address) => {
        try {
            await updateShippingAddress(address);
        } catch (error) {
            onUnhandledError(error);
        }
    }, [updateShippingAddress, onUnhandledError]);
    ```

### 1.2. The Handler (`CheckoutPage.tsx`)
The `CheckoutPage` orchestrator receives the error and uses a `handleError` method to update its local state, which in turn triggers the display of an error modal.

*   **Code Evidence (`packages/core/src/app/checkout/CheckoutPage.tsx`):**
    ```typescript
    // packages/core/src/app/checkout/CheckoutPage.tsx
    private handleError(error: Error): void {
        this.setState({ error });
    }
    ```

### 1.3. The Displayer (`CheckoutPage.tsx`)
The orchestrator conditionally renders the `ErrorModal` component whenever its `error` state is populated.

*   **Code Evidence (`packages/core/src/app/checkout/CheckoutPage.tsx`):**
    ```tsx
    // packages/core/src/app/checkout/CheckoutPage.tsx
    <ErrorModal
        error={ error }
        onClose={ this.handleErrorModalClose }
    />
    ```

## 2. Flow 2: Synchronous Rendering Errors (The Error Boundary Model)

This flow acts as a safety net for unpredictable UI rendering errors.

### 2.1. The Catcher (`ErrorBoundary.tsx`)
The `ErrorBoundary` is a class component that uses the `componentDidCatch` lifecycle method to catch rendering errors from anywhere in its child component tree.

*   **Code Evidence (`packages/error/src/ErrorBoundary.tsx`):**
    ```typescript
    // packages/error/src/ErrorBoundary.tsx
    class ErrorBoundary extends Component<Props, State> {
        componentDidCatch(error: Error, errorInfo: ErrorInfo) {
            const { logger } = this.props;
            logger.log(error, { errorInfo });
        }
    }
    ```

### 2.2. The Wrapper (`CheckoutApp.tsx`)
The entire application is wrapped in the `ErrorBoundary` at the highest level to ensure complete coverage.

*   **Code Evidence (`packages/core/src/app/checkout/CheckoutApp.tsx`):**
    ```tsx
    // packages/core/src/app/checkout/CheckoutApp.tsx
    <ErrorBoundary logger={ errorLogger } fallbackComponent={ ErrorFallback }>
        <CheckoutProvider checkoutService={ checkoutService }>
            { /* ... rest of the application ... */ }
        </CheckoutProvider>
    </ErrorBoundary>
    ```

### 2.3. The Displayer (`ErrorFallback.tsx`)
If the `ErrorBoundary` catches an error, it renders a static `ErrorFallback` component to provide a graceful failure message to the user.

*   **Code Evidence (`packages/error/src/ErrorFallback.tsx`):**
    ```tsx
    // packages/error/src/ErrorFallback.tsx
    const ErrorFallback: FunctionComponent<ErrorFallbackProps> = ({ error }) => (
        <div className="layout-container">
            <h1><TranslatedString id="common.something_went_wrong" /></h1>
            { /* ... */ }
        </div>
    );
    ```

## 3. The Shared Logging Service

Both error handling flows use a single, shared `ErrorLogger` to ensure all errors, regardless of their origin, are logged for developer analysis.

*   **Code Evidence (`packages/core/src/app/checkout/checkout.tsx`):**
    ```typescript
    // packages/core/src/app/checkout/checkout.tsx
    const errorLogger = createErrorLogger(
        { ... },
        {
            // ... configuration ...
        }
    );
    ```
