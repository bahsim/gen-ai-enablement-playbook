# Payment Component - Implementation Analysis

## Core Architecture

The `Payment` component manages the entire payment flow, including method selection, validation, submission, and error handling. It's a complex class component that orchestrates payment processing through the BigCommerce SDK.

### State Management Pattern

```typescript
interface PaymentState {
    didExceedSpamLimit: boolean;                                    // Spam protection state
    isReady: boolean;                                              // Component readiness
    selectedMethod?: PaymentMethod;                                // Currently selected method
    shouldDisableSubmit: { [key: string]: boolean };              // Per-method submit state
    shouldHidePaymentSubmitButton: { [key: string]: boolean };    // Per-method button visibility
    submitFunctions: { [key: string]: ((values: PaymentFormValues) => void) | null };  // Custom submit handlers
    validationSchemas: { [key: string]: ObjectSchema<Partial<PaymentFormValues>> | null };  // Per-method validation
}
```

**Key Implementation Details:**
- Uses unique payment method IDs as keys for per-method state management
- Maintains separate submit functions and validation schemas for each payment method
- Tracks spam protection state separately from general readiness

### Payment Method Management

The component implements sophisticated payment method handling:

```typescript
private async loadPaymentMethodsOrThrow(): Promise<void> {
    const { loadPaymentMethods, onUnhandledError = noop } = this.props;

    try {
        await loadPaymentMethods();

        const selectedMethod = this.state.selectedMethod || this.props.defaultMethod;

        if (selectedMethod) {
            this.trackSelectedPaymentMethod(selectedMethod);
        }
    } catch (error) {
        onUnhandledError(error);
    }
}
```

**Method Selection Logic:**
- Loads available payment methods from the SDK
- Tracks analytics for selected methods
- Handles errors through centralized error handling

### Submit Function Registration

Payment methods can register custom submit functions:

```typescript
private setSubmit: (
    method: PaymentMethod,
    fn: (values: PaymentFormValues) => void | null,
) => void = (method, fn) => {
    const uniqueId = getUniquePaymentMethodId(method.id, method.gateway);
    const { submitFunctions } = this.state;

    if (submitFunctions[uniqueId] === fn) {
        return;
    }

    this.setState({
        submitFunctions: {
            ...submitFunctions,
            [uniqueId]: fn,
        },
    });
};
```

**Architectural Pattern:**
- Uses unique IDs combining method ID and gateway
- Allows payment methods to override default submission behavior
- Prevents unnecessary re-renders by checking equality

### Validation Schema Management

Each payment method can have its own validation schema:

```typescript
private setValidationSchema: (
    method: PaymentMethod,
    schema: ObjectSchema<Partial<PaymentFormValues>> | null,
) => void = (method, schema) => {
    const uniqueId = getUniquePaymentMethodId(method.id, method.gateway);
    const { validationSchemas } = this.state;

    if (validationSchemas[uniqueId] === schema) {
        return;
    }

    this.setState({
        validationSchemas: {
            ...validationSchemas,
            [uniqueId]: schema,
        },
    });
};
```

### Submit Handling Logic

The component implements complex submit logic with multiple fallback strategies:

```typescript
private handleSubmit: (values: PaymentFormValues) => void = async (values) => {
    const {
        defaultMethod,
        loadPaymentMethods,
        isPaymentDataRequired,
        onCartChangedError = noop,
        onSubmit = noop,
        onSubmitError = noop,
        submitOrder,
        analyticsTracker,
    } = this.props;

    const { selectedMethod = defaultMethod, submitFunctions } = this.state;

    analyticsTracker.clickPayButton({shouldCreateAccount: values.shouldCreateAccount});

    // 1. Check for custom submit function
    const customSubmit = selectedMethod &&
        submitFunctions[getUniquePaymentMethodId(selectedMethod.id, selectedMethod.gateway)];

    if (customSubmit) {
        return customSubmit(values);
    }

    // 2. Use default submission flow
    try {
        const state = await submitOrder(mapToOrderRequestBody(values, isPaymentDataRequired()));
        const order = state.data.getOrder();

        analyticsTracker.paymentComplete();
        onSubmit(order?.orderId);
    } catch (error) {
        analyticsTracker.paymentRejected();

        // 3. Handle specific error types
        if (isErrorWithType(error) && error.type === 'payment_method_invalid') {
            return loadPaymentMethods();
        }

        if (isCartChangedError(error)) {
            return onCartChangedError(error);
        }

        onSubmitError(error);
    }
};
```

**Error Handling Strategy:**
- Custom submit functions take precedence
- Specific error types trigger different recovery actions
- Cart changes are handled separately from payment errors
- Analytics tracking for all submit attempts

### Spam Protection Integration

The component implements spam protection with retry logic:

