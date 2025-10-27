# Form Validation Logic

## Purpose
Validates form input based on country-specific requirements and business rules.

## Input Data
- Form field values
- Country code
- Validation schema
- Business rules

## Output Data
- Validation errors
- Field-specific error messages
- Overall form validity status

## Logic Flow
1. Load country-specific validation rules
2. Apply field-level validation
3. Check business rule compliance
4. Generate error messages
5. Update form state

## Key Functions
- `getBillingAddressFieldsValidationSchema()`: Country-specific rules
- `getCustomFormFieldsValidationSchema()`: Custom field validation
- `getAcknowledgmentValidationSchema()`: Acknowledgment validation

## Error Types
- Required field errors
- Format validation errors
- Business rule violations
- Custom field errors

