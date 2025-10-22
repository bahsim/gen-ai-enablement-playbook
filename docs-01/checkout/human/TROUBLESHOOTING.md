# Troubleshooting - Checkout System

## 🚨 Common Issues and Solutions

### Checkout Not Loading

#### Problem: Checkout container is empty
**Symptoms:**
- Empty div where checkout should appear
- No error messages in console
- CheckoutApp renders but no content

**Solutions:**
```typescript
// 1. Check container ID exists
const container = document.getElementById('checkout-container');
if (!container) {
  console.error('Container not found');
}

// 2. Verify checkout ID is valid
const checkoutId = 'your-checkout-id';
if (!checkoutId || checkoutId === 'undefined') {
  console.error('Invalid checkout ID');
}

// 3. Check for JavaScript errors
window.addEventListener('error', (event) => {
  console.error('JavaScript error:', event.error);
});
```

#### Problem: Checkout loads but shows error
**Symptoms:**
- Error message displayed
- Console shows API errors
- Checkout state shows error

**Solutions:**
```typescript
// 1. Check network requests
fetch('/api/checkout/123')
  .then(response => {
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}: ${response.statusText}`);
    }
    return response.json();
  })
  .catch(error => console.error('API error:', error));

// 2. Verify checkout ID format
const isValidCheckoutId = /^[a-zA-Z0-9-_]+$/.test(checkoutId);
if (!isValidCheckoutId) {
  console.error('Invalid checkout ID format');
}
```

### Step Navigation Issues

#### Problem: Can't navigate between steps
**Symptoms:**
- Step headers not clickable
- Steps stuck on one step
- Navigation buttons not working

**Solutions:**
```typescript
// 1. Check step state
const stepStatus = {
  isActive: true,
  isComplete: false,
  isEditable: true,
  isRequired: true
};

// 2. Verify step dependencies
const canEditStep = (stepIndex, allSteps) => {
  const previousSteps = allSteps.slice(0, stepIndex);
  return previousSteps.every(step => step.isComplete || !step.isRequired);
};

// 3. Check step props
<CheckoutStep
  isActive={true}
  isEditable={canEditStep(index, steps)}
  onEdit={handleStepEdit}
  type={CheckoutStepType.Customer}
/>
```

#### Problem: Step validation failing
**Symptoms:**
- Step shows as incomplete
- Validation errors not clear
- Can't proceed to next step

**Solutions:**
```typescript
// 1. Check required fields
const validateStep = (stepData) => {
  const errors = {};
  
  if (!stepData.email) {
    errors.email = 'Email is required';
  }
  
  if (!stepData.firstName) {
    errors.firstName = 'First name is required';
  }
  
  return {
    isValid: Object.keys(errors).length === 0,
    errors
  };
};

// 2. Debug validation
console.log('Step data:', stepData);
console.log('Validation result:', validateStep(stepData));

// 3. Check step dependencies
const checkDependencies = (step) => {
  if (step.type === 'shipping' && !customerStep.isComplete) {
    return { isValid: false, error: 'Customer step must be completed first' };
  }
  return { isValid: true };
};
```

### Payment Issues

#### Problem: Payment method not working
**Symptoms:**
- Payment method not selectable
- Payment form not loading
- Payment processing fails

**Solutions:**
```typescript
// 1. Check payment method availability
const availableMethods = checkout.payments || [];
if (availableMethods.length === 0) {
  console.error('No payment methods available');
}

// 2. Verify payment method configuration
const paymentMethod = {
  id: 'credit-card',
  type: 'card',
  isEnabled: true,
  requiresBillingAddress: true
};

// 3. Check payment form validation
const validatePaymentForm = (formData) => {
  const errors = {};
  
  if (!formData.cardNumber) {
    errors.cardNumber = 'Card number is required';
  }
  
  if (!formData.expiryDate) {
    errors.expiryDate = 'Expiry date is required';
  }
  
  if (!formData.cvv) {
    errors.cvv = 'CVV is required';
  }
  
  return { isValid: Object.keys(errors).length === 0, errors };
};
```

#### Problem: Payment processing fails
**Symptoms:**
- Payment submission fails
- Error messages not helpful
- Payment state not updating

**Solutions:**
```typescript
// 1. Check payment data
const paymentData = {
  methodId: 'credit-card',
  paymentData: {
    ccNumber: '4111111111111111',
    ccExpiry: '12/25',
    ccCvv: '123',
    ccName: 'John Doe'
  }
};

// 2. Handle payment errors
const handlePaymentError = (error) => {
  console.error('Payment error:', error);
  
  if (error.code === 'INVALID_CARD') {
    showError('Invalid card number');
  } else if (error.code === 'EXPIRED_CARD') {
    showError('Card has expired');
  } else if (error.code === 'INSUFFICIENT_FUNDS') {
    showError('Insufficient funds');
  } else {
    showError('Payment failed. Please try again.');
  }
};

// 3. Retry payment
const retryPayment = async () => {
  try {
    const result = await submitPayment(paymentData);
    if (result.success) {
      console.log('Payment successful');
    }
  } catch (error) {
    handlePaymentError(error);
  }
};
```

### Animation and UI Issues

#### Problem: Step transitions not smooth
**Symptoms:**
- Jerky animations
- Steps appear/disappear abruptly
- CSS transitions not working

**Solutions:**
```css
/* 1. Check CSS transitions */
.checkout-view-content {
  transition: all 0.3s ease-in-out;
  opacity: 0;
  transform: translateY(20px);
}

.checkout-view-content-enter {
  opacity: 1;
  transform: translateY(0);
}

.checkout-view-content-exit {
  opacity: 0;
  transform: translateY(-20px);
}

