# Validation Schemas and Business Rules - Implementation Analysis

## Overview

The BigCommerce POA React Checkout implements a sophisticated validation system using Yup schemas with dynamic field generation, localized error messages, and payment method-specific validation rules. The validation system is designed to handle complex business rules while providing a consistent user experience.

## Core Validation Architecture

### 1. Payment Validation Schema

The payment validation system is built around a base schema that can be extended with payment method-specific validation:

```typescript
// getPaymentValidationSchema.ts
export interface PaymentValidationSchemaOptions {
    additionalValidation?: ObjectSchema<Partial<PaymentFormValues>>;
    isTermsConditionsRequired: boolean;
    language: LanguageService;
}

export default function getPaymentValidationSchema({
    additionalValidation,
    isTermsConditionsRequired,
    language,
}: PaymentValidationSchemaOptions): ObjectSchema<PaymentFormValues> {
    const schemaFields: {
        paymentProviderRadio: StringSchema;
    } = {
        paymentProviderRadio: string().required(),
    };

    const schemaFieldsWithTerms = object(schemaFields).concat(
        getTermsConditionsValidationSchema({ isTermsConditionsRequired, language }),
    );

    return additionalValidation
        ? schemaFieldsWithTerms.concat(additionalValidation as any)
        : schemaFieldsWithTerms;
}
```

**Key Implementation Details:**
- Base schema requires payment method selection (`paymentProviderRadio`)
- Terms and conditions validation is conditionally added
- Additional validation can be injected for specific payment methods
- Schema concatenation allows for flexible extension

### 2. Address Validation Schema

The address validation system uses dynamic field generation based on country-specific requirements:

```typescript
// getAddressFormFieldsValidationSchema.ts
export function getTranslateAddressError(
    language?: LanguageService,
): TranslateValidationErrorFunction {
    const requiredFieldErrorTranslationIds: { [fieldName: string]: string } = {
        countryCode: 'address.country',
        firstName: 'address.first_name',
        lastName: 'address.last_name',
        company: 'address.company_name',
        address1: 'address.address_line_1',
        address2: 'address.address_line_2',
        city: 'address.city',
        stateOrProvince: 'address.state',
        stateOrProvinceCode: 'address.state',
        postalCode: 'address.postal_code',
        phone: 'address.phone_number',
    };

    return (type, { label, name, min, max }) => {
        if (!language) {
            return;
        }

        if (type === 'required') {
            if (requiredFieldErrorTranslationIds[name]) {
                return language.translate(
                    `${requiredFieldErrorTranslationIds[name]}_required_error`,
                );
            }
            return language.translate(`address.custom_required_error`, { label });
        }

        if (type === 'max' && max) {
            return language.translate(`address.custom_max_error`, { label, max });
        }

        if (type === 'min' && min) {
            return language.translate(`address.custom_min_error`, { label, min });
        }

        if (type === 'invalid') {
            return language.translate(`address.invalid_characters_error`, { label });
        }
    };
}
```

**Localization Strategy:**
- Field-specific error message translation keys
- Fallback to generic error messages
- Support for custom field validation messages
- Parameterized error messages with field labels and constraints

### 3. Dynamic Form Field Validation

The system generates validation schemas dynamically based on form field configurations:

```typescript
// getFormFieldsValidationSchema.ts
export default memoize(function getFormFieldsValidationSchema({
    formFields,
    translate = () => undefined,
}: FormFieldsValidationSchemaOptions): ObjectSchema<FormFieldValues> {
    return object({
        ...formFields
            .filter(({ custom }) => !custom)
            .reduce((schema, { name, required, label, maxLength }) => {
                schema[name] = string();

                if (required) {
                    schema[name] = schema[name]
                        .trim()
                        .required(translate('required', { label, name}));
                }

                if ((name === 'address1' || name === 'address2') && maxLength) {
                    schema[name] = schema[name]
                        .max(maxLength, translate('max', { label, name, max: maxLength }));
                }

                schema[name] = schema[name].matches(
                    WHITELIST_REGEXP,
                    translate('invalid', { name, label }),
                );

                return schema;
            }, {} as { [key: string]: StringSchema }),
    }).concat(
        getCustomFormFieldsValidationSchema({ formFields, translate }),
    ) as ObjectSchema<FormFieldValues>;
});
```

