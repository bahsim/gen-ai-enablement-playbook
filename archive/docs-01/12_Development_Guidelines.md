# BigCommerce POA React Checkout - Development Guidelines

## Code Organization

### File Naming Conventions

```
ComponentName.tsx          # React components
ComponentName.test.tsx     # Component tests
ComponentName.scss         # Component styles
_componentName.scss        # SCSS partials
ComponentName.types.ts     # Type definitions
ComponentName.mock.ts      # Test mocks
ComponentName.utils.ts     # Utility functions
```

### Directory Structure

```
packages/core/src/
├── app/                   # Main application components
│   ├── checkout/         # Checkout flow components
│   ├── customer/         # Customer-related components
│   ├── shipping/         # Shipping components
│   ├── billing/          # Billing components
│   ├── payment/          # Payment components
│   ├── order/            # Order confirmation components
│   ├── ui/               # Reusable UI components
│   └── common/           # Shared utilities
├── scss/                 # Styling files
│   ├── components/       # Component-specific styles
│   ├── layouts/          # Layout styles
│   ├── settings/         # Design tokens and variables
│   └── tools/            # SCSS mixins and functions
└── static/               # Static assets
    ├── img/              # Images
    └── svg/              # SVG icons
```

### Import Organization

```typescript
// 1. External libraries
import React, { Component, ReactNode } from 'react';
import classNames from 'classnames';
import { noop } from 'lodash';

// 2. Internal packages
import { TranslatedString } from '@bigcommerce/checkout/locale';
import { CheckoutSelectors } from '@bigcommerce/checkout-sdk';
import { CheckoutContextProps } from '@bigcommerce/checkout/payment-integration-api';

// 3. Relative imports
import { withCheckout } from '../checkout';
import { Button, ButtonVariant } from '../ui/button';
import { ErrorModal } from '../common/error';
```

## TypeScript Guidelines

### Interface Definitions

```typescript
// Use descriptive interface names
export interface CheckoutStepProps {
    children?: ReactNode;
    heading?: ReactNode;
    isActive?: boolean;
    isBusy: boolean;
    isComplete?: boolean;
    isEditable?: boolean;
    suggestion?: ReactNode;
    summary?: ReactNode;
    type: CheckoutStepType;
    stepNumber: number;
    onExpanded?(step: CheckoutStepType): void;
    onEdit?(step: CheckoutStepType): void;
}

// Use generic constraints for type safety
export function withBillingAndPaymentContext<T extends object>(
    Component: React.ComponentType<T>
) {
    return function WrappedComponent(props: T) {
        // Implementation
    };
}
```

### Type Safety Best Practices

```typescript
// Use strict typing
const handleSubmit = (values: PaymentFormValues): void => {
    // Implementation
};

// Avoid 'any' type
// BAD
const data: any = getData();

// GOOD
const data: CheckoutData = getData();

// Use union types for specific values
type CheckoutStepType = 'customer' | 'shipping' | 'billing' | 'payment';
```

## Component Development

### Class Component Template

```typescript
import React, { Component, ReactNode } from 'react';
import { noop } from 'lodash';

import { TranslatedString } from '@bigcommerce/checkout/locale';
import { withCheckout } from '../checkout';

export interface ComponentProps {
    onReady?(): void;
    onUnhandledError(error: Error): void;
}

export interface WithCheckoutProps {
    // Define checkout-related props
}

interface ComponentState {
    isReady: boolean;
    error?: Error;
}

class Component extends Component<ComponentProps & WithCheckoutProps, ComponentState> {
    state: ComponentState = {
        isReady: false,
    };

    async componentDidMount(): Promise<void> {
        const { onReady = noop, onUnhandledError = noop } = this.props;

        try {
            // Initialize component
            this.setState({ isReady: true });
            onReady();
        } catch (error) {
            if (error instanceof Error) {
                onUnhandledError(error);
            }
        }
    }

    render(): ReactNode {
        const { isReady, error } = this.state;

        if (error) {
            return <ErrorDisplay error={error} />;
        }

        return (
            <div className="component">
                <h2>
                    <TranslatedString id="component.heading" />
                </h2>
                {/* Component content */}
            </div>
        );
    }
}

function mapToComponentProps({
    checkoutService,
    checkoutState,
}: CheckoutContextProps): WithCheckoutProps | null {
    // Map checkout state to component props
    return {
        // Mapped props
    };
}

export default withCheckout(mapToComponentProps)(Component);
```

### Functional Component Template

```typescript
import React, { FunctionComponent, useCallback, useEffect } from 'react';

import { TranslatedString } from '@bigcommerce/checkout/locale';
import { Button, ButtonVariant } from '../ui/button';

export interface FunctionalComponentProps {
    onAction?(): void;
    onUnhandledError(error: Error): void;
}

const FunctionalComponent: FunctionComponent<FunctionalComponentProps> = (props) => {
    const { onAction, onUnhandledError } = props;

    const handleAction = useCallback(() => {
        try {
            onAction?.();
        } catch (error) {
            if (error instanceof Error) {
                onUnhandledError(error);
            }
        }
    }, [onAction, onUnhandledError]);

    useEffect(() => {
        // Side effects
    }, []);

    return (
        <div className="functional-component">
            <h2>
                <TranslatedString id="component.heading" />
            </h2>
            <Button
                onClick={handleAction}
                variant={ButtonVariant.Primary}
            >
                <TranslatedString id="component.action_button" />
            </Button>
        </div>
    );
};

export default FunctionalComponent;
```

