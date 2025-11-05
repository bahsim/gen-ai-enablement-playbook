---
**Title:** Spec for: Internationalization (I18n) Implementation
**Purpose:** To define the structure for a low-level document that provides concrete code evidence for the patterns described in the high-level I18n architecture.
---

# Spec: Internationalization (I18n) Implementation

## 1. Document Purpose

The new document, `implementation-details/07-internationalization-implementation.md`, must serve as the primary low-level guide to the **Internationalization (I18n) Slice**. It must provide concrete code examples and file paths to prove the existence of the Provider/Consumer pattern and the dual access methods (HOC and hook).

## 2. Mandatory Sections

The document must contain the following sections in this order:

### 1. The Language Context Provider
*   Must provide code evidence for the `LocaleContext`, showing how the context is created and what data it holds (e.g., the `LocaleState` interface).
*   Must specify the file path for this component.

### 2. The State Access Patterns
*   Must provide two sub-sections detailing the different methods for accessing the language state.

#### 2.1. The Legacy Pattern: `withLanguage` HOC
*   Must provide a code snippet showing the implementation of the `withLanguage` Higher-Order Component.
*   Must explain its purpose: to inject language context into class components.

#### 2.2. The Modern Pattern: `useLocale` Hook
*   Must provide a code snippet showing the implementation of the `useLocale` hook.
*   Must explain its purpose: to provide direct context access for functional components.

### 3. The Consumer Component: `TranslatedString`
*   Must provide a code snippet from `TranslatedString.tsx` showing how it uses the `useLocale` hook to connect to the context and retrieve the translation function.

## 3. Principles
*   The document must be focused on **concrete evidence**. Every claim must be backed by a file path or a direct code snippet from the codebase.
