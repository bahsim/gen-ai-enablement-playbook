---
**Title:** Forms Implementation Patterns
**Purpose:** To provide a low-level, evidence-based guide to the specific technologies and code patterns used to implement the Forms & Validation architecture.
---

# Forms Implementation Patterns

This document details the "how" of the Forms & Validation Slice. It provides the concrete, coder-level examples of the specific libraries and patterns that must be used to implement the high-level architectural principles defined in `07-the-forms-and-validation-architecture.md`.

## 1. Core Technologies

The implementation of the Forms & Validation architecture is built upon two core open-source libraries:

*   **Formik:** This library is the **Form State Engine**. It is used to manage all aspects of form state, including values, errors, and submission status.
*   **Yup:** This library is the **Declarative Validation Engine**. It is used to create the schema that defines the validation rules for the form's data.

## 2. The `withFormik` HOC: The State Injection Layer

The primary pattern for providing the Formik state to a component tree is the `withFormik` Higher-Order Component (HOC). This HOC wraps a "dumb" presentational component and injects the Formik state engine.

Its configuration object is responsible for two key architectural connections:
1.  It defines the `handleSubmit` function, which is the bridge to the application's action dispatching layer.
2.  It defines the `validationSchema`, which is the bridge to the Yup validation engine.

## 3. The `<Field>` Component: The UI/State Bridge

The primary pattern for connecting a specific input component to the Formik state is Formik's built-in `<Field>` component. This component uses the **render prop** pattern to act as the architectural bridge between the UI and the state.

It is responsible for:
1.  Finding the Formik context provided by the `withFormik` HOC.
2.  Accessing the state for a specific field (e.g., `email`).
3.  Passing the required props (`value`, `onChange`, `onBlur`, error status, etc.) to the "dumb" UI component that it renders.

## 4. End-to-End Implementation Example

The `GuestForm.tsx` and its related components provide the canonical, evidence-based example of this entire, multi-layered pattern in action.

```typescript
// GuestForm.tsx - A "dumb" presentational component wrapped in withFormik

// 1. The `withFormik` HOC provides the state and validation schema.
export default withLanguage(
    withFormik<GuestFormProps & WithLanguageProps, GuestFormValues>({
        // Bridge to the action layer
        handleSubmit: (values, { props: { onContinueAsGuest } }) => {
            onContinueAsGuest(values);
        },
        // Bridge to the validation engine
        validationSchema: ({ language }: GuestFormProps & WithLanguageProps) => {
            const email = string()
                .email(language.translate('customer.email_invalid_error'))
                .required(language.translate('customer.email_required_error'));
            
            return object({ email });
        },
    })(memo(GuestForm)),
);

// 2. The component itself is "dumb" and just renders generic fields.
const GuestForm: FunctionComponent<GuestFormProps> = ({
    // ... receives props from Formik HOC
}) => {
    return (
        <Form>
            <EmailField isFloatingLabelEnabled={isFloatingLabelEnabled} onChange={onChangeEmail}/>
        </Form>
    );
};


// EmailField.tsx - A reusable field component

// 3. It uses `<Field>` (via `BasicFormField`) to bridge to the Formik state.
const EmailField: FunctionComponent<EmailFieldProps> = ({
    isFloatingLabelEnabled,
    onChange,
}) => (
    <BasicFormField
        isFloatingLabelEnabled={isFloatingLabelEnabled}
        name="email"
        render={({ field, form: { touched, errors } }: FieldProps) => (
            <TextInput
                {...field} // <-- Receives value, onChange, onBlur from <Field>
                isInvalid={touched.email && errors.email}
                type={InputType.Email}
            />
        )}
    />
);
```

This implementation pattern creates a clean, multi-layered separation of concerns that is the standard for all forms in the application.
