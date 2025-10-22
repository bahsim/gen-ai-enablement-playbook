# Address Mapping Logic

## Purpose
Transforms form data into standardized address objects for API communication.

## Input Data
- Form field values from address form
- Country code for validation context
- Customer data for pre-filling

## Output Data
- Standardized Address object
- Validation status
- Error messages if invalid

## Logic Flow
1. Extract form values
2. Apply country-specific formatting
3. Validate required fields
4. Transform to Address object
5. Return result or errors

## Key Functions
- mapAddressFromFormValues(): Main transformation function
- isEqualAddress(): Address comparison
- getAddressFormFieldsValidationSchema(): Validation rules

## Error Handling
- Field validation errors
- Format validation errors
- Required field validation

## Code Pattern
```typescript
const mapAddressFromFormValues = (formValues: AddressFormValues): Address => {
  return {
    firstName: formValues.firstName,
    lastName: formValues.lastName,
    company: formValues.company,
    address1: formValues.address1,
    address2: formValues.address2,
    city: formValues.city,
    stateOrProvince: formValues.stateOrProvince,
    postalCode: formValues.postalCode,
    countryCode: formValues.countryCode,
    phone: formValues.phone,
    customFields: formValues.customFields
  };
};

const isEqualAddress = (address1: Address, address2: Address): boolean => {
  // Compare address fields for equality
  return JSON.stringify(address1) === JSON.stringify(address2);
};

const getAddressFormFieldsValidationSchema = (countryCode: string) => {
  // Return country-specific validation schema
  return yup.object().shape({
    // Validation rules based on country
  });
};
```

## Data Transformation Flow
```
Form Values → Field Extraction → Country Formatting → Validation → Address Object
     ↓
Error Collection → User Notification → Retry Logic → Success
```

## Country-Specific Logic
- US: State validation, ZIP code format
- CA: Province validation, postal code format
- UK: County validation, postcode format
- AU: State validation, postcode format

## Validation Rules
- Required fields based on country
- Format validation for postal codes
- Phone number format validation
- Custom field validation

## Real Implementation Details

### mapAddressFromFormValues Function
```typescript
export default function mapAddressFromFormValues(formValues: AddressFormValues): Address {
    const { customFields, ...address } = formValues;
    const shouldSaveAddress = formValues.shouldSaveAddress;

    return {
        ...address,
        shouldSaveAddress,
        customFields: mapCustomFormFieldsFromFormValues(customFields),
    };
}
```

### isEqualAddress Function
```typescript
export default function isEqualAddress(
    address1?: ComparableAddress,
    address2?: ComparableAddress,
): boolean {
    if (!address1 || !address2) {
        return false;
    }

    return (
        isEqual(normalizeAddress(address1), normalizeAddress(address2)) &&
        isSameState(address1, address2)
    );
}
```

### Address Normalization (Real Implementation)
```typescript
function normalizeAddress(address: ComparableAddress) {
    const ignoredFields: ComparableAddressFields[] = [
        'id',
        'shouldSaveAddress',
        'stateOrProvince',
        'stateOrProvinceCode',
        'type',
        'email',
        'country',
    ];

    return omit(
        {
            ...address,
            customFields: (address.customFields || []).filter(({ fieldValue }) => !!fieldValue),
        },
        ignoredFields,
    );
}
```

### Validation Schema (Real Implementation)
```typescript
export default memoize(function getAddressFormFieldsValidationSchema({
    formFields,
    language,
}: AddressFormFieldsValidationSchemaOptions): ObjectSchema<FormFieldValues> {
    return getFormFieldsValidationSchema({
        formFields,
        translate: getTranslateAddressError(language),
    });
});
```

## Address Formatting by Country
- **US**: Street, City, State ZIP
- **CA**: Street, City, Province Postal Code
- **UK**: Street, City, County Postcode
- **AU**: Street, City, State Postcode

## Error Handling for Address Mapping
- **Missing Required Fields**: Show field-specific error messages
- **Invalid Format**: Display format validation errors
- **Country Mismatch**: Handle country-specific validation failures
- **Network Errors**: Retry address validation API calls

## Performance Optimizations
- **Memoized Validation**: Cache validation schemas by country
- **Debounced Validation**: Prevent excessive validation calls
- **Lazy Loading**: Load country-specific rules on demand
- **Batch Processing**: Group multiple address operations
