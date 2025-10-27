# HOC Pattern - Implementation Analysis

## Core Architecture

The `withCheckout.tsx` file implements a Higher-Order Component (HOC) pattern for injecting checkout context into React components. It provides a clean, reusable way to access checkout state and services throughout the application.

### HOC Implementation

```typescript
import { createMappableInjectHoc } from '@bigcommerce/checkout/legacy-hoc';
import { CheckoutContext, CheckoutContextProps } from '@bigcommerce/checkout/payment-integration-api';

export type WithCheckoutProps = CheckoutContextProps;

const withCheckout = createMappableInjectHoc(CheckoutContext, {
    displayNamePrefix: 'WithCheckout',
});

export default withCheckout;
```

**HOC Strategy:**
- **Legacy HOC System**: Uses BigCommerce's legacy HOC system
- **Context Injection**: Injects checkout context into components
- **Type Safety**: Provides proper TypeScript types
- **Display Name**: Sets display name prefix for debugging

## Context Integration

### Checkout Context

```typescript
import { CheckoutContext, CheckoutContextProps } from '@bigcommerce/checkout/payment-integration-api';
```

**Context Strategy:**
- **Context Import**: Imports checkout context and props
- **Type Reuse**: Reuses existing type definitions
- **API Integration**: Integrates with payment integration API
- **Type Safety**: Maintains type safety throughout

### Context Props

```typescript
export type WithCheckoutProps = CheckoutContextProps;
```

**Props Strategy:**
- **Type Alias**: Creates type alias for clarity
- **Context Props**: Reuses context props type
- **Type Safety**: Maintains type safety
- **Documentation**: Provides clear type documentation

## HOC Factory

### createMappableInjectHoc

```typescript
const withCheckout = createMappableInjectHoc(CheckoutContext, {
    displayNamePrefix: 'WithCheckout',
});
```

**Factory Strategy:**
- **Context Injection**: Injects checkout context
- **Display Name**: Sets display name prefix
- **HOC Creation**: Creates reusable HOC
- **Configuration**: Configures HOC behavior

### Display Name Configuration

```typescript
{
    displayNamePrefix: 'WithCheckout',
}
```

**Display Name Strategy:**
- **Debugging**: Helps with React DevTools debugging
- **Component Identification**: Identifies wrapped components
- **Naming Convention**: Follows consistent naming convention
- **Development**: Aids in development and debugging

## Usage Pattern

### Component Wrapping

```typescript
// Example usage
const MyComponent = ({ checkoutService, checkoutState }: WithCheckoutProps) => {
    // Component implementation
};

export default withCheckout(MyComponent);
```

**Wrapping Strategy:**
- **Props Injection**: Injects checkout props into component
- **Type Safety**: Maintains TypeScript type safety
- **Service Access**: Provides access to checkout service
- **State Access**: Provides access to checkout state

### Props Access

```typescript
interface MyComponentProps {
    // Other props
}

const MyComponent = ({ 
    checkoutService, 
    checkoutState, 
    ...otherProps 
}: MyComponentProps & WithCheckoutProps) => {
    // Component implementation
};
```

**Props Strategy:**
- **Intersection Types**: Combines component props with HOC props
- **Service Access**: Accesses checkout service
- **State Access**: Accesses checkout state
- **Type Safety**: Maintains type safety

## Legacy HOC System

### createMappableInjectHoc

```typescript
import { createMappableInjectHoc } from '@bigcommerce/checkout/legacy-hoc';
```

**Legacy System Strategy:**
- **Legacy Support**: Supports legacy HOC patterns
- **Backward Compatibility**: Maintains backward compatibility
- **Migration Path**: Provides migration path for new patterns
- **Feature Support**: Supports advanced HOC features

### HOC Features

```typescript
const withCheckout = createMappableInjectHoc(CheckoutContext, {
    displayNamePrefix: 'WithCheckout',
});
```

**Feature Strategy:**
- **Context Injection**: Injects React context
- **Display Name**: Sets display name for debugging
- **Type Safety**: Maintains TypeScript type safety
- **Performance**: Optimizes performance

## Type Safety

### Type Definitions

```typescript
export type WithCheckoutProps = CheckoutContextProps;
```

**Type Strategy:**
- **Type Alias**: Creates clear type alias
- **Context Props**: Reuses existing context props
- **Type Safety**: Maintains type safety
- **Documentation**: Provides clear documentation

### Generic Types

```typescript
const withCheckout = createMappableInjectHoc(CheckoutContext, {
    displayNamePrefix: 'WithCheckout',
});
```

**Generic Strategy:**
- **Type Inference**: Leverages TypeScript type inference
- **Context Types**: Uses context types
- **HOC Types**: Maintains HOC type safety
- **Component Types**: Preserves component types

## Performance Considerations

### HOC Optimization

```typescript
const withCheckout = createMappableInjectHoc(CheckoutContext, {
    displayNamePrefix: 'WithCheckout',
});
```

**Performance Strategy:**
- **Memoization**: HOC system provides memoization
- **Context Optimization**: Optimizes context access
- **Re-render Prevention**: Prevents unnecessary re-renders
- **Bundle Size**: Minimal bundle impact

