---
**Title:** Forms and Validation Guide
**Purpose:** A practical guide to the project's standardized system for handling user input.
**Audience:** All Developers
**Maintenance:** Update when form patterns or the UI component library changes.
---

# Forms and Validation Guide

This guide provides a practical, "Level 2" deep-dive into the standardized system for building and validating forms in the `checkout-js` application. Given that nearly every step of the checkout process involves a form, understanding these patterns is essential for any developer.

## 1. Core Technologies

The project's form system is built on two specialized, industry-standard libraries:

*   **Formik:** This library is used for managing all aspects of form state. It handles tracking field values, validation errors, submission status, and more, removing the need to manage this complex state manually with React hooks.
*   **Yup:** This library is used for all form validation. It provides a simple, expressive API for defining validation schemas as objects. Formik is configured to use this schema to automatically validate form fields.

## 2. Standard Implementation Pattern

All forms in the application follow a consistent implementation pattern. The `AddressForm` component (`packages/core/src/app/address/AddressForm.tsx`) is a perfect example of this standard.

The core elements are:

1.  **`<Formik>` Provider:** The entire form is wrapped in the `<Formik>` component, which provides all the necessary context for state management. It is configured with `initialValues`, a `validationSchema`, and an `onSubmit` handler.
2.  **Validation Schema:** A `Yup.object()` schema is defined to declare the validation rules for each field.
3.  **Shared UI Components:** Fields are built using the shared form components from the `@bigcommerce/checkout/ui` package, such as `BasicFormField` and `CheckboxFormField`.
4.  **Connecting to State:** Each form field component is connected to the Formik state via its `name` prop, which must match a key in the `initialValues` and the validation schema.
5.  **Submission:** The `onSubmit` function is called by Formik only after all validation rules in the schema have passed.

**Example (`AddressForm.tsx`):**

```typescript
import { Formik, Form } from 'formik';
import { object as YupObject, string as YupString } from 'yup';

// ... other imports

const AddressForm: FunctionComponent<AddressFormProps> = ({
    // ... props
}) => {
    const validationSchema = useMemo(() => {
        return YupObject({
            firstName: YupString().required(language.translate('address.first_name_required_error')),
            lastName: YupString().required(language.translate('address.last_name_required_error')),
            // ... other fields
        });
    }, [language]);

    const handleSubmit = (values: AddressFormValues) => {
        onSubmit(mapToAddress(values, countries, formFields));
    };

    return (
        <Formik
            initialValues={getInitialValues(address, formFields)}
            onSubmit={handleSubmit}
            validationSchema={validationSchema}
        >
            <Form>
                {/* ... form fields ... */}
                <BasicFormField
                    labelContent={language.translate('address.first_name_label')}
                    name="firstName"
                />
                <BasicFormField
                    labelContent={language.translate('address.last_name_label')}
                    name="lastName"
                />
                {/* ... other form fields ... */}
            </Form>
        </Formik>
    );
};
```

## 3. Dynamic Forms

For cases where form fields need to be generated from a configuration object (for example, some payment methods define their own required fields), the application uses a `DynamicFormField` component.

Instead of manually laying out each field, a developer can pass a `field` configuration object to `DynamicFormField`, and it will render the appropriate input (e.g., text, dropdown, checkbox) with the correct label and validation rules. This is a more advanced pattern used primarily within the payment step and for the different fields required by various shipping addresses.

**Example (`FormField` Configuration):**

The `DynamicFormField` component expects a configuration object that conforms to the `FormField` type from the Checkout SDK. This object defines everything the component needs to render the field.

```typescript
// This is a conceptual example of what a FormField object looks like.
const firstNameField: FormField = {
    id: 'firstName',
    name: 'firstName',
    label: 'First Name',
    required: true,
    custom: false,
    // ... other properties like `fieldType`, `options`, etc.
};

// This would then be passed to the component
<DynamicFormField
    field={firstNameField}
    // ... other props
/>
```
