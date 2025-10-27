# Examples - Checkout System

## 🚀 Real-World Usage Examples

### Basic Checkout Integration

#### Simple Checkout Setup
```typescript
import React from 'react';
import { CheckoutApp } from '@bigcommerce/checkout';

const BasicCheckout = () => {
  const handleComplete = (order) => {
    console.log('Order completed:', order);
    window.location.href = `/order-confirmation/${order.id}`;
  };

  const handleError = (error) => {
    console.error('Checkout error:', error);
    alert('Checkout failed. Please try again.');
  };

  return (
    <div className="checkout-container">
      <h1>Complete Your Purchase</h1>
      <CheckoutApp
        checkoutId="your-checkout-id"
        containerId="checkout-container"
        onComplete={handleComplete}
        onError={handleError}
      />
    </div>
  );
};

export default BasicCheckout;
```

#### Checkout with Custom Styling
```typescript
import React from 'react';
import { CheckoutApp } from '@bigcommerce/checkout';
import './Checkout.css';

const StyledCheckout = () => {
  return (
    <div className="custom-checkout">
      <div className="checkout-header">
        <h1>Secure Checkout</h1>
        <div className="security-badges">
          <span>🔒 SSL Secured</span>
          <span>💳 PCI Compliant</span>
        </div>
      </div>
      
      <CheckoutApp
        checkoutId="your-checkout-id"
        containerId="checkout-container"
        publicPath="/assets/"
      />
      
      <div className="checkout-footer">
        <p>Need help? <a href="/support">Contact Support</a></p>
      </div>
    </div>
  );
};

export default StyledCheckout;
```

### Advanced Checkout Features

#### Checkout with Analytics
```typescript
import React, { useEffect } from 'react';
import { CheckoutApp } from '@bigcommerce/checkout';
import { useAnalytics } from '@bigcommerce/checkout/analytics';

const AnalyticsCheckout = () => {
  const analytics = useAnalytics();

  useEffect(() => {
    // Track checkout start
    analytics.track('checkout_started', {
      checkoutId: 'your-checkout-id',
      timestamp: Date.now()
    });
  }, []);

  const handleStepComplete = (stepType) => {
    analytics.track('checkout_step_completed', {
      step: stepType,
      timestamp: Date.now()
    });
  };

  const handleComplete = (order) => {
    analytics.track('checkout_completed', {
      orderId: order.id,
      total: order.total,
      timestamp: Date.now()
    });
    
    window.location.href = `/order-confirmation/${order.id}`;
  };

  return (
    <CheckoutApp
      checkoutId="your-checkout-id"
      containerId="checkout-container"
      onStepComplete={handleStepComplete}
      onComplete={handleComplete}
    />
  );
};

export default AnalyticsCheckout;
```

#### Checkout with Error Handling
```typescript
import React, { useState } from 'react';
import { CheckoutApp } from '@bigcommerce/checkout';

const ErrorHandlingCheckout = () => {
  const [error, setError] = useState(null);
  const [retryCount, setRetryCount] = useState(0);

  const handleError = (error) => {
    console.error('Checkout error:', error);
    setError(error);
    
    // Track error
    analytics.track('checkout_error', {
      error: error.message,
      code: error.code,
      retryCount
    });
  };

  const handleRetry = () => {
    setError(null);
    setRetryCount(prev => prev + 1);
    
    // Force re-render of checkout
    window.location.reload();
  };

  if (error) {
    return (
      <div className="error-container">
        <h2>Checkout Error</h2>
        <p>{error.message}</p>
        <button onClick={handleRetry}>
          Try Again {retryCount > 0 && `(${retryCount})`}
        </button>
        <p>
          <a href="/support">Contact Support</a> if the problem persists.
        </p>
      </div>
    );
  }

  return (
    <CheckoutApp
      checkoutId="your-checkout-id"
      containerId="checkout-container"
      onError={handleError}
    />
  );
};

export default ErrorHandlingCheckout;
```

