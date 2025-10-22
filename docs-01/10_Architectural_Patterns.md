# BigCommerce POA React Checkout - Architectural Patterns

## Component Architecture Patterns

### 1. Higher-Order Components (HOCs)

The application extensively uses HOCs for context injection and cross-cutting concerns:

```typescript
// Pattern: Context injection via HOCs
export function withBillingAndPaymentContext<T extends object>(
    Component: React.ComponentType<T>
) {
    return function WrappedComponent(props: T) {
        return (
            <BillingAndPaymentContextProvider>
                <Component {...props} />
            </BillingAndPaymentContextProvider>
        );
    };
}

// Usage
export default withAnalytics(withBillingAndPaymentContext(BillingAndPayment));
```

### 2. Compound Components

The checkout uses compound component patterns for complex UI structures:

```typescript
// Pattern: Compound component structure
<CheckoutStep>
    <CheckoutStepHeader />
    <CheckoutStepContent>
        <CustomerStep />
        <ShippingStep />
        <BillingAndPaymentStep />
    </CheckoutStepContent>
</CheckoutStep>
```

### 3. Context-Based State Management

Multiple context providers manage different concerns:

```typescript
// Pattern: Multiple context providers for different concerns
<AnalyticsProvider>
    <CheckoutProvider>
        <PaymentProvider>
            <BillingAndPaymentContextProvider>
                <App />
            </BillingAndPaymentContextProvider>
        </PaymentProvider>
    </CheckoutProvider>
</AnalyticsProvider>
```

## State Management Patterns

### 1. React Context + Reselect

The application uses React Context with Reselect for optimized state selection:

```typescript
// ACTUAL: Real Reselect usage from mapToCheckoutProps.ts
import { createSelector } from 'reselect';

export default function mapToCheckoutProps({
    checkoutService,
    checkoutState,
}: CheckoutContextProps): WithCheckoutProps {
    const { data, errors, statuses } = checkoutState;
    const { promotions = EMPTY_ARRAY } = data.getCheckout() || {};
    const submitOrderError = errors.getSubmitOrderError() as CustomError;
    
    // Real selector usage in the codebase
    return {
        // ... mapped props
    };
}
```

### 2. Class Component State Management

The main checkout component uses class-based state management:

```typescript
// ACTUAL: Class component with local state
class Checkout extends Component<CheckoutProps & WithCheckoutProps, CheckoutState> {
    state: CheckoutState = {
        isBillingSameAsShipping: true,
        isCartEmpty: false,
        isRedirecting: false,
        isMultiShippingMode: false,
        hasSelectedShippingOptions: false,
        isSubscribed: false,
        buttonConfigs: [],
    };
    
    // State updates via setState
    this.setState({
        isBillingSameAsShipping: checkoutBillingSameAsShippingEnabled,
        isSubscribed: defaultNewsletterSignupOption,
    });
}
```

## Payment Integration Patterns

### 1. Payment Method Architecture

Each payment method follows a consistent integration pattern:

```typescript
// Pattern: Unified payment integration interface
interface PaymentIntegration {
    id: string;
    initialize(): Promise<void>;
    validate(): ValidationResult;
    process(): Promise<PaymentResult>;
    handleError(error: PaymentError): void;
}
```

### 2. Actual Payment Integration Pattern

The BillingAndPayment component demonstrates the actual integration pattern:

```typescript
// ACTUAL: Real payment integration from BillingAndPayment.tsx
const BillingAndPayment: FunctionComponent<BillingAndPaymentProps> = (props) => {
    const {isBillingReady, isPaymentReady, submitBilling, submitPayment} = useBillingAndPayment();

    const handlePlaceOrderClick = useCallback(() => {
        if (!submitBilling) {
            console.error('Cannot place order: submitBilling is not defined');   
            return;
        }
        submitBilling();
    }, [submitBilling, submitPayment]);

    return (
        <LazyContainer>
            <Payment
                checkEmbeddedSupport={checkEmbeddedSupport}
                errorLogger={errorLogger}
                onCartChangedError={onCartChangedError}
                onSubmit={onSubmit}
                onSubmitError={onSubmitError}
            />
            <Billing
                navigateNextStep={handleSubmitPayment}
                onUnhandledError={onUnhandledError}
            />
        </LazyContainer>
    );
};
```

## Error Handling Patterns

### 1. Comprehensive Error Handling

The application implements comprehensive error handling with user-friendly messages:

