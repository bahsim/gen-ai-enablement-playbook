# Implementation Details: Internationalization (I18n)

This document provides the low-level, evidence-based implementation details for the **Internationalization (I18n) Slice**. It provides concrete code examples for the key architectural patterns described in the high-level `08-the-internationalization-architecture.md` document.

## 1. The Language Context Provider

The entire I18n slice is built around a standard React Context provider. This provider acts as the single source of truth for all language-related data.

*   **Code Evidence (`packages/locale/src/LocaleContext.ts`):** This file defines the `LocaleState` interface and creates the `LocaleContext` itself.
    ```typescript
    // packages/locale/src/LocaleContext.ts
    export interface LocaleState {
        language: LanguageService;
        locale: string;
    }

    const LocaleContext = createContext<LocaleState | undefined>(undefined);

    export default LocaleContext;
    ```

## 2. The State Access Patterns

The application provides two distinct patterns for components to access the `LocaleContext`.

### 2.1. The Legacy Pattern: `withLanguage` HOC
This Higher-Order Component is used to inject the language context as props into legacy class components that cannot use hooks.

*   **Code Evidence (`packages/locale/src/withLanguage.tsx`):**
    ```typescript
    // packages/locale/src/withLanguage.tsx
    export default function withLanguage<TProps extends WithLanguageProps>(
        OriginalComponent: ComponentType<TProps>
    ): ComponentType<Omit<TProps, keyof WithLanguageProps>> {
        const WithLanguage: FunctionComponent<Omit<TProps, keyof WithLanguageProps>> = props => (
            <LocaleContext.Consumer>
                { context => <OriginalComponent { ...props as TProps } language={ context.language } /> }
            </LocaleContext.Consumer>
        );
        // ...
        return WithLanguage;
    }
    ```

### 2.2. The Modern Pattern: `useLocale` Hook
This is the primary pattern for modern functional components. The `useLocale` hook provides direct access to the `LocaleContext`, allowing the component to be completely decoupled from its parent.

*   **Code Evidence (`packages/locale/src/LocaleContext.ts`):**
    ```typescript
    // packages/locale/src/LocaleContext.ts
    export function useLocale(): LocaleState {
        const context = useContext(LocaleContext);

        if (!context) {
            throw new Error(
                'useLocale must be used within a LocaleContextProvider'
            );
        }

        return context;
    }
    ```

## 3. The Consumer Component: `TranslatedString`

The `TranslatedString` component is the canonical consumer of the I18n slice. It uses the modern `useLocale` hook to get access to the translation function and render the appropriate string.

*   **Code Evidence (`packages/locale/src/TranslatedString.tsx`):**
    ```typescript
    // packages/locale/src/TranslatedString.tsx
    const TranslatedString: FunctionComponent<TranslatedStringProps> = ({ id, data }) => {
        const { language } = useLocale();

        return <>{ language.translate(id, data) }</>;
    };
    ```