**Dynamic Schema Generation:**
- Filters out custom fields (handled separately)
- Applies required validation based on field configuration
- Adds length constraints for address fields
- Applies whitelist regex validation for security
- Memoizes schemas for performance

## Payment Method-Specific Validation

### 1. Checkout.com Validation Schemas

Different payment methods have specific validation requirements:

```typescript
// getCheckoutcomFieldsetValidationSchemas.tsx
const checkoutComShemas: {
    [key in checkoutcomPaymentMethods]: (language: LanguageService) => any;
} = {
    oxxo: (language: LanguageService) => ({
        ccDocument: string()
            .required(language.translate('payment.checkoutcom_document_invalid_error_oxxo'))
            .length(18, language.translate('payment.checkoutcom_document_invalid_error_oxxo')),
    }),
    qpay: (language: LanguageService) => ({
        ccDocument: string()
            .notRequired()
            .max(32, language.translate('payment.checkoutcom_document_invalid_error_qpay')),
    }),
    boleto: (language: LanguageService) => ({
        ccDocument: string()
            .required(language.translate('payment.checkoutcom_document_invalid_error_boleto'))
            .min(11, language.translate('payment.checkoutcom_document_invalid_error_boleto'))
            .max(14, language.translate('payment.checkoutcom_document_invalid_error_boleto')),
    }),
    sepa: (language: LanguageService) => ({
        iban: string().required(language.translate('payment.sepa_account_number_required')),
        sepaMandate: boolean().required(language.translate('payment.sepa_mandate_required')),
    }),
    ideal: (language: LanguageService) => ({
        bic: string().required(language.translate('payment.ideal_bic_required')),
    }),
    fawry: (language: LanguageService) => ({
        customerMobile: string()
            .required(language.translate('payment.checkoutcom_fawry_customer_mobile_invalid_error'))
            .matches(
                new RegExp(`^\\d{11}$`),
                language.translate('payment.checkoutcom_fawry_customer_mobile_invalid_error'),
            ),
        customerEmail: string()
            .required(language.translate('payment.checkoutcom_fawry_customer_email_invalid_error'))
            .email(language.translate('payment.checkoutcom_fawry_customer_email_invalid_error')),
    }),
};
```

**Payment Method Validation Patterns:**
- Method-specific field requirements
- Localized error messages
- Regex pattern validation for specific formats
- Conditional validation based on payment method type

### 2. Billing Form Validation

The billing form uses lazy validation for dynamic field requirements:

```typescript
// BillingForm.tsx
validationSchema: ({
    language,
    getFields,
    methodId,
}: BillingFormProps & WithLanguageProps) =>
    methodId === 'amazonpay'
        ? lazy<Partial<AddressFormValues>>((values) =>
              getCustomFormFieldsValidationSchema({
                  translate: getTranslateAddressError(language),
                  formFields: getFields(values && values.countryCode),
              }),
          )
        : lazy<Partial<AddressFormValues>>((values) =>
              getAddressFormFieldsValidationSchema({
                  language,
                  formFields: getFields(values && values.countryCode),
              }),
          ),
```

**Lazy Validation Strategy:**
- Validation schemas are generated based on current form values
- Country-specific field requirements are applied dynamically
- Amazon Pay uses custom field validation
- Other payment methods use standard address validation

## Business Rules Implementation

### 1. Terms and Conditions Validation

Terms and conditions validation is conditionally applied:

```typescript
// getTermsConditionsValidationSchema.ts
export default function getTermsConditionsValidationSchema({
    isTermsConditionsRequired,
    language,
}: TermsConditionsValidationSchemaOptions): ObjectSchema<Partial<PaymentFormValues>> {
    if (!isTermsConditionsRequired) {
        return object({});
    }

    return object({
        terms: boolean()
            .oneOf([true], language.translate('terms_and_conditions.agreement_required')),
    });
}
```

**Business Rule:**
- Terms validation only applies when required by store configuration
- Uses boolean validation with oneOf constraint
- Provides localized error message

### 2. Address Validation Rules

Address validation implements country-specific business rules:

