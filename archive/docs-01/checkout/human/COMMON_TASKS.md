# Common Tasks - Checkout System

## 🎯 How to Do Specific Things

### Adding a Custom Step

#### 1. Create Step Component
```typescript
import React from 'react';
import { CheckoutStepType } from '@bigcommerce/checkout';

const CustomStep = ({ isActive, onComplete, onEdit }) => {
  const [isValid, setIsValid] = useState(false);
  
  const handleComplete = () => {
    if (isValid) {
      onComplete();
    }
  };
  
  return (
    <div className="custom-step">
      <h3>Custom Step</h3>
      <input 
        type="text" 
        onChange={(e) => setIsValid(e.target.value.length > 0)}
        placeholder="Enter custom data"
      />
      <button onClick={handleComplete} disabled={!isValid}>
        Complete Step
      </button>
    </div>
  );
};
```

#### 2. Add to Step Configuration
```typescript
const stepConfig = {
  type: CheckoutStepType.Custom,
  component: CustomStep,
  isRequired: true,
  validation: (data) => data.customField && data.customField.length > 0
};
```

#### 3. Handle Step Events
```typescript
const handleStepComplete = (stepType, data) => {
  console.log(`Step ${stepType} completed:`, data);
  // Update checkout state
};

const handleStepEdit = (stepType) => {
  console.log(`Editing step: ${stepType}`);
  // Navigate to step
};
```

### Customizing Step Validation

#### 1. Basic Validation
```typescript
const validateCustomerStep = (customerData) => {
  const errors = {};
  
  if (!customerData.email) {
    errors.email = 'Email is required';
  } else if (!isValidEmail(customerData.email)) {
    errors.email = 'Invalid email format';
  }
  
  if (!customerData.firstName) {
    errors.firstName = 'First name is required';
  }
  
  return {
    isValid: Object.keys(errors).length === 0,
    errors
  };
};
```

#### 2. Async Validation
```typescript
const validateEmailAsync = async (email) => {
  try {
    const response = await fetch('/api/validate-email', {
      method: 'POST',
      body: JSON.stringify({ email })
    });
    
    const result = await response.json();
    return result.isValid;
  } catch (error) {
    console.error('Email validation error:', error);
    return false;
  }
};
```

#### 3. Cross-Step Validation
```typescript
const validateBillingMatchesShipping = (billing, shipping) => {
  return billing.address1 === shipping.address1 &&
         billing.city === shipping.city &&
         billing.postalCode === shipping.postalCode;
};
```

### Handling Errors

#### 1. Error Boundaries
```typescript
class CheckoutErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }
  
  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }
  
  componentDidCatch(error, errorInfo) {
    console.error('Checkout error:', error, errorInfo);
    // Send to error tracking service
  }
  
  render() {
    if (this.state.hasError) {
      return (
        <div className="error-boundary">
          <h2>Something went wrong</h2>
          <p>Please refresh the page and try again.</p>
          <button onClick={() => window.location.reload()}>
            Refresh Page
          </button>
        </div>
      );
    }
    
    return this.props.children;
  }
}
```

#### 2. Step Error Handling
```typescript
const StepWithErrorHandling = ({ onError }) => {
  const [error, setError] = useState(null);
  
  const handleStepError = (error) => {
    setError(error);
    onError(error);
  };
  
  const retry = () => {
    setError(null);
    // Retry step logic
  };
  
  return (
    <div>
      {error ? (
        <div className="step-error">
          <p>Error: {error.message}</p>
          <button onClick={retry}>Retry</button>
        </div>
      ) : (
        <StepContent />
      )}
    </div>
  );
};
```

#### 3. Network Error Handling
```typescript
const handleNetworkError = (error) => {
  if (error.code === 'NETWORK_ERROR') {
    // Show network error message
    showNotification('Network error. Please check your connection.');
  } else if (error.code === 'TIMEOUT') {
    // Show timeout message
    showNotification('Request timed out. Please try again.');
  } else {
    // Show generic error
    showNotification('An error occurred. Please try again.');
  }
};
```

### Adding Analytics

#### 1. Basic Tracking
```typescript
import { useAnalytics } from '@bigcommerce/checkout/analytics';

const CheckoutWithAnalytics = () => {
  const analytics = useAnalytics();
  
  useEffect(() => {
    analytics.track('checkout_started');
  }, []);
  
  const handleStepComplete = (stepType) => {
    analytics.track('checkout_step_completed', {
      step: stepType,
      timestamp: Date.now()
    });
  };
  
  return (
    <CheckoutApp
      checkoutId="123"
      containerId="checkout"
      onStepComplete={handleStepComplete}
    />
  );
};
```

