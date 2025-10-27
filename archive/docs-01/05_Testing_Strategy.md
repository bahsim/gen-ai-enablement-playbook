# Testing Strategy

## Overview
The BigCommerce POA React checkout project implements a comprehensive multi-layered testing strategy to ensure code quality, reliability, and maintainability. This document outlines the testing philosophy, tools, and practices used throughout the project.

## Testing Philosophy

### 1. Test-Driven Development (TDD)
- **Write tests first** for new features and bug fixes
- **Red-Green-Refactor** cycle for development
- **Test coverage requirements** for all new code

### 2. Testing Pyramid
```
    /\
   /  \     E2E Tests (Few)
  /____\    Integration Tests (Some)
 /______\   Unit Tests (Many)
```

### 3. Testing Principles
- **Fast**: Unit tests should run in milliseconds
- **Reliable**: Tests should be deterministic and not flaky
- **Maintainable**: Tests should be easy to understand and modify
- **Comprehensive**: Cover all critical paths and edge cases

## Testing Tools and Configuration

### Jest Configuration
```javascript
// packages/core/jest.config.js
module.exports = {
    displayName: 'core',
    preset: '../../jest.preset.js',
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

### Testing Libraries
- **Jest**: Primary testing framework
- **Testing Library**: Component testing utilities
- **Playwright**: E2E testing
- **Polly.js**: HTTP request mocking
- **MSW**: API mocking

## Unit Testing

### Component Testing
```typescript
// Example component test
import { render, screen, fireEvent } from '@testing-library/react';
import { Checkout } from './Checkout';

describe('Checkout Component', () => {
    it('should render checkout steps', () => {
        const mockProps = {
            checkoutId: 'test-123',
            containerId: 'test-container',
            // ... other props
        };

        render(<Checkout {...mockProps} />);
        
        expect(screen.getByText('Shipping')).toBeInTheDocument();
        expect(screen.getByText('Payment')).toBeInTheDocument();
    });

    it('should handle step navigation', () => {
        render(<Checkout {...mockProps} />);
        
        const nextButton = screen.getByText('Continue');
        fireEvent.click(nextButton);
        
        expect(screen.getByText('Payment')).toHaveClass('active');
    });
});
```

### Utility Function Testing
```typescript
// Example utility test
import { getCheckoutStepStatuses } from './getCheckoutStepStatuses';

describe('getCheckoutStepStatuses', () => {
    it('should return correct step statuses', () => {
        const checkout = {
            consignments: [],
            billingAddress: null,
            payments: []
        };

        const result = getCheckoutStepStatuses(checkout);
        
        expect(result).toEqual([
            { type: 'customer', isComplete: false },
            { type: 'shipping', isComplete: false },
            { type: 'billing', isComplete: false },
            { type: 'payment', isComplete: false }
        ]);
    });
});
```

### Mocking Strategies
```typescript
// Mock external dependencies
jest.mock('@bigcommerce/checkout-sdk', () => ({
    createCheckoutService: jest.fn(() => ({
        getState: jest.fn(() => mockCheckoutState),
        subscribe: jest.fn(() => jest.fn()),
        loadCheckout: jest.fn(() => Promise.resolve(mockCheckoutSelectors))
    }))
}));

// Mock React components
jest.mock('../ui/Button', () => ({
    __esModule: true,
    default: ({ children, ...props }) => (
        <button {...props}>{children}</button>
    )
}));
```

## Integration Testing

### API Integration Tests
```typescript
// Example API integration test
import { createCheckoutService } from '@bigcommerce/checkout-sdk';

describe('Checkout API Integration', () => {
    let checkoutService: CheckoutService;

    beforeEach(() => {
        checkoutService = createCheckoutService();
    });

    it('should load checkout data', async () => {
        const checkoutId = 'test-checkout-123';
        
        const result = await checkoutService.loadCheckout(checkoutId);
        
        expect(result.data.getCheckout()).toBeDefined();
        expect(result.data.getCart()).toBeDefined();
    });

    it('should handle API errors gracefully', async () => {
        const invalidCheckoutId = 'invalid-id';
        
        await expect(
            checkoutService.loadCheckout(invalidCheckoutId)
        ).rejects.toThrow();
    });
});
```

### Payment Integration Tests
```typescript
// Example payment integration test
import { PayPalCommerceIntegration } from '@bigcommerce/checkout/paypal-commerce-integration';