```typescript
// ACTUAL: Real error handling from translations
// packages/locale/src/translations/en.json
"payment": {
    "errors": {
        "card_declined": "Payment was declined. Please try a different card.",
        "card_error": "Your card details could not be verified. Please double check them and try again.",
        "expired_card": "Your card has expired. Please try again with a valid card.",
        "gateway_error": "Something went wrong on the server. Please try again at a later time.",
        "connection_error": "We're experiencing difficulty processing your transaction. Please try again later.",
        "duplicate_transaction": "This is a duplicate transaction. Please contact us to confirm your order."
    }
}
```

### 2. Error Recovery Patterns

Components implement error recovery mechanisms:

```typescript
// ACTUAL: Error handling from Checkout.tsx
componentDidUpdate(prevProps: WithCheckoutProps): void {
    if (this.props.error && this.props.error !== prevProps.error) {
        this.setState({ error: this.props.error });
    }
}

// ACTUAL: Error handling from BillingAndPayment.tsx
const handlePlaceOrderClick = useCallback(() => {
    if (!submitBilling) {
        console.error('Cannot place order: submitBilling is not defined');   
        return;
    }
    submitBilling();
}, [submitBilling, submitPayment]);
```

## Performance Optimization Patterns

### 1. Lazy Loading

The application uses lazy loading for code splitting:

```typescript
// ACTUAL: LazyContainer from packages/ui/src/loading/LazyContainer.tsx
const LazyContainer: FunctionComponent<LazyContainerProps> = ({ loadingSkeleton, children }) => (
    <ErrorBoundary
        fallback={
            <div className="lazyContainer-error">
                <TranslatedString id="common.unstable_network_error" />
            </div>
        }
        filter={filterError}
    >
        <Suspense fallback={loadingSkeleton || <LoadingSpinner isLoading />}>{children}</Suspense>
    </ErrorBoundary>
);
```

### 2. Memoization

Strategic use of React hooks for performance:

```typescript
// ACTUAL: useCallback from BillingAndPayment.tsx
const handlePlaceOrderClick = useCallback(() => {
    if (!submitBilling) {
        console.error('Cannot place order: submitBilling is not defined');   
        return;
    }
    submitBilling();
}, [submitBilling, submitPayment]);

// ACTUAL: useEffect for side effects
useEffect(() => {
    if (isBillingReady && isPaymentReady) {
        onReady?.();
    }
}, [isBillingReady, isPaymentReady]);
```

## Testing Patterns

### 1. Multi-Layered Testing Strategy

The application implements a comprehensive testing pyramid:

```typescript
// ACTUAL: Real test from CheckoutStepHeader.test.tsx
describe('CheckoutStepHeader', () => {
    let defaultProps: CheckoutStepHeaderProps;

    beforeEach(() => {
        defaultProps = {
            heading: 'Billing',
            summary: 'Billing summary',
            type: CheckoutStepType.Billing,
        };
    });

    it('renders summary if it is inactive and complete', () => {
        render(<CheckoutStepHeader {...defaultProps} isComplete />);
        expect(screen.getByTestId('step-info')).toHaveTextContent('Billing summary');
    });

    it('triggers callback when clicked', async () => {
        const handleEdit = jest.fn();
        render(<CheckoutStepHeader {...defaultProps} isEditable onEdit={handleEdit}/>);
        
        await userEvent.click(screen.getByRole('heading'));
        expect(handleEdit).toHaveBeenCalledWith(defaultProps.type);
    });
});
```

### 2. Jest Configuration

Custom Jest configuration with transformers:

```javascript
// Jest configuration with custom transformers
module.exports = {
    transform: {
        '^.+\\.(ts|tsx)?$': ['ts-jest', {
            tsconfig: '<rootDir>/tsconfig.spec.json',
            diagnostics: false,
        }],
        '\\.(gif|png|jpe?g|svg)$': '../../scripts/jest/file-transformer',
        '\\.scss$': '../../scripts/jest/style-transformer',
    },
    setupFilesAfterEnv: ['../../jest-setup.ts'],
    coverageDirectory: '../../coverage/packages/core'
};
```

## Internationalization Patterns

### 1. Translation Key Convention

Consistent translation key structure:

```
<area>.<section>.<name>_(action|heading|label|text|<status>)

Examples:
- address.address_line_1_label
- address.address_line_1_required_error
- customer.create_account_success
- payment.payment_methods_text
```

### 2. Usage Pattern

Consistent translation usage throughout the application:

