# API Reference - Checkout System

## 📚 Complete Component Documentation

### CheckoutApp

Main application wrapper that provides checkout context and error handling.

```typescript
interface CheckoutAppProps {
  checkoutId: string;           // Required: Your checkout ID
  containerId: string;          // Required: DOM container ID
  publicPath?: string;          // Optional: Public asset path
  sentryConfig?: BrowserOptions; // Optional: Error tracking config
  sentrySampleRate?: number;    // Optional: Error sampling rate
}
```

**Usage:**
```typescript
<CheckoutApp
  checkoutId="your-checkout-id"
  containerId="checkout-container"
  publicPath="/assets/"
  sentryConfig={{ dsn: 'your-sentry-dsn' }}
  sentrySampleRate={0.1}
/>
```

**Features:**
- Provider hierarchy setup
- Error boundary integration
- Sentry error tracking
- Public path configuration

### CheckoutStep

Individual step management with animation and focus handling.

```typescript
interface CheckoutStepProps {
  children?: ReactNode;         // Step content
  heading?: ReactNode;          // Step heading
  isActive?: boolean;           // Whether step is active
  isBusy: boolean;              // Whether step is busy/loading
  isComplete?: boolean;         // Whether step is complete
  isEditable?: boolean;         // Whether step can be edited
  suggestion?: ReactNode;       // Step suggestion
  summary?: ReactNode;          // Step summary
  type: CheckoutStepType;       // Step type
  onExpanded?(step: CheckoutStepType): void;  // Step expanded callback
  onEdit?(step: CheckoutStepType): void;      // Step edit callback
}
```

**Usage:**
```typescript
<CheckoutStep
  isActive={true}
  isBusy={false}
  isComplete={false}
  isEditable={true}
  type={CheckoutStepType.Customer}
  onExpanded={handleStepExpanded}
  onEdit={handleStepEdit}
>
  <CustomerForm />
</CheckoutStep>
```

**Features:**
- Animation lifecycle management
- Focus management
- Scroll positioning
- Mobile optimization

### CheckoutStepHeader

Step navigation controls and user interaction.

```typescript
interface CheckoutStepHeaderProps {
  heading: ReactNode;           // Step heading
  isActive?: boolean;           // Whether step is active
  isComplete?: boolean;         // Whether step is complete
  isEditable?: boolean;         // Whether step can be edited
  summary?: ReactNode;          // Step summary
  type: CheckoutStepType;       // Step type
  onEdit?(type: CheckoutStepType): void;  // Edit callback
}
```

**Usage:**
```typescript
<CheckoutStepHeader
  heading="Customer Information"
  isActive={true}
  isComplete={false}
  isEditable={true}
  type={CheckoutStepType.Customer}
  onEdit={handleEdit}
/>
```

**Features:**
- Clickable step headers
- Visual state indicators
- Edit button functionality
- Accessibility support

### CheckoutSuggestion

Payment method suggestions and custom checkout integration.

```typescript
interface CheckoutSuggestionProps {
  onUnhandledError?(error: Error): void;  // Error handling callback
}

interface WithCheckoutSuggestionsProps {
  isExecutingPaymentMethodCheckout: boolean;  // Checkout execution state
  providerWithCustomCheckout?: string;        // Custom checkout provider
  deinitializeCustomer(options: CustomerRequestOptions): Promise<CheckoutSelectors>;
  executePaymentMethodCheckout(options: ExecutePaymentMethodCheckoutOptions): Promise<CheckoutSelectors>;
  initializeCustomer(options: CustomerInitializeOptions): Promise<CheckoutSelectors>;
}
```

**Usage:**
```typescript
<CheckoutSuggestion
  onUnhandledError={handleError}
/>
```

**Features:**
- Payment method detection
- Custom checkout integration
- Analytics tracking
- Error handling

## 🎯 Step Types

### CheckoutStepType Enum

```typescript
enum CheckoutStepType {
  Billing = 'billing',
  Customer = 'customer',
  Payment = 'payment',
  Shipping = 'shipping',
  // Custom POA steps:
  OrderDetails = 'order-details',
  BillingAndPayment = 'billing-and-payment',
}
```

### CheckoutStepStatus Interface

```typescript
interface CheckoutStepStatus {
  isActive: boolean;      // Whether the step is currently active
  isBusy: boolean;        // Whether the step is in a busy/loading state
  isComplete: boolean;    // Whether the step is completed
  isEditable: boolean;    // Whether the step can be edited
  isRequired: boolean;    // Whether the step is required
  type: CheckoutStepType; // The type of the step
}
```

## 🔧 Hooks and Utilities

### withCheckout HOC

Higher-Order Component for checkout context injection.

```typescript
const withCheckout = createMappableInjectHoc(CheckoutContext, {
  displayNamePrefix: 'WithCheckout',
});

// Usage
const MyComponent = withCheckout(mapToCheckoutProps)(Component);
```

### useAnalytics Hook

Analytics tracking hook.

```typescript
const { analyticsTracker } = useAnalytics();

// Usage
analyticsTracker.checkoutStepView(stepType);
analyticsTracker.customerSuggestionExecute();
```