## Styling Guidelines

### SCSS Architecture

```scss
// Use BEM methodology
.component {
    &__element {
        // Element styles
    }
    
    &--modifier {
        // Modifier styles
    }
}

// Use design tokens
.component {
    padding: spacing('single');
    background: color('white');
    border: 1px solid color('grey-light');
    font-size: fontSize('base');
}

// Responsive design
.component {
    padding: spacing('single');
    
    @media (max-width: $breakpoint-medium) {
        padding: spacing('half');
    }
}
```

### Design Token Usage

```scss
// Use spacing tokens
$component-padding: spacing('single');
$component-margin: spacing('double');

// Use color tokens
$component-background: color('white');
$component-text: color('grey-dark');

// Use typography tokens
$component-font-size: fontSize('base');
$component-font-weight: fontWeight('normal');
```

## Testing Guidelines

### Component Testing

```typescript
import React from 'react';
import { render, screen, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

import Component from './Component';
import { ComponentProps } from './Component';

describe('Component', () => {
    let defaultProps: ComponentProps;

    beforeEach(() => {
        defaultProps = {
            onUnhandledError: jest.fn(),
        };
    });

    it('renders component correctly', () => {
        render(<Component {...defaultProps} />);
        
        expect(screen.getByText('Component Heading')).toBeInTheDocument();
    });

    it('handles user interaction', async () => {
        const user = userEvent.setup();
        const handleAction = jest.fn();
        
        render(<Component {...defaultProps} onAction={handleAction} />);
        
        await user.click(screen.getByRole('button'));
        
        expect(handleAction).toHaveBeenCalled();
    });

    it('handles errors gracefully', async () => {
        const onUnhandledError = jest.fn();
        
        render(<Component {...defaultProps} onUnhandledError={onUnhandledError} />);
        
        // Trigger error condition
        await waitFor(() => {
            expect(onUnhandledError).toHaveBeenCalled();
        });
    });
});
```

### Hook Testing

```typescript
import { renderHook, act } from '@testing-library/react';
import { useContext } from './useContext';
import { ContextProvider } from './ContextProvider';

describe('useContext', () => {
    it('returns context value', () => {
        const wrapper = ({ children }: { children: React.ReactNode }) => (
            <ContextProvider>{children}</ContextProvider>
        );

        const { result } = renderHook(() => useContext(), { wrapper });

        expect(result.current.data).toBeDefined();
        expect(result.current.updateData).toBeInstanceOf(Function);
    });

    it('throws error when used outside provider', () => {
        const { result } = renderHook(() => useContext());

        expect(result.error).toBeInstanceOf(Error);
        expect(result.error?.message).toBe('useContext must be used within a ContextProvider');
    });
});
```

## Error Handling Guidelines

### Error Boundary Implementation

```typescript
import React, { Component, ErrorInfo, ReactNode } from 'react';

import { TranslatedString } from '@bigcommerce/checkout/locale';

export interface ErrorBoundaryProps {
    children: ReactNode;
    fallback?: ReactNode;
    onError?(error: Error, errorInfo: ErrorInfo): void;
}

export interface ErrorBoundaryState {
    hasError: boolean;
    error?: Error;
}

class ErrorBoundary extends Component<ErrorBoundaryProps, ErrorBoundaryState> {
    state: ErrorBoundaryState = {
        hasError: false,
    };

    static getDerivedStateFromError(error: Error): ErrorBoundaryState {
        return {
            hasError: true,
            error,
        };
    }

    componentDidCatch(error: Error, errorInfo: ErrorInfo): void {
        const { onError } = this.props;
        
        if (onError) {
            onError(error, errorInfo);
        }
    }

    render(): ReactNode {
        const { hasError, error } = this.state;
        const { children, fallback } = this.props;

        if (hasError) {
            return fallback || (
                <div className="error-boundary">
                    <h2>
                        <TranslatedString id="common.error_heading" />
                    </h2>
                    <p>
                        <TranslatedString id="common.error_message" />
                    </p>
                    {error && (
                        <details>
                            <summary>Error Details</summary>
                            <pre>{error.stack}</pre>
                        </details>
                    )}
                </div>
            );
        }

        return children;
    }
}

export default ErrorBoundary;
```

### Error Handling Best Practices