```typescript
// Pattern: Consistent translation usage
import { TranslatedString } from '@bigcommerce/checkout/locale';

<Button>
    <TranslatedString id="order_confirmation.continue_shopping" />
</Button>
```

## Analytics Patterns

### 1. Multi-Service Analytics

The application integrates multiple analytics services:

```typescript
// ACTUAL: Real analytics from AnalyticsProvider.tsx
const AnalyticsProvider = ({ checkoutService, children }: AnalyticsProviderProps) => {
    const checkoutBegin = () => {
        getStepTracker().trackCheckoutStarted();
        getBodlService().checkoutBegin();
    };

    const trackStepCompleted = (currentStep: string) => {
        getStepTracker().trackStepCompleted(currentStep);
        getBodlService().stepCompleted(currentStep);
    };

    const paymentComplete = () => {
        getBodlService().paymentComplete();
        getBraintreeAnalyticTracker().paymentComplete();
        getPayPalCommerceAnalyticTracker().paymentComplete();
    };

    const analyticsTracker: AnalyticsEvents = {
        checkoutBegin,
        trackStepCompleted,
        paymentComplete,
        // ... other methods
    };
};
```

## Security Patterns

### 1. Input Validation

Comprehensive input validation with Yup schemas:

```typescript
// ACTUAL: Real validation from getFormFieldsValidationSchema.ts
export default memoize(function getFormFieldsValidationSchema({
    formFields,
    translate = () => undefined,
}: FormFieldsValidationSchemaOptions): ObjectSchema<FormFieldValues> {
    return object({
        ...formFields
            .filter(({ custom }) => !custom)
            .reduce((schema, { name, required, label, maxLength }) => {
                schema[name] = string();

                if (required) {
                    schema[name] = schema[name]
                        .trim()
                        .required(translate('required', { label, name}));
                }

                schema[name] = schema[name].matches(
                    WHITELIST_REGEXP,
                    translate('invalid', { name, label }),
                );

                return schema;
            }, {} as { [key: string]: StringSchema }),
    });
});
```

### 2. XSS Prevention

DOMPurify integration for content sanitization:

```typescript
// ACTUAL: DOMPurify is imported in OrderConfirmation.tsx
import DOMPurify from 'dompurify';

// XSS prevention handled by React's built-in escaping and DOMPurify
```

## Build System Patterns

### 1. Nx Workspace Configuration

Intelligent build orchestration:

```json
{
    "targets": {
        "build": {
            "executor": "nx:run-commands",
            "options": {
                "command": "webpack --mode production"
            },
            "dependsOn": [
                { "target": "generate" },
                { "target": "build", "dependencies": true }
            ]
        }
    }
}
```

### 2. Webpack Configuration

Optimized bundling with code splitting:

```typescript
// Pattern: Lazy loading for payment integrations
const BillingAndPayment = lazy(() =>
    retry(
        () =>
            import(
                /* webpackChunkName: "billing-payment" */
                '../billing-and-payment/BillingAndPayment'
            ),
    ),
);
```

## Development Guidelines

### 1. Code Organization

Consistent file naming conventions:

```
ComponentName.tsx          # React components
ComponentName.test.tsx     # Component tests
ComponentName.scss         # Component styles
_componentName.scss        # SCSS partials
ComponentName.types.ts     # Type definitions
```

### 2. Import Organization

Structured import organization:

```typescript
// Pattern: Organized imports
// 1. External libraries
import React, { Component, ReactNode } from 'react';
import classNames from 'classnames';

// 2. Internal packages
import { TranslatedString } from '@bigcommerce/checkout/locale';
import { CheckoutSelectors } from '@bigcommerce/checkout-sdk';

// 3. Relative imports
import { withCheckout } from '../checkout';
import { Button, ButtonVariant } from '../ui/button';
```

### 3. TypeScript Patterns

Comprehensive interface definitions:

```typescript
// Pattern: Comprehensive interface definitions
export interface CheckoutProps {
    containerId: string;
    config: StoreConfig;
    checkoutService: CheckoutService;
    embeddedMessenger?: EmbeddedCheckoutMessenger;
}

export interface WithCheckoutProps {
    billingAddress?: Address;
    cart?: Cart;
    consignments?: Consignment[];
    error?: Error;
    hasCartChanged: boolean;
    // ... other props
}
```

These architectural patterns form the foundation of the BigCommerce POA React Checkout system, ensuring consistency, maintainability, and scalability across the entire application.