### Checkout Support

Feature support checking interface.

```typescript
interface CheckoutSupport {
  isSupported(...ids: string[]): boolean;
}

// Usage
const support = new CheckoutSupport();
if (support.isSupported('feature1', 'feature2')) {
  // Use supported features
}
```

## 📊 State Management

### Checkout State

```typescript
interface CheckoutState {
  data: {
    getCheckout(): Checkout | undefined;
    getCustomer(): Customer | undefined;
    getBillingAddress(): Address | undefined;
    getShippingAddress(): Address | undefined;
    getConsignments(): Consignment[] | undefined;
    getCart(): Cart | undefined;
    getConfig(): Config | undefined;
    getOrder(): Order | undefined;
  };
  statuses: {
    isLoadingCheckout(): boolean;
    isPending(): boolean;
    isSubmittingOrder(): boolean;
  };
  errors: {
    getSubmitOrderError(): Error | undefined;
  };
}
```

### Step Status Calculation

```typescript
const getCheckoutStepStatuses = createSelector(
  getCustomerStepStatus,
  getShippingStepStatus,
  getBillingStepStatus,
  getPaymentStepStatus,
  getOrderSubmitStatus,
  (customerStep, shippingStep, billingStep, paymentStep, orderStatus) => {
    // Step status calculation logic
  }
);
```

## 🎨 Styling and Theming

### CSS Classes

```css
/* Main checkout container */
.checkout-step {
  /* Step styling */
}

.checkout-step--active {
  /* Active step styling */
}

.checkout-step--complete {
  /* Complete step styling */
}

/* Step header */
.stepHeader {
  /* Header styling */
}

.stepHeader--active {
  /* Active header styling */
}

/* Step content */
.checkout-view-content {
  /* Content styling */
}

.checkout-view-content-enter {
  /* Enter animation */
}

.checkout-view-content-exit {
  /* Exit animation */
}
```

### Responsive Design

```css
/* Mobile-first responsive design */
.checkout-step {
  padding: 10px;
}

@media (min-width: 768px) {
  .checkout-step {
    padding: 20px;
  }
}

@media (min-width: 1024px) {
  .checkout-step {
    padding: 30px;
  }
}
```

## 🔍 Validation and Error Handling

### Address Validation

```typescript
const isValidAddress = (address: Address, fields: AddressField[]): boolean => {
  // Address validation logic
};

// Usage
const isBillingValid = isValidAddress(billingAddress, billingFields);
const isShippingValid = isValidAddress(shippingAddress, shippingFields);
```

### Payment Method Validation

```typescript
const validatePaymentMethod = (method: string, data: any): ValidationResult => {
  switch (method) {
    case 'credit-card':
      return validateCreditCard(data);
    case 'paypal':
      return validatePayPal(data);
    default:
      return { isValid: false, error: 'Unknown payment method' };
  }
};
```

### Error Handling

```typescript
const handleCheckoutError = (error: Error) => {
  console.error('Checkout error:', error);
  
  if (error.code === 'NETWORK_ERROR') {
    showError('Network error. Please check your connection.');
  } else if (error.code === 'VALIDATION_ERROR') {
    showError('Please check your information and try again.');
  } else {
    showError('An error occurred. Please try again.');
  }
};
```

## 📱 Mobile and Accessibility

### Mobile Optimization

```typescript
const isMobileView = (): boolean => {
  return window.innerWidth < 768;
};

// Usage
const delay = isMobileView() ? 0 : getTransitionDelay();
```

### Accessibility

```typescript
// ARIA attributes
<div
  role="tabpanel"
  aria-labelledby="step-header"
  aria-busy={isBusy}
  tabIndex={isActive ? 0 : -1}
>
  {/* Step content */}
</div>

// Focus management
const focusStep = (stepElement: HTMLElement) => {
  const firstInput = stepElement.querySelector('input, select, textarea');
  if (firstInput) {
    firstInput.focus();
  }
};
```

## 🔗 Integration Points

### Analytics Integration

```typescript
// Event tracking
analyticsTracker.checkoutStepView(stepType);
analyticsTracker.checkoutStepComplete(stepType);
analyticsTracker.customerSuggestionExecute();

// Custom events
analyticsTracker.track('custom_event', {
  eventName: 'step_validation_failed',
  stepType: 'customer',
  error: 'email_required'
});
```

### Error Tracking

```typescript
// Sentry integration
const errorLogger = createErrorLogger(
  { sentry: sentryConfig },
  {
    errorTypes: ['UnrecoverableError'],
    publicPath: publicPath,
    sampleRate: sentrySampleRate
  }
);
```

### Performance Monitoring

```typescript
// Performance tracking
const trackPerformance = (metric: string, value: number) => {
  analyticsTracker.track('performance_metric', {
    metric,
    value,
    timestamp: Date.now()
  });
};
```

## 🔗 Related Topics

- [Quick Start](./QUICK_START.md) - Basic setup and configuration
- [Common Tasks](./COMMON_TASKS.md) - How to do specific things
- [Troubleshooting](./TROUBLESHOOTING.md) - Common issues and solutions