```typescript
// Always handle errors gracefully
const handleSubmit = async (values: FormValues) => {
    try {
        setLoading(true);
        setError(null);
        
        const result = await submitForm(values);
        
        if (result.success) {
            onSuccess(result);
        } else {
            throw new Error(result.error);
        }
    } catch (error) {
        if (error instanceof Error) {
            setError(error.message);
            onUnhandledError(error);
        }
    } finally {
        setLoading(false);
    }
};

// Use specific error types
class ValidationError extends Error {
    constructor(message: string, public field: string) {
        super(message);
        this.name = 'ValidationError';
    }
}

class PaymentError extends Error {
    constructor(message: string, public code: string) {
        super(message);
        this.name = 'PaymentError';
    }
}
```

## Performance Guidelines

### Memoization

```typescript
// Use React.memo for expensive components
const ExpensiveComponent = React.memo(({ data, onAction }: Props) => {
    const processedData = useMemo(() => 
        data.map(item => processItem(item)),
        [data]
    );

    const handleAction = useCallback((item: Item) => {
        onAction(item);
    }, [onAction]);

    return (
        <div>
            {processedData.map(item => (
                <ItemComponent
                    key={item.id}
                    item={item}
                    onAction={handleAction}
                />
            ))}
        </div>
    );
});

// Use useMemo for expensive calculations
const expensiveValue = useMemo(() => {
    return heavyCalculation(data);
}, [data]);

// Use useCallback for event handlers
const handleClick = useCallback(() => {
    onAction();
}, [onAction]);
```

### Lazy Loading

```typescript
// Lazy load components
const LazyComponent = lazy(() => import('./LazyComponent'));

// Use Suspense for loading states
<Suspense fallback={<LoadingSpinner />}>
    <LazyComponent />
</Suspense>

// Lazy load with retry
const LazyComponent = lazy(() =>
    retry(() => import('./LazyComponent'))
);
```

## Accessibility Guidelines

### ARIA Implementation

```typescript
// Use semantic HTML
<main role="main">
    <h1>Checkout</h1>
    <section aria-labelledby="shipping-heading">
        <h2 id="shipping-heading">Shipping Information</h2>
        <form aria-label="Shipping form">
            <label htmlFor="address">Address</label>
            <input
                id="address"
                type="text"
                aria-describedby="address-help"
                aria-invalid={hasError}
                aria-required="true"
            />
            <div id="address-help">Enter your shipping address</div>
            {hasError && (
                <div role="alert" aria-live="polite">
                    {errorMessage}
                </div>
            )}
        </form>
    </section>
</main>
```

### Keyboard Navigation

```typescript
// Handle keyboard events
const handleKeyDown = (event: KeyboardEvent) => {
    if (event.key === 'Enter' || event.key === ' ') {
        event.preventDefault();
        handleAction();
    }
};

// Focus management
const focusFirstInput = (container: HTMLElement) => {
    const firstInput = container.querySelector('input, select, textarea') as HTMLElement;
    if (firstInput) {
        firstInput.focus();
    }
};
```

## Security Guidelines

### Input Validation

```typescript
// Validate all inputs
const validateInput = (value: string, type: string): boolean => {
    switch (type) {
        case 'email':
            return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value);
        case 'phone':
            return /^\+?[\d\s-()]+$/.test(value);
        case 'postalCode':
            return /^[A-Za-z0-9\s-]+$/.test(value);
        default:
            return true;
    }
};

// Sanitize user input
const sanitizeInput = (input: string): string => {
    return input.trim().replace(/[<>]/g, '');
};
```

### Secure Data Handling

```typescript
// Never log sensitive data
const logError = (error: Error, context: any) => {
    console.error('Error occurred:', {
        message: error.message,
        stack: error.stack,
        context: {
            // Only log non-sensitive context
            step: context.step,
            timestamp: context.timestamp,
        },
    });
};

// Use secure communication
const submitPayment = async (paymentData: PaymentData) => {
    const response = await fetch('/api/payment', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            'X-CSRF-Token': getCsrfToken(),
        },
        body: JSON.stringify(paymentData),
    });
    
    return response.json();
};
```

## Code Review Guidelines

### Review Checklist

- [ ] **Functionality**: Does the code work as intended?
- [ ] **Testing**: Are there adequate tests?
- [ ] **Performance**: Any performance implications?
- [ ] **Security**: Any security vulnerabilities?
- [ ] **Accessibility**: WCAG compliance maintained?
- [ ] **Documentation**: Code is well-documented?
- [ ] **Type Safety**: Proper TypeScript usage?
- [ ] **Error Handling**: Comprehensive error handling?
- [ ] **Styling**: Consistent with design system?
- [ ] **Internationalization**: Proper translation usage?

### Common Issues to Avoid

```typescript
// BAD: Using any type
const data: any = getData();

// GOOD: Use specific types
const data: CheckoutData = getData();

// BAD: Missing error handling
const result = await apiCall();

// GOOD: Handle errors
try {
    const result = await apiCall();
} catch (error) {
    handleError(error);
}

// BAD: Hardcoded strings
<h1>Checkout</h1>

// GOOD: Use translations
<h1><TranslatedString id="checkout.heading" /></h1>
```

These development guidelines ensure consistent, maintainable, and high-quality code across the BigCommerce POA React Checkout application.