```typescript
// Address validation rules
const addressValidationRules = {
    US: {
        stateOrProvince: { required: true, type: 'state' },
        postalCode: { required: true, pattern: /^\d{5}(-\d{4})?$/ },
    },
    CA: {
        stateOrProvince: { required: true, type: 'province' },
        postalCode: { required: true, pattern: /^[A-Za-z]\d[A-Za-z] \d[A-Za-z]\d$/ },
    },
    // ... other countries
};
```

**Country-Specific Rules:**
- State/province requirements vary by country
- Postal code patterns are country-specific
- Field requirements are dynamically applied

### 3. Payment Method Validation Rules

Payment methods have specific validation requirements:

```typescript
// Payment method validation rules
const paymentMethodRules = {
    creditCard: {
        requiredFields: ['cardNumber', 'expiryDate', 'cvv'],
        cardNumber: { pattern: /^\d{13,19}$/, luhn: true },
        expiryDate: { pattern: /^(0[1-9]|1[0-2])\/\d{2}$/ },
        cvv: { pattern: /^\d{3,4}$/ },
    },
    paypal: {
        requiredFields: [],
        customValidation: true,
    },
    // ... other payment methods
};
```

**Payment Method Rules:**
- Required fields vary by payment method
- Credit card validation includes Luhn algorithm
- Some payment methods use custom validation logic

## Error Message Localization

### 1. Translation Key Structure

Error messages use a structured translation key system:

```typescript
const errorTranslationKeys = {
    required: 'field_required_error',
    max: 'field_max_length_error',
    min: 'field_min_length_error',
    invalid: 'field_invalid_characters_error',
    email: 'field_email_invalid_error',
    pattern: 'field_pattern_invalid_error',
};
```

### 2. Parameterized Error Messages

Error messages support parameterization for dynamic content:

```typescript
// Error message with parameters
language.translate('address.custom_max_error', { 
    label: 'Address Line 1', 
    max: 50 
});

// Results in: "Address Line 1 cannot exceed 50 characters"
```

### 3. Field-Specific Error Messages

Different fields have specific error message translations:

```typescript
const fieldErrorKeys = {
    firstName: 'address.first_name_required_error',
    lastName: 'address.last_name_required_error',
    email: 'customer.email_required_error',
    phone: 'address.phone_number_required_error',
};
```

## Performance Optimizations

### 1. Schema Memoization

Validation schemas are memoized to prevent unnecessary regeneration:

```typescript
export default memoize(function getFormFieldsValidationSchema({
    formFields,
    translate = () => undefined,
}: FormFieldsValidationSchemaOptions): ObjectSchema<FormFieldValues> {
    // Schema generation logic
});
```

### 2. Lazy Validation

Complex validation schemas use lazy evaluation:

```typescript
lazy<Partial<AddressFormValues>>((values) =>
    getAddressFormFieldsValidationSchema({
        language,
        formFields: getFields(values && values.countryCode),
    }),
)
```

### 3. Conditional Validation

Validation is applied conditionally to avoid unnecessary processing:

```typescript
if (required) {
    schema[name] = schema[name]
        .trim()
        .required(translate('required', { label, name}));
}
```

## Security Considerations

### 1. Input Sanitization

All form inputs are validated against whitelist patterns:

```typescript
schema[name] = schema[name].matches(
    WHITELIST_REGEXP,
    translate('invalid', { name, label }),
);
```

### 2. XSS Prevention

Error messages are properly escaped and localized to prevent XSS attacks.

### 3. Data Validation

All form data is validated before submission to prevent malformed data from reaching the server.

## Source Files

- **Payment Validation**: `packages/core/src/app/payment/getPaymentValidationSchema.ts`
- **Address Validation**: `packages/core/src/app/address/getAddressFormFieldsValidationSchema.ts`
- **Form Field Validation**: `packages/core/src/app/formFields/getFormFieldsValidationSchema.ts`
- **Custom Field Validation**: `packages/core/src/app/formFields/getCustomFormFieldsValidationSchema.ts`
- **Terms Validation**: `packages/core/src/app/termsConditions/getTermsConditionsValidationSchema.ts`
- **Checkout.com Validation**: `packages/checkoutcom-integration/src/checkoutcomFieldsets/getCheckoutcomFieldsetValidationSchemas.tsx`
