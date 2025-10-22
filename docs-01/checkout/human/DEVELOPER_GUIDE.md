# Practical Developer Guide - Payment & Billing Forms

## 🚀 **Where to Start - File Locations**

### **Main Components (Start Here)**
```
packages/core/src/app/billing-and-payment/
├── BillingAndPayment.tsx          # Main orchestrator component
├── BillingAndPayment.scss         # Styles
└── context/
    ├── BillingAndPaymentContext.ts        # Context definition
    ├── BillingAndPaymentContextProvider.tsx  # Context provider
    ├── useBillingAndPayment.ts            # Hook for using context
    └── withBillingAndPaymentContext.tsx   # HOC wrapper

packages/core/src/app/billing/
├── BillingForm.tsx                # Billing form component
├── Billing.tsx                    # Billing container
└── StaticBillingAddress.tsx       # Static billing display

packages/core/src/app/payment/
├── PaymentForm.tsx                # Payment form component
├── Payment.tsx                    # Payment container
├── PaymentMethodList.tsx          # Payment method selection
└── PaymentSubmitButton.tsx        # Submit button
```

## 🎯 **Quick Start - What You Need to Know**

### **1. The Main Flow**
```typescript
// BillingAndPayment.tsx - This is your main component
const BillingAndPayment = () => {
  return (
    <div>
      <div className="billing-section">
        <Billing />  {/* Handles billing address */}
      </div>
      <div className="payment-section">
        <Payment />  {/* Handles payment methods */}
      </div>
      <Button onClick={handlePlaceOrderClick}>Place Order</Button>
    </div>
  );
};
```

### **2. How It Works**
1. **User fills billing form** → `Billing` component
2. **User selects payment method** → `Payment` component  
3. **User clicks "Place Order"** → `BillingAndPayment` orchestrates
4. **Billing submits first** → Then payment submits
5. **Order completes** → Success or error

## 🔧 **Common Tasks - What You'll Actually Do**

### **Task 1: Add a New Billing Field**

**File:** `packages/core/src/app/billing/BillingForm.tsx`

```typescript
// 1. Add field to the form values type
export type BillingFormValues = AddressFormValues & { 
  orderComment: string;
  yourNewField: string;  // Add this
};

// 2. Add field to the form
const BillingForm = ({ values, setFieldValue, ... }) => {
  return (
    <Form>
      {/* Existing fields */}
      
      {/* Add your new field */}
      <div className="form-field">
        <label htmlFor="yourNewField">Your New Field</label>
        <input
          id="yourNewField"
          name="yourNewField"
          type="text"
          value={values.yourNewField || ''}
          onChange={(e) => setFieldValue('yourNewField', e.target.value)}
        />
      </div>
    </Form>
  );
};
```

### **Task 2: Add a New Payment Method**

**File:** `packages/core/src/app/payment/PaymentMethodList.tsx`

```typescript
// 1. Add to PaymentMethodId enum
export enum PaymentMethodId {
  // ... existing methods
  YourNewMethod = 'yournewmethod',
}

// 2. Add to the payment method list
const PaymentMethodList = ({ methods, onSelect }) => {
  return (
    <div className="payment-methods">
      {methods.map(method => (
        <div key={method.id} onClick={() => onSelect(method)}>
          {method.friendlyName}
        </div>
      ))}
      
      {/* Add your custom payment method */}
      <div onClick={() => onSelect({ id: 'yournewmethod', friendlyName: 'Your New Method' })}>
        Your New Method
      </div>
    </div>
  );
};
```

### **Task 3: Customize Validation**

**File:** `packages/core/src/app/payment/getPaymentValidationSchema.ts`

```typescript
// Add validation for your payment method
const getPaymentValidationSchema = (methodId: string) => {
  switch (methodId) {
    case 'yournewmethod':
      return yup.object().shape({
        yourField: yup.string().required('This field is required'),
        // ... other validation rules
      });
    
    // ... existing cases
  }
};
```

### **Task 4: Handle Form Submission**

**File:** `packages/core/src/app/billing-and-payment/BillingAndPayment.tsx`

```typescript
const handlePlaceOrderClick = useCallback(() => {
  // 1. Validate billing first
  if (!submitBilling) {
    console.error('Cannot place order: submitBilling is not defined');
    return;
  }
  
  // 2. Submit billing (this will trigger payment submission)
  submitBilling();
}, [submitBilling]);
```

## 🐛 **Common Issues & Quick Fixes**

### **Issue 1: Form Not Submitting**
```typescript
// Check if submit handlers are defined
const { submitBilling, submitPayment } = useBillingAndPayment();

if (!submitBilling || !submitPayment) {
  console.error('Submit handlers not defined');
  return;
}
```

