---
**Title:** The Forms & Validation Guide
**Purpose:** A guide to the Forms & Validation Cross-Cutting Concern (CCC), documenting it as a governed, horizontal architectural slice.
**Audience:** All Developers
**Maintenance:** Update if the core form libraries (Formik, Yup) are changed or if new standard form patterns are introduced.
---

# The Forms & Validation Guide

This document provides a guide to the **Forms & Validation slice**, a **governed horizontal slice** that provides a standardized, reusable set of tools and patterns for all user input in the application.

## 1. Architectural Principles

This slice is architected according to two core principles designed to manage this critical Cross-Cutting Concern (CCC):

*   **Standardization:** To prevent the **scattering and tangling** of form state logic and validation rules, the application uses a single, standardized stack for all forms.
*   **Decoupling:** The slice **decouples** validation logic from component logic. This is achieved by defining validation rules in independent, reusable schemas that are then provided to the form components, rather than writing validation logic directly inside the components.

## 2. Core Technologies

The Forms & Validation slice is built on two core open-source libraries that work together:

*   **Formik:** This library is used for all form state management. It handles the complexities of tracking field values, errors, visit/touched states, and submission status.
*   **Yup:** This library is used for all form validation. It provides a simple, expressive API for defining validation schemas as objects. Formik is configured to use this schema to automatically validate form fields.

By combining these two libraries, we create a cohesive, decoupled slice where the component's only job is to render the UI, while the state and validation are handled by the framework.

## 3. Standard Implementation Pattern

The following example demonstrates the complete, standard pattern for creating a form in this application.

```typescript
import { Formik, Form } from 'formik';
import { object, string } from 'yup';
import { BasicFormField, CheckboxFormField } from '@bigcommerce/checkout/ui';

// 1. Define the validation schema using Yup.
// This decouples the validation rules from the component.
const validationSchema = object({
    email: string()
        .email('Please enter a valid email address.')
        .required('Email address is required.'),
    password: string()
        .required('Password is required.'),
});

const MyFormComponent = () => {
    const handleSubmit = (values) => {
        console.log('Form submitted:', values);
        // Dispatch an action to the Checkout SDK, etc.
    };

    // 2. Wrap the form in the <Formik> provider.
    // It is configured with initialValues, the validationSchema, and an onSubmit handler.
    return (
        <Formik
            initialValues={{
                email: '',
                password: '',
                shouldRemember: false,
            }}
            onSubmit={handleSubmit}
            validationSchema={validationSchema}
        >
            <Form>
                {/* 3. Use shared form components. */}
                {/* The `name` prop connects the field to the Formik state. */}
                <BasicFormField
                    name="email"
                    label="Email Address"
                />

                <BasicFormField
                    name="password"
                    label="Password"
                    type="password"
                />

                <CheckboxFormField
                    name="shouldRemember"
                    label="Remember me"
                />

                <button type="submit">Sign In</button>
            </Form>
        </Formik>
    );
};
```

## 4. Dynamic Forms

For cases where form fields need to be generated dynamically based on a configuration object (for example, a payment method that defines its own required fields), the application uses a special `DynamicFormField` component.

This component accepts a `field` configuration object as a prop and dynamically renders the appropriate input (e.g., a text input, a dropdown, a checkbox) based on the properties of that object. This provides an extra layer of decoupling, allowing the server to define the shape of a form that the client can then render without having to be explicitly coded for it.
