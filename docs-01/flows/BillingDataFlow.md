# Billing Data Flow - Logic Extraction

## Data Input Sources
- **Checkout Service**: Provides billing address data
- **Country Configuration**: Determines field requirements
- **Customer Data**: Pre-fills existing address information
- **Form State**: User input from address form

## Data Transformation Points

### 1. Address Mapping
- **Input**: Form values from address form
- **Process**: `mapAddressFromFormValues()` transforms form data to Address object
- **Output**: Standardized address object for API

### 2. Address Comparison
- **Input**: Current billing address vs new form values
- **Process**: `isEqualAddress()` compares addresses
- **Output**: Boolean indicating if update is needed

### 3. Field Validation
- **Input**: Form values and country code
- **Process**: Country-specific validation schema application
- **Output**: Validation errors or success state

## Data Flow Sequence

### Initialization Flow
1. **Load Countries** → `loadBillingAddressFields()`
2. **Get Customer Data** → Extract existing billing address
3. **Map Form Values** → Convert address to form format
4. **Render Form** → Display fields based on country

### Submission Flow
1. **Form Submission** → `handleSubmit()` triggered
2. **Address Mapping** → Convert form values to address object
3. **Address Comparison** → Check if address changed
4. **Update Address** → `updateAddress()` if changed
5. **Update Checkout** → `updateCheckout()` for order comments
6. **Navigation** → `navigateNextStep()` to next step

## State Management Flow

### Component State
- **isInitializing**: Loading state for country data
- **isUpdating**: Processing state for address updates
- **billingAddress**: Current billing address object
- **countries**: Available countries list

### Form State (Formik)
- **values**: Current form field values
- **errors**: Validation error messages
- **touched**: Field interaction tracking
- **isSubmitting**: Form submission state

## Error Handling Flow

### Validation Errors
1. **Field Validation** → Real-time validation errors
2. **Form Submission** → Pre-submission validation
3. **Error Display** → Show errors to user

### API Errors
1. **Address Update** → Handle update failures
2. **Checkout Update** → Handle checkout update failures
3. **Error Propagation** → Pass errors to parent component

## Integration Points

### Checkout Service Integration
- **updateAddress()**: Updates billing address in checkout
- **updateCheckout()**: Updates checkout with order comments
- **loadBillingAddressFields()**: Loads country and field data

### Address Utilities Integration
- **mapAddressFromFormValues()**: Converts form to address object
- **isEqualAddress()**: Compares address objects
- **getBillingAddressFields()**: Gets country-specific fields

## Performance Considerations

### Data Loading
- **Lazy Loading**: Countries loaded on component mount
- **Caching**: Country data cached to prevent re-fetching
- **Conditional Loading**: Fields loaded based on country selection

### Form Optimization
- **Debounced Validation**: Prevents excessive validation calls
- **Memoized Schemas**: Validation schemas cached per country
- **Conditional Rendering**: Fields rendered only when needed

