# Spec: Internationalization (I18n) Guide

## 1. Objective

To analyze the `C:\learn\checkout-js` project and generate the complete content for the **`01-application-architecture/06-internationalization-guide.md`** document.

## 2. Rationale

The checkout application must support multiple languages and regions. This guide is essential for developers to understand the patterns and tools for consuming translated content and adding new translations.

## 3. Verification Criteria

The successful execution of this spec will result in the `01-application-architecture/06-internationalization-guide.md` file being populated with the following:

1.  **The I18n System:**
    *   Must identify the core components of the internationalization system from the `packages/locale` package:
        *   **`<LocaleProvider>`**: The top-level provider that bootstraps the system.
        *   **`LanguageService`**: The service responsible for loading translation files.

2.  **Consuming Translations in a Component:**
    *   Must describe the primary pattern for accessing the translation function. This includes the `withLanguage` Higher-Order Component (HOC) and the modern `useLanguage()` hook, which injects the `language.translate()` method.
    *   Must provide a clear, practical code example of a React component using the `language.translate()` method with a translation key.

3.  **Translation Files:**
    *   Must explain that translations are stored in JSON files.
    *   Must describe the convention for the location and structure of these files (e.g., `packages/core/src/app/locale/translations/en.json`).
    *   Must explain the key-value format used within the JSON files.