```typescript
private handleCloseModal: (event: Event, props: ErrorModalOnCloseProps) => Promise<void> =
    async (_, { error }) => {
        if (!error) {
            return;
        }

        const { cartUrl, clearError, loadCheckout } = this.props;
        const { type: errorType } = error as any;

        // Handle different error types
        if (errorType === 'provider_fatal_error' || errorType === 'order_could_not_be_finalized_error') {
            window.location.replace(cartUrl || '/');
        }

        if (errorType === 'tax_provider_unavailable') {
            window.location.reload();
        }

        if (errorType === 'cart_consistency') {
            await loadCheckout();
        }

        if (isErrorWithType(error) && error.body) {
            const { body, headers, status } = error;

            if (body.type === 'provider_error' && headers.location) {
                window.top?.location.assign(headers.location);
            }

            // Handle spam protection errors
            if (status === 429 || body.type === 'spam_protection_expired' || body.type === 'spam_protection_failed') {
                this.setState({ didExceedSpamLimit: true });
                await loadCheckout();
            }
        }

        clearError(error);
    };
```

### Store Credit Management

The component handles store credit application:

```typescript
private handleStoreCreditChange: (useStoreCredit: boolean) => void = async (useStoreCredit) => {
    const { applyStoreCredit, onUnhandledError = noop } = this.props;

    try {
        await applyStoreCredit(useStoreCredit);
    } catch (e) {
        onUnhandledError(e);
    }
};
```

### Cart Total Change Handling

The component subscribes to cart total changes and reloads payment methods:

```typescript
private async handleCartTotalChange(): Promise<void> {
    const { isReady } = this.state;

    if (!isReady) {
        return;
    }

    this.setState({ isReady: false });
    await this.loadPaymentMethodsOrThrow();
    this.setState({ isReady: true });
}
```

**Performance Optimization:**
- Only processes changes when component is ready
- Temporarily disables component during reload
- Prevents unnecessary processing during initialization

### Before Unload Handling

The component implements sophisticated before-unload logic:

```typescript
private handleBeforeUnload: (event: BeforeUnloadEvent) => string | undefined = (event) => {
    const { defaultMethod, isSubmittingOrder, language } = this.props;
    const { selectedMethod = defaultMethod } = this.state;

    // Don't show warning for certain payment methods that require redirection
    if (
        !isSubmittingOrder ||
        !selectedMethod ||
        selectedMethod.type === PaymentMethodProviderType.Hosted ||
        selectedMethod.type === PaymentMethodProviderType.PPSDK ||
        // ... extensive list of payment method IDs that don't need warnings
    ) {
        return;
    }

    const message = language.translate('common.leave_warning');
    event.returnValue = message;
    return message;
};
```

**Business Logic:**
- Only shows warnings for certain payment methods
- Considers submission state and method type
- Uses localized warning messages

### Context Value Management

The component provides context values to child components:

```typescript
private getContextValue = memoizeOne(() => {
    return {
        disableSubmit: this.disableSubmit,
        setSubmit: this.setSubmit,
        setValidationSchema: this.setValidationSchema,
        hidePaymentSubmitButton: this.hidePaymentSubmitButton,
    };
});
```

**Performance Optimization:**
- Uses `memoizeOne` to prevent unnecessary re-renders
- Provides stable function references to child components

### Error Modal Rendering

The component renders different error modals based on error types:

```typescript
private renderOrderErrorModal(): ReactNode {
    const { finalizeOrderError, language, shouldLocaliseErrorMessages, submitOrderError } = this.props;

    const error: any = submitOrderError || finalizeOrderError;

    if (
        !error ||
        error.type === 'order_finalization_not_required' ||
        error.type === 'payment_cancelled' ||
        error.type === 'payment_invalid_form' ||
        error.type === 'spam_protection_not_completed' ||
        error.type === 'invalid_hosted_form_value'
    ) {
        return null;
    }

    return (
        <ErrorModal
            error={error}
            message={mapSubmitOrderErrorMessage(
                error,
                language.translate.bind(language),
                shouldLocaliseErrorMessages,
            )}
            onClose={this.handleCloseModal}
            title={mapSubmitOrderErrorTitle(error, language.translate.bind(language))}
        />
    );
}
```

**Error Filtering Logic:**
- Filters out benign error types
- Uses localized error messages when available
- Provides custom error titles

## Source Files

- **Main Implementation**: `packages/core/src/app/payment/Payment.tsx`
- **Error Mapping**: `packages/core/src/app/payment/mapSubmitOrderErrorMessage.ts`
- **Order Request Mapping**: `packages/core/src/app/payment/mapToOrderRequestBody.ts`
- **Payment Context**: `packages/core/src/app/payment/PaymentContext.tsx`
- **Payment Method Utils**: `packages/core/src/app/payment/paymentMethod.ts`