### Custom Step Implementation

#### Custom Customer Step
```typescript
import React, { useState, useEffect } from 'react';
import { CheckoutStepType } from '@bigcommerce/checkout';

const CustomCustomerStep = ({ isActive, onComplete, onEdit }) => {
  const [formData, setFormData] = useState({
    email: '',
    firstName: '',
    lastName: '',
    phone: ''
  });
  const [errors, setErrors] = useState({});
  const [isValid, setIsValid] = useState(false);

  useEffect(() => {
    validateForm();
  }, [formData]);

  const validateForm = () => {
    const newErrors = {};
    
    if (!formData.email) {
      newErrors.email = 'Email is required';
    } else if (!isValidEmail(formData.email)) {
      newErrors.email = 'Invalid email format';
    }
    
    if (!formData.firstName) {
      newErrors.firstName = 'First name is required';
    }
    
    if (!formData.lastName) {
      newErrors.lastName = 'Last name is required';
    }
    
    setErrors(newErrors);
    setIsValid(Object.keys(newErrors).length === 0);
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    if (isValid) {
      onComplete(formData);
    }
  };

  const handleInputChange = (field, value) => {
    setFormData(prev => ({
      ...prev,
      [field]: value
    }));
  };

  return (
    <div className="custom-customer-step">
      <h2>Customer Information</h2>
      <form onSubmit={handleSubmit}>
        <div className="form-group">
          <label htmlFor="email">Email Address *</label>
          <input
            id="email"
            type="email"
            value={formData.email}
            onChange={(e) => handleInputChange('email', e.target.value)}
            className={errors.email ? 'error' : ''}
          />
          {errors.email && <span className="error-message">{errors.email}</span>}
        </div>
        
        <div className="form-row">
          <div className="form-group">
            <label htmlFor="firstName">First Name *</label>
            <input
              id="firstName"
              type="text"
              value={formData.firstName}
              onChange={(e) => handleInputChange('firstName', e.target.value)}
              className={errors.firstName ? 'error' : ''}
            />
            {errors.firstName && <span className="error-message">{errors.firstName}</span>}
          </div>
          
          <div className="form-group">
            <label htmlFor="lastName">Last Name *</label>
            <input
              id="lastName"
              type="text"
              value={formData.lastName}
              onChange={(e) => handleInputChange('lastName', e.target.value)}
              className={errors.lastName ? 'error' : ''}
            />
            {errors.lastName && <span className="error-message">{errors.lastName}</span>}
          </div>
        </div>
        
        <div className="form-group">
          <label htmlFor="phone">Phone Number</label>
          <input
            id="phone"
            type="tel"
            value={formData.phone}
            onChange={(e) => handleInputChange('phone', e.target.value)}
          />
        </div>
        
        <button 
          type="submit" 
          disabled={!isValid}
          className="submit-button"
        >
          Continue to Shipping
        </button>
      </form>
    </div>
  );
};

const isValidEmail = (email) => {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return emailRegex.test(email);
};

export default CustomCustomerStep;
```