### Context Access

```typescript
import { CheckoutContext, CheckoutContextProps } from '@bigcommerce/checkout/payment-integration-api';
```

**Access Strategy:**
- **Direct Access**: Provides direct context access
- **Service Access**: Provides service access
- **State Access**: Provides state access
- **Performance**: Optimizes context access

## Integration Points

### Checkout Context

```typescript
import { CheckoutContext, CheckoutContextProps } from '@bigcommerce/checkout/payment-integration-api';
```

**Integration Strategy:**
- **Context Integration**: Integrates with checkout context
- **API Integration**: Integrates with payment integration API
- **Service Integration**: Integrates with checkout services
- **State Integration**: Integrates with checkout state

### Legacy HOC System

```typescript
import { createMappableInjectHoc } from '@bigcommerce/checkout/legacy-hoc';
```

**Legacy Integration Strategy:**
- **Legacy Support**: Supports legacy HOC patterns
- **Migration Path**: Provides migration path
- **Backward Compatibility**: Maintains compatibility
- **Feature Support**: Supports advanced features

## Usage Examples

### Basic Usage

```typescript
const MyComponent = ({ checkoutService, checkoutState }: WithCheckoutProps) => {
    const { data, statuses } = checkoutState;
    const checkout = data.getCheckout();
    
    return (
        <div>
            {checkout && <h1>Checkout ID: {checkout.id}</h1>}
        </div>
    );
};

export default withCheckout(MyComponent);
```

**Basic Strategy:**
- **Props Access**: Accesses checkout props
- **Service Access**: Uses checkout service
- **State Access**: Accesses checkout state
- **Component Implementation**: Implements component logic

### Advanced Usage

```typescript
interface MyComponentProps {
    title: string;
    onAction: () => void;
}

const MyComponent = ({ 
    title, 
    onAction, 
    checkoutService, 
    checkoutState 
}: MyComponentProps & WithCheckoutProps) => {
    const { data, statuses } = checkoutState;
    const checkout = data.getCheckout();
    
    const handleAction = async () => {
        await checkoutService.loadCheckout();
        onAction();
    };
    
    return (
        <div>
            <h1>{title}</h1>
            {checkout && <p>Checkout ID: {checkout.id}</p>}
            <button onClick={handleAction}>Load Checkout</button>
        </div>
    );
};

export default withCheckout(MyComponent);
```

**Advanced Strategy:**
- **Props Combination**: Combines component props with HOC props
- **Service Usage**: Uses checkout service methods
- **State Management**: Manages checkout state
- **Event Handling**: Handles user interactions

## Testing Considerations

### HOC Testing

```typescript
// Test example
import { render } from '@testing-library/react';
import { CheckoutProvider } from '@bigcommerce/checkout/payment-integration-api';

const TestWrapper = ({ children }) => (
    <CheckoutProvider checkoutService={mockCheckoutService}>
        {children}
    </CheckoutProvider>
);

test('MyComponent with HOC', () => {
    render(
        <TestWrapper>
            <MyComponent title="Test" onAction={jest.fn()} />
        </TestWrapper>
    );
});
```

**Testing Strategy:**
- **Provider Wrapping**: Wraps with checkout provider
- **Mock Services**: Uses mock checkout service
- **Component Testing**: Tests wrapped component
- **Integration Testing**: Tests HOC integration

### Mock Integration

```typescript
const mockCheckoutService = {
    loadCheckout: jest.fn(),
    // ... other methods
};
```

**Mock Strategy:**
- **Service Mocking**: Mocks checkout service
- **State Mocking**: Mocks checkout state
- **Method Mocking**: Mocks service methods
- **Integration Testing**: Tests HOC integration

## Migration Considerations

### Legacy to Modern

```typescript
// Legacy HOC usage
const MyComponent = withCheckout(({ checkoutService, checkoutState }) => {
    // Component implementation
});

// Modern hook usage (alternative)
const MyComponent = () => {
    const { checkoutService, checkoutState } = useCheckout();
    // Component implementation
};
```

**Migration Strategy:**
- **Legacy Support**: Maintains legacy HOC support
- **Modern Alternatives**: Provides modern alternatives
- **Gradual Migration**: Enables gradual migration
- **Backward Compatibility**: Maintains compatibility

### HOC to Hooks

```typescript
// HOC pattern
const MyComponent = withCheckout(({ checkoutService, checkoutState }) => {
    // Component implementation
});

// Hook pattern
const MyComponent = () => {
    const { checkoutService, checkoutState } = useCheckout();
    // Component implementation
};
```

**Migration Strategy:**
- **Pattern Support**: Supports both patterns
- **Gradual Migration**: Enables gradual migration
- **Type Safety**: Maintains type safety
- **Performance**: Optimizes performance

## Source Files

- **Main Implementation**: `packages/core/src/app/checkout/withCheckout.tsx`
- **Test File**: `packages/core/src/app/checkout/withCheckout.test.tsx`
- **Dependencies**: Legacy HOC system, Checkout context, Payment integration API