### **Issue 2: Validation Not Working**
```typescript
// Make sure validation schema is applied
const validationSchema = getPaymentValidationSchema(selectedMethodId);

// In your form
<Formik
  validationSchema={validationSchema}
  onSubmit={handleSubmit}
>
  {/* Form content */}
</Formik>
```

### **Issue 3: Context Not Available**
```typescript
// Wrap your component with the context HOC
export default withBillingAndPaymentContext(YourComponent);

// Or use the hook
const { isBillingReady, isPaymentReady } = useBillingAndPayment();
```

## 📝 **Real Code Examples**

### **Example 1: Add a Custom Billing Field**

```typescript
// In BillingForm.tsx
const BillingForm = ({ values, setFieldValue, ... }) => {
  return (
    <Form>
      {/* Existing address fields */}
      
      {/* Your custom field */}
      <div className="form-field">
        <label htmlFor="companyTaxId">Company Tax ID</label>
        <input
          id="companyTaxId"
          name="companyTaxId"
          type="text"
          value={values.companyTaxId || ''}
          onChange={(e) => setFieldValue('companyTaxId', e.target.value)}
          placeholder="Enter your company tax ID"
        />
      </div>
    </Form>
  );
};
```

### **Example 2: Add a Custom Payment Method**

```typescript
// In PaymentMethodList.tsx
const PaymentMethodList = ({ methods, onSelect, selectedMethod }) => {
  const customMethods = [
    { id: 'banktransfer', friendlyName: 'Bank Transfer' },
    { id: 'cryptocurrency', friendlyName: 'Cryptocurrency' },
  ];
  
  return (
    <div className="payment-methods">
      {/* Existing methods */}
      {methods.map(method => (
        <div 
          key={method.id} 
          className={`payment-method ${selectedMethod?.id === method.id ? 'selected' : ''}`}
          onClick={() => onSelect(method)}
        >
          {method.friendlyName}
        </div>
      ))}
      
      {/* Custom methods */}
      {customMethods.map(method => (
        <div 
          key={method.id}
          className={`payment-method ${selectedMethod?.id === method.id ? 'selected' : ''}`}
          onClick={() => onSelect(method)}
        >
          {method.friendlyName}
        </div>
      ))}
    </div>
  );
};
```

### **Example 3: Custom Validation**

```typescript
// In getPaymentValidationSchema.ts
const getPaymentValidationSchema = (methodId: string) => {
  switch (methodId) {
    case 'banktransfer':
      return yup.object().shape({
        bankName: yup.string().required('Bank name is required'),
        accountNumber: yup.string().required('Account number is required'),
        routingNumber: yup.string().required('Routing number is required'),
      });
    
    case 'cryptocurrency':
      return yup.object().shape({
        walletAddress: yup.string().required('Wallet address is required'),
        cryptocurrency: yup.string().required('Cryptocurrency type is required'),
      });
    
    default:
      return yup.object().shape({});
  }
};
```

## 🔍 **Debugging Tips**

### **1. Check Context State**
```typescript
const { isBillingReady, isPaymentReady, submitBilling, submitPayment } = useBillingAndPayment();

console.log('Billing ready:', isBillingReady);
console.log('Payment ready:', isPaymentReady);
console.log('Submit billing:', !!submitBilling);
console.log('Submit payment:', !!submitPayment);
```

### **2. Check Form Values**
```typescript
// In your form component
console.log('Form values:', values);
console.log('Form errors:', errors);
console.log('Form touched:', touched);
```

### **3. Check Validation Schema**
```typescript
// In your validation function
console.log('Method ID:', methodId);
console.log('Validation schema:', validationSchema);
```

## 🚀 **Next Steps**

1. **Start with BillingForm.tsx** - Add your billing fields
2. **Modify PaymentMethodList.tsx** - Add your payment methods
3. **Update validation schemas** - Add your validation rules
4. **Test the flow** - Use the debugging tips above
5. **Style your changes** - Update the SCSS files

## 📚 **Key Files to Remember**

- **BillingAndPayment.tsx** - Main orchestrator
- **BillingForm.tsx** - Billing form fields
- **PaymentForm.tsx** - Payment form fields
- **PaymentMethodList.tsx** - Payment method selection
- **getPaymentValidationSchema.ts** - Validation rules
- **BillingAndPaymentContext.ts** - State management

## ⚡ **Quick Reference**

```typescript
// Add billing field
setFieldValue('fieldName', value);

// Add payment method
onSelect({ id: 'methodId', friendlyName: 'Method Name' });

// Check if ready to submit
const { isBillingReady, isPaymentReady } = useBillingAndPayment();
const canSubmit = isBillingReady && isPaymentReady;

// Submit the form
submitBilling(); // This triggers the whole flow
```

**This is your practical guide - start here and you'll know exactly what to do!**