describe('PayPal Commerce Integration', () => {
    it('should initialize PayPal SDK', async () => {
        const integration = new PayPalCommerceIntegration();
        
        await integration.initialize({
            clientId: 'test-client-id',
            currency: 'USD'
        });
        
        expect(integration.isInitialized()).toBe(true);
    });

    it('should process payment successfully', async () => {
        const integration = new PayPalCommerceIntegration();
        const paymentData = {
            amount: 100,
            currency: 'USD',
            paymentMethodId: 'paypalcommerce'
        };

        const result = await integration.processPayment(paymentData);
        
        expect(result.status).toBe('success');
        expect(result.transactionId).toBeDefined();
    });
});
```

## E2E Testing with Playwright

### Test Structure
```typescript
// Example E2E test
import { test, expect } from '@playwright/test';

test.describe('Checkout Flow', () => {
    test('should complete checkout successfully', async ({ page }) => {
        // Navigate to checkout
        await page.goto('/checkout');
        
        // Fill shipping address
        await page.fill('[data-testid="shipping-first-name"]', 'John');
        await page.fill('[data-testid="shipping-last-name"]', 'Doe');
        await page.fill('[data-testid="shipping-address"]', '123 Main St');
        await page.fill('[data-testid="shipping-city"]', 'New York');
        await page.selectOption('[data-testid="shipping-state"]', 'NY');
        await page.fill('[data-testid="shipping-postcode"]', '10001');
        
        // Continue to payment
        await page.click('[data-testid="continue-to-payment"]');
        
        // Fill payment information
        await page.fill('[data-testid="card-number"]', '4111111111111111');
        await page.fill('[data-testid="card-expiry"]', '12/25');
        await page.fill('[data-testid="card-cvv"]', '123');
        
        // Complete checkout
        await page.click('[data-testid="complete-order"]');
        
        // Verify order confirmation
        await expect(page.locator('[data-testid="order-confirmation"]')).toBeVisible();
        await expect(page.locator('[data-testid="order-number"]')).toBeDefined();
    });

    test('should handle payment errors', async ({ page }) => {
        await page.goto('/checkout');
        
        // Fill payment with invalid card
        await page.fill('[data-testid="card-number"]', '4000000000000002');
        await page.fill('[data-testid="card-expiry"]', '12/25');
        await page.fill('[data-testid="card-cvv"]', '123');
        
        await page.click('[data-testid="complete-order"]');
        
        // Verify error message
        await expect(page.locator('[data-testid="payment-error"]')).toBeVisible();
        await expect(page.locator('[data-testid="payment-error"]')).toContainText('card was declined');
    });
});
```

### E2E Test Configuration
```typescript
// playwright.config.ts
import { PlaywrightTestConfig } from '@playwright/test';

const config: PlaywrightTestConfig = {
    testDir: './e2e',
    timeout: 30000,
    expect: {
        timeout: 5000
    },
    use: {
        baseURL: 'http://localhost:8080',
        trace: 'on-first-retry',
        screenshot: 'only-on-failure'
    },
    projects: [
        {
            name: 'Chrome',
            use: { browserName: 'chromium' }
        },
        {
            name: 'Firefox',
            use: { browserName: 'firefox' }
        },
        {
            name: 'Safari',
            use: { browserName: 'webkit' }
        }
    ]
};

export default config;
```

## Test Coverage Requirements

### Coverage Targets
- **Statements**: 90%
- **Branches**: 85%
- **Functions**: 90%
- **Lines**: 90%

### Coverage Configuration
```javascript
// jest.config.js coverage settings
module.exports = {
    collectCoverageFrom: [
        'src/**/*.{ts,tsx}',
        '!src/**/*.d.ts',
        '!src/**/*.test.{ts,tsx}',
        '!src/**/*.spec.{ts,tsx}'
    ],
    coverageThreshold: {
        global: {
            branches: 85,
            functions: 90,
            lines: 90,
            statements: 90
        }
    }
};
```

## Performance Testing

### Bundle Size Testing
```typescript
// Example bundle size test
import { getBundleSize } from '@bigcommerce/checkout/test-utils';

