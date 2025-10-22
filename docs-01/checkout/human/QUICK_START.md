# Quick Start - Checkout System

## 🚀 Get Started in 5 Minutes

### 1. Basic Setup
```typescript
import { CheckoutApp } from '@bigcommerce/checkout';

// Render checkout in your app
<CheckoutApp
  checkoutId="your-checkout-id"
  containerId="checkout-container"
  onComplete={handleCheckoutComplete}
/>
```

### 2. Essential Props
```typescript
interface CheckoutAppProps {
  checkoutId: string;           // Required: Your checkout ID
  containerId: string;          // Required: DOM container ID
  publicPath?: string;          // Optional: Public asset path
  sentryConfig?: BrowserOptions; // Optional: Error tracking
}
```

### 3. Handle Events
```typescript
const handleCheckoutComplete = (order) => {
  console.log('Order completed:', order);
  // Redirect to success page
  window.location.href = '/order-confirmation';
};
```

## 📋 Common Patterns

### Custom Step Validation
```typescript
const CustomStep = ({ isActive, onComplete }) => {
  const [isValid, setIsValid] = useState(false);
  
  useEffect(() => {
    if (isActive && isValid) {
      onComplete();
    }
  }, [isActive, isValid, onComplete]);
  
  return (
    <div>
      {/* Your step content */}
    </div>
  );
};
```

### Error Handling
```typescript
const CheckoutWithErrorHandling = () => {
  const [error, setError] = useState(null);
  
  const handleError = (error) => {
    console.error('Checkout error:', error);
    setError(error.message);
  };
  
  return (
    <div>
      {error && <div className="error">{error}</div>}
      <CheckoutApp
        checkoutId="123"
        containerId="checkout"
        onError={handleError}
      />
    </div>
  );
};
```

### Analytics Integration
```typescript
const CheckoutWithAnalytics = () => {
  const analytics = useAnalytics();
  
  useEffect(() => {
    analytics.track('checkout_started');
  }, []);
  
  return (
    <CheckoutApp
      checkoutId="123"
      containerId="checkout"
      onComplete={() => analytics.track('checkout_completed')}
    />
  );
};
```

## 🎯 Key Concepts

### Checkout Steps
The checkout system uses a step-based approach:

1. **Customer** - Email and authentication
2. **Shipping** - Address and shipping method
3. **Billing** - Billing address and information
4. **Payment** - Payment method selection
5. **Order Details** - Order summary and confirmation

### Step States
Each step has these states:
- `isActive` - Currently visible to user
- `isComplete` - Step validation passed
- `isEditable` - User can modify step
- `isRequired` - Step must be completed

### POA Checkout
For Point of Access checkout:
- **Billing and Payment** combined into single step
- **Order Details** step for order summary
- **Custom step management** for specialized flows

## 🔧 Configuration

### Basic Configuration
```typescript
const config = {
  checkoutId: 'your-checkout-id',
  containerId: 'checkout-container',
  publicPath: '/assets/',
  sentryConfig: {
    dsn: 'your-sentry-dsn',
    environment: 'production'
  }
};
```

### Advanced Configuration
```typescript
const advancedConfig = {
  ...config,
  // Custom checkout settings
  checkoutSettings: {
    guestCheckoutEnabled: true,
    walletButtonsOnTop: false,
    floatingLabelEnabled: true
  },
  // Feature flags
  experiments: {
    'STRIPE-546.allow_billing_address_editing': true,
    'PROJECT-5015.manual_order.display_custom_shipping': false
  }
};
```

## 🚨 Common Issues

### Step Not Showing
**Problem**: Step doesn't appear in checkout
**Solution**: Check `isActive` prop and step dependencies

```typescript
// Make sure step is active
<CheckoutStep isActive={true} type={CheckoutStepType.Customer} />
```

### Validation Failing
**Problem**: Step validation keeps failing
**Solution**: Check step requirements and dependencies

```typescript
// Ensure all required data is present
const stepStatus = {
  isComplete: hasEmail && hasAddress,
  isEditable: true,
  isRequired: true
};
```

### Animation Issues
**Problem**: Step transitions not smooth
**Solution**: Check CSS transitions and timing

```css
.checkout-view-content {
  transition: all 0.3s ease-in-out;
}
```

## 📚 Next Steps

1. **Read Common Tasks** - Learn how to do specific things
2. **Check API Reference** - Complete component and prop documentation
3. **See Examples** - Real-world usage examples
4. **Troubleshooting** - Common problems and solutions

## 🔗 Quick Links

- [Common Tasks](./COMMON_TASKS.md) - How to do specific things
- [API Reference](./API_REFERENCE.md) - Complete API documentation
- [Troubleshooting](./TROUBLESHOOTING.md) - Common issues and solutions
- [Examples](./EXAMPLES.md) - Practical usage examples