#### Custom Payment Step
```typescript
import React, { useState, useEffect } from 'react';
import { CheckoutStepType } from '@bigcommerce/checkout';

const CustomPaymentStep = ({ isActive, onComplete, paymentMethods }) => {
  const [selectedMethod, setSelectedMethod] = useState('');
  const [paymentData, setPaymentData] = useState({});
  const [errors, setErrors] = useState({});
  const [isProcessing, setIsProcessing] = useState(false);

  const handleMethodSelect = (methodId) => {
    setSelectedMethod(methodId);
    setPaymentData({});
    setErrors({});
  };

  const handlePaymentDataChange = (field, value) => {
    setPaymentData(prev => ({
      ...prev,
      [field]: value
    }));
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    setIsProcessing(true);
    
    try {
      const result = await processPayment({
        methodId: selectedMethod,
        paymentData
      });
      
      if (result.success) {
        onComplete(result);
      } else {
        setErrors({ general: result.error });
      }
    } catch (error) {
      setErrors({ general: error.message });
    } finally {
      setIsProcessing(false);
    }
  };

  const processPayment = async (paymentInfo) => {
    // Simulate payment processing
    return new Promise((resolve) => {
      setTimeout(() => {
        resolve({ success: true, transactionId: 'txn_123' });
      }, 2000);
    });
  };

  return (
    <div className="custom-payment-step">
      <h2>Payment Information</h2>
      
      <form onSubmit={handleSubmit}>
        <div className="payment-methods">
          <h3>Select Payment Method</h3>
          {paymentMethods.map(method => (
            <div 
              key={method.id}
              className={`payment-method ${selectedMethod === method.id ? 'selected' : ''}`}
              onClick={() => handleMethodSelect(method.id)}
            >
              <input
                type="radio"
                name="paymentMethod"
                value={method.id}
                checked={selectedMethod === method.id}
                onChange={() => handleMethodSelect(method.id)}
              />
              <label>{method.name}</label>
            </div>
          ))}
        </div>
        
        {selectedMethod === 'credit-card' && (
          <div className="credit-card-form">
            <div className="form-group">
              <label>Card Number</label>
              <input
                type="text"
                placeholder="1234 5678 9012 3456"
                onChange={(e) => handlePaymentDataChange('cardNumber', e.target.value)}
              />
            </div>
            
            <div className="form-row">
              <div className="form-group">
                <label>Expiry Date</label>
                <input
                  type="text"
                  placeholder="MM/YY"
                  onChange={(e) => handlePaymentDataChange('expiryDate', e.target.value)}
                />
              </div>
              
              <div className="form-group">
                <label>CVV</label>
                <input
                  type="text"
                  placeholder="123"
                  onChange={(e) => handlePaymentDataChange('cvv', e.target.value)}
                />
              </div>
            </div>
            
            <div className="form-group">
              <label>Cardholder Name</label>
              <input
                type="text"
                placeholder="John Doe"
                onChange={(e) => handlePaymentDataChange('cardholderName', e.target.value)}
              />
            </div>
          </div>
        )}
        
        {errors.general && (
          <div className="error-message">{errors.general}</div>
        )}
        
        <button 
          type="submit" 
          disabled={!selectedMethod || isProcessing}
          className="submit-button"
        >
          {isProcessing ? 'Processing...' : 'Complete Order'}
        </button>
      </form>
    </div>
  );
};

export default CustomPaymentStep;
```

### Integration Examples

#### React Router Integration
```typescript
import React from 'react';
import { BrowserRouter, Routes, Route, useParams } from 'react-router-dom';
import { CheckoutApp } from '@bigcommerce/checkout';

const CheckoutPage = () => {
  const { checkoutId } = useParams();
  
  return (
    <div className="checkout-page">
      <CheckoutApp
        checkoutId={checkoutId}
        containerId="checkout-container"
        onComplete={(order) => {
          // Redirect to success page
          window.location.href = `/order-confirmation/${order.id}`;
        }}
      />
    </div>
  );
};

const App = () => {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/checkout/:checkoutId" element={<CheckoutPage />} />
        <Route path="/order-confirmation/:orderId" element={<OrderConfirmation />} />
      </Routes>
    </BrowserRouter>
  );
};

export default App;
```

#### Redux Integration
```typescript
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { CheckoutApp } from '@bigcommerce/checkout';

const CheckoutContainer = () => {
  const dispatch = useDispatch();
  const checkoutState = useSelector(state => state.checkout);
  
  const handleStepComplete = (stepType, data) => {
    dispatch({
      type: 'CHECKOUT_STEP_COMPLETE',
      payload: { stepType, data }
    });
  };
  
  const handleComplete = (order) => {
    dispatch({
      type: 'CHECKOUT_COMPLETE',
      payload: order
    });
  };
  
  return (
    <CheckoutApp
      checkoutId={checkoutState.checkoutId}
      containerId="checkout-container"
      onStepComplete={handleStepComplete}
      onComplete={handleComplete}
    />
  );
};

export default CheckoutContainer;
```