describe('Bundle Size', () => {
    it('should not exceed size limits', async () => {
        const bundleSize = await getBundleSize('packages/core/dist/index.js');
        
        expect(bundleSize.gzipped).toBeLessThan(500 * 1024); // 500KB
        expect(bundleSize.minified).toBeLessThan(1500 * 1024); // 1.5MB
    });
});
```

### Performance Testing
```typescript
// Example performance test
import { measurePerformance } from '@bigcommerce/checkout/test-utils';

describe('Checkout Performance', () => {
    it('should load within performance budget', async () => {
        const metrics = await measurePerformance(() => {
            // Render checkout component
            render(<Checkout {...props} />);
        });

        expect(metrics.firstContentfulPaint).toBeLessThan(1500); // 1.5s
        expect(metrics.largestContentfulPaint).toBeLessThan(2500); // 2.5s
    });
});
```

## Testing Best Practices

### 1. Test Organization
```typescript
// Organize tests by feature
describe('Checkout Flow', () => {
    describe('Shipping Step', () => {
        describe('Address Validation', () => {
            it('should validate required fields', () => {
                // Test implementation
            });
            
            it('should handle invalid postal codes', () => {
                // Test implementation
            });
        });
    });
});
```

### 2. Test Data Management
```typescript
// Use factories for test data
import { createMockCheckout, createMockCustomer } from './test-utils';

describe('Checkout Component', () => {
    const mockCheckout = createMockCheckout({
        id: 'test-123',
        customer: createMockCustomer({
            email: 'test@example.com'
        })
    });

    it('should render customer information', () => {
        render(<Checkout checkout={mockCheckout} />);
        expect(screen.getByText('test@example.com')).toBeInTheDocument();
    });
});
```

### 3. Async Testing
```typescript
// Handle async operations properly
it('should load checkout data', async () => {
    const { findByText } = render(<Checkout />);
    
    // Wait for async operation to complete
    const shippingStep = await findByText('Shipping');
    expect(shippingStep).toBeInTheDocument();
});
```

### 4. Error Testing
```typescript
// Test error scenarios
it('should handle API errors', async () => {
    // Mock API to throw error
    jest.spyOn(checkoutService, 'loadCheckout').mockRejectedValue(
        new Error('Network error')
    );

    render(<Checkout />);
    
    const errorMessage = await screen.findByText('Unable to load checkout');
    expect(errorMessage).toBeInTheDocument();
});
```

## Continuous Integration

### Test Commands
```json
{
    "scripts": {
        "test": "nx run-many --target=test --all",
        "test:watch": "nx run-many --target=test --all --watch",
        "test:coverage": "nx run-many --target=test --all --coverage",
        "test:e2e": "npm run build && npx playwright test",
        "test:ci": "npm run test:coverage && npm run test:e2e"
    }
}
```

### CI Pipeline
```yaml
# .github/workflows/test.yml
name: Tests
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
        with:
          node-version: '22'
      - run: npm ci
      - run: npm run test:ci
      - run: npm run test:e2e
```

## Monitoring and Reporting

### Test Metrics
- **Test Execution Time**: Track test performance
- **Coverage Trends**: Monitor coverage over time
- **Flaky Test Detection**: Identify and fix unreliable tests
- **Test Failure Analysis**: Analyze common failure patterns

### Reporting Tools
- **Jest Coverage Reports**: HTML and JSON coverage reports
- **Playwright Reports**: HTML test reports with screenshots
- **CI/CD Integration**: Automated reporting in CI pipeline

## Conclusion

The testing strategy ensures high code quality and reliability through comprehensive unit, integration, and E2E testing. The multi-layered approach provides confidence in code changes while maintaining fast feedback loops for developers.

Regular review and updates of the testing strategy help maintain its effectiveness as the project evolves.