#### 2. Custom Events
```typescript
const trackCustomEvent = (eventName, properties) => {
  analytics.track(eventName, {
    ...properties,
    checkoutId: '123',
    timestamp: Date.now()
  });
};

// Usage
trackCustomEvent('custom_field_filled', {
  fieldName: 'special_instructions',
  value: 'Please leave at door'
});
```

#### 3. Error Tracking
```typescript
const trackError = (error, context) => {
  analytics.track('checkout_error', {
    error: error.message,
    stack: error.stack,
    context,
    timestamp: Date.now()
  });
};
```

### Customizing UI

#### 1. Custom Styling
```css
/* Override checkout styles */
.checkout-step {
  border: 2px solid #007bff;
  border-radius: 8px;
  padding: 20px;
}

.checkout-step--active {
  border-color: #28a745;
  box-shadow: 0 0 10px rgba(40, 167, 69, 0.3);
}

.step-header {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  padding: 15px;
  border-radius: 5px;
}
```

#### 2. Custom Components
```typescript
const CustomStepHeader = ({ title, isComplete, onEdit }) => (
  <div className="custom-step-header">
    <div className="step-icon">
      {isComplete ? '✓' : '○'}
    </div>
    <h3 className="step-title">{title}</h3>
    {onEdit && (
      <button className="edit-button" onClick={onEdit}>
        Edit
      </button>
    )}
  </div>
);
```

#### 3. Responsive Design
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

### Working with Payment Methods

#### 1. Adding Custom Payment Method
```typescript
const CustomPaymentMethod = ({ onSelect, isSelected }) => (
  <div 
    className={`payment-method ${isSelected ? 'selected' : ''}`}
    onClick={() => onSelect('custom-payment')}
  >
    <input 
      type="radio" 
      checked={isSelected}
      onChange={() => onSelect('custom-payment')}
    />
    <label>Custom Payment</label>
  </div>
);
```

#### 2. Payment Method Validation
```typescript
const validatePaymentMethod = (method, data) => {
  switch (method) {
    case 'credit-card':
      return validateCreditCard(data);
    case 'paypal':
      return validatePayPal(data);
    case 'custom-payment':
      return validateCustomPayment(data);
    default:
      return { isValid: false, error: 'Unknown payment method' };
  }
};
```

#### 3. Payment Processing
```typescript
const processPayment = async (paymentData) => {
  try {
    const response = await fetch('/api/process-payment', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(paymentData)
    });
    
    const result = await response.json();
    
    if (result.success) {
      return { success: true, transactionId: result.transactionId };
    } else {
      throw new Error(result.error);
    }
  } catch (error) {
    return { success: false, error: error.message };
  }
};
```

### Testing Checkout

#### 1. Unit Tests
```typescript
import { render, fireEvent } from '@testing-library/react';
import { CheckoutStep } from './CheckoutStep';

test('renders step when active', () => {
  const { getByText } = render(
    <CheckoutStep isActive={true} type="customer">
      <div>Customer Step</div>
    </CheckoutStep>
  );
  
  expect(getByText('Customer Step')).toBeInTheDocument();
});

test('calls onEdit when edit button clicked', () => {
  const onEdit = jest.fn();
  const { getByText } = render(
    <CheckoutStep isActive={false} isEditable={true} onEdit={onEdit}>
      <div>Customer Step</div>
    </CheckoutStep>
  );
  
  fireEvent.click(getByText('Edit'));
  expect(onEdit).toHaveBeenCalledWith('customer');
});
```

#### 2. Integration Tests
```typescript
test('complete checkout flow', async () => {
  const { getByText, getByLabelText } = render(<CheckoutApp />);
  
  // Fill customer information
  fireEvent.change(getByLabelText('Email'), { 
    target: { value: 'test@example.com' } 
  });
  
  // Complete shipping
  fireEvent.click(getByText('Continue to Shipping'));
  
  // Complete payment
  fireEvent.click(getByText('Complete Order'));
  
  // Verify completion
  expect(getByText('Order Complete')).toBeInTheDocument();
});
```

## 🔗 Related Topics

- [API Reference](./API_REFERENCE.md) - Complete component documentation
- [Troubleshooting](./TROUBLESHOOTING.md) - Common issues and solutions
- [Examples](./EXAMPLES.md) - Real-world usage examples