/* 2. Ensure proper timing */
.checkout-step {
  transition-duration: 0.3s;
  transition-timing-function: ease-in-out;
}
```

```typescript
// 3. Check animation state
const [isAnimating, setIsAnimating] = useState(false);

const handleTransitionStart = () => {
  setIsAnimating(true);
};

const handleTransitionEnd = () => {
  setIsAnimating(false);
};
```

#### Problem: Focus management issues
**Symptoms:**
- Focus not moving to active step
- Keyboard navigation not working
- Screen reader issues

**Solutions:**
```typescript
// 1. Check focus management
const focusStep = (stepElement) => {
  const firstInput = stepElement.querySelector('input, select, textarea');
  if (firstInput) {
    firstInput.focus();
  }
};

// 2. Handle keyboard navigation
const handleKeyDown = (event) => {
  if (event.key === 'Tab') {
    // Handle tab navigation
    const nextElement = getNextFocusableElement(event.target);
    if (nextElement) {
      nextElement.focus();
    }
  }
};

// 3. Check ARIA attributes
<div
  role="tabpanel"
  aria-labelledby="step-header"
  tabIndex={isActive ? 0 : -1}
>
  {/* Step content */}
</div>
```

### Performance Issues

#### Problem: Checkout loads slowly
**Symptoms:**
- Long loading times
- Slow step transitions
- High memory usage

**Solutions:**
```typescript
// 1. Lazy load components
const LazyCheckoutStep = React.lazy(() => import('./CheckoutStep'));

// 2. Memoize expensive calculations
const memoizedStepStatus = useMemo(() => {
  return calculateStepStatus(checkoutData);
}, [checkoutData]);

// 3. Debounce user input
const debouncedValidation = useCallback(
  debounce((value) => {
    validateField(value);
  }, 300),
  []
);
```

#### Problem: Memory leaks
**Symptoms:**
- Memory usage increases over time
- Performance degrades
- Browser becomes unresponsive

**Solutions:**
```typescript
// 1. Clean up event listeners
useEffect(() => {
  const handleResize = () => {
    // Handle resize
  };
  
  window.addEventListener('resize', handleResize);
  
  return () => {
    window.removeEventListener('resize', handleResize);
  };
}, []);

// 2. Clear timeouts
useEffect(() => {
  const timeoutId = setTimeout(() => {
    // Do something
  }, 1000);
  
  return () => {
    clearTimeout(timeoutId);
  };
}, []);

// 3. Clean up subscriptions
useEffect(() => {
  const subscription = checkoutService.subscribe(handleUpdate);
  
  return () => {
    subscription.unsubscribe();
  };
}, []);
```

### Browser Compatibility Issues

#### Problem: Not working in older browsers
**Symptoms:**
- JavaScript errors in older browsers
- CSS not rendering correctly
- Features not working

**Solutions:**
```typescript
// 1. Check browser support
const isSupported = () => {
  return 'fetch' in window && 
         'Promise' in window && 
         'Map' in window;
};

if (!isSupported()) {
  showError('Your browser is not supported. Please update to a newer version.');
}

// 2. Polyfill missing features
import 'core-js/stable';
import 'regenerator-runtime/runtime';

// 3. Graceful degradation
const useModernFeature = () => {
  if ('IntersectionObserver' in window) {
    return new IntersectionObserver(callback);
  } else {
    // Fallback for older browsers
    return null;
  }
};
```

## 🔍 Debugging Tools

### Console Debugging
```typescript
// 1. Enable debug mode
const DEBUG = process.env.NODE_ENV === 'development';

if (DEBUG) {
  console.log('Checkout state:', checkoutState);
  console.log('Step statuses:', stepStatuses);
  console.log('Payment methods:', paymentMethods);
}

// 2. Add debug logging
const debugLog = (message, data) => {
  if (DEBUG) {
    console.log(`[Checkout Debug] ${message}`, data);
  }
};
```

### React DevTools
```typescript
// 1. Add display names
const CheckoutStep = React.memo(({ isActive, type }) => {
  // Component logic
});

CheckoutStep.displayName = 'CheckoutStep';

// 2. Add debug props
const CheckoutStepWithDebug = (props) => {
  if (DEBUG) {
    console.log('CheckoutStep props:', props);
  }
  
  return <CheckoutStep {...props} />;
};
```

### Network Debugging
```typescript
// 1. Log network requests
const originalFetch = window.fetch;
window.fetch = (...args) => {
  console.log('Fetch request:', args);
  return originalFetch(...args)
    .then(response => {
      console.log('Fetch response:', response);
      return response;
    })
    .catch(error => {
      console.error('Fetch error:', error);
      throw error;
    });
};
```

## 📞 Getting Help

### Check Documentation
1. [Quick Start](./QUICK_START.md) - Basic setup and configuration
2. [Common Tasks](./COMMON_TASKS.md) - How to do specific things
3. [API Reference](./API_REFERENCE.md) - Complete component documentation

### Debug Steps
1. **Check console** for JavaScript errors
2. **Verify configuration** - checkout ID, container ID, etc.
3. **Test in isolation** - create minimal reproduction
4. **Check network** - verify API calls are working
5. **Test in different browsers** - check compatibility

### Common Solutions
- **Refresh the page** - often fixes temporary issues
- **Clear browser cache** - removes stale data
- **Check network connection** - ensure stable internet
- **Update browser** - use latest version
- **Disable extensions** - check for conflicts

## 🔗 Related Topics

- [Quick Start](./QUICK_START.md) - Basic setup and configuration
- [Common Tasks](./COMMON_TASKS.md) - How to do specific things
- [API Reference](./API_REFERENCE.md) - Complete component documentation