### Testing Examples

#### Unit Test Example
```typescript
import React from 'react';
import { render, fireEvent, waitFor } from '@testing-library/react';
import { CheckoutStep } from './CheckoutStep';

describe('CheckoutStep', () => {
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
      <CheckoutStep 
        isActive={false} 
        isEditable={true} 
        onEdit={onEdit}
        type="customer"
      >
        <div>Customer Step</div>
      </CheckoutStep>
    );
    
    fireEvent.click(getByText('Edit'));
    expect(onEdit).toHaveBeenCalledWith('customer');
  });
  
  test('shows loading state when busy', () => {
    const { getByText } = render(
      <CheckoutStep isActive={true} isBusy={true} type="customer">
        <div>Customer Step</div>
      </CheckoutStep>
    );
    
    expect(getByText('Customer Step')).toHaveAttribute('aria-busy', 'true');
  });
});
```

#### Integration Test Example
```typescript
import React from 'react';
import { render, fireEvent, waitFor } from '@testing-library/react';
import { CheckoutApp } from './CheckoutApp';

describe('CheckoutApp Integration', () => {
  test('complete checkout flow', async () => {
    const { getByText, getByLabelText } = render(
      <CheckoutApp
        checkoutId="test-checkout-id"
        containerId="checkout-container"
      />
    );
    
    // Wait for checkout to load
    await waitFor(() => {
      expect(getByText('Customer Information')).toBeInTheDocument();
    });
    
    // Fill customer information
    fireEvent.change(getByLabelText('Email'), { 
      target: { value: 'test@example.com' } 
    });
    
    fireEvent.change(getByLabelText('First Name'), { 
      target: { value: 'John' } 
    });
    
    fireEvent.change(getByLabelText('Last Name'), { 
      target: { value: 'Doe' } 
    });
    
    // Continue to shipping
    fireEvent.click(getByText('Continue to Shipping'));
    
    // Wait for shipping step
    await waitFor(() => {
      expect(getByText('Shipping Information')).toBeInTheDocument();
    });
    
    // Complete shipping
    fireEvent.change(getByLabelText('Address'), { 
      target: { value: '123 Main St' } 
    });
    
    fireEvent.change(getByLabelText('City'), { 
      target: { value: 'Anytown' } 
    });
    
    fireEvent.change(getByLabelText('State'), { 
      target: { value: 'CA' } 
    });
    
    fireEvent.change(getByLabelText('ZIP Code'), { 
      target: { value: '12345' } 
    });
    
    // Continue to payment
    fireEvent.click(getByText('Continue to Payment'));
    
    // Wait for payment step
    await waitFor(() => {
      expect(getByText('Payment Information')).toBeInTheDocument();
    });
    
    // Complete payment
    fireEvent.change(getByLabelText('Card Number'), { 
      target: { value: '4111111111111111' } 
    });
    
    fireEvent.change(getByLabelText('Expiry Date'), { 
      target: { value: '12/25' } 
    });
    
    fireEvent.change(getByLabelText('CVV'), { 
      target: { value: '123' } 
    });
    
    // Complete order
    fireEvent.click(getByText('Complete Order'));
    
    // Wait for completion
    await waitFor(() => {
      expect(getByText('Order Complete')).toBeInTheDocument();
    });
  });
});
```

## 🔗 Related Topics

- [Quick Start](./QUICK_START.md) - Basic setup and configuration
- [Common Tasks](./COMMON_TASKS.md) - How to do specific things
- [API Reference](./API_REFERENCE.md) - Complete component documentation
- [Troubleshooting](./TROUBLESHOOTING.md) - Common issues and solutions
