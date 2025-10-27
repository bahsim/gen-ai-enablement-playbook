# AI Agent Rules - BigCommerce POA React Checkout

## 🎯 PROJECT-SPECIFIC RULES - What Makes This Codebase Unique

### 1. Package Naming Convention
```bash
# This project has 50+ packages with specific naming:
packages/[payment-method]-integration/    # e.g., paypal-commerce-integration
packages/[feature]-utils/                # e.g., dom-utils, error-handling-utils
packages/core/                           # Main app (NOT src/app)
packages/analytics/                      # Analytics (NOT @analytics)
packages/locale/                         # i18n (NOT @locale)
```

### 2. Class Components for Main Features
```typescript
// This project uses CLASS components for main checkout features
// NOT functional components with hooks
class Checkout extends Component<CheckoutProps & WithCheckoutProps, CheckoutState> {
    state: CheckoutState = {
        isBillingSameAsShipping: true,
        isCartEmpty: false,
        // ... other state
    };

    async componentDidMount(): Promise<void> {
        // Async operations in lifecycle methods
    }
}
```

### 3. Specific Import Patterns
```typescript
// Use these EXACT imports from this project:
import { TranslatedString } from '@bigcommerce/checkout-sdk';
import { withAnalytics } from '@bigcommerce/checkout/analytics';
import { withBillingAndPaymentContext } from '@bigcommerce/checkout/billing-and-payment';
import { LazyContainer } from '@bigcommerce/checkout/ui';
```

### 4. SCSS Design Token System
```scss
// This project uses specific design tokens:
padding: spacing("single");           // NOT padding: 16px
color: $color-textPrimary;           // NOT color: #333
font-size: fontSize("large");        // NOT font-size: 18px
border-radius: $borderRadius-base;   // NOT border-radius: 4px

// Import pattern:
@import '../../settings/global';
```

### 5. File Structure Rules
```bash
# This project has specific file organization:
packages/core/src/app/[feature]/ComponentName.tsx
packages/core/src/scss/components/[feature]/_componentName.scss
packages/core/src/scss/settings/[feature]/_settings.scss

# NOT the typical React structure:
src/components/ComponentName.tsx  # ❌ WRONG
src/styles/ComponentName.scss     # ❌ WRONG
```

### 6. HOC Pattern Usage
```typescript
// This project uses specific HOC patterns:
export default withAnalytics(withBillingAndPaymentContext(BillingAndPayment));

// NOT typical React patterns:
export default withRouter(Component);  // ❌ WRONG
```

### 7. Translation System
```typescript
// This project uses TranslatedString, NOT react-i18next:
<TranslatedString id="checkout.billing.title" />

// NOT:
<Trans>checkout.billing.title</Trans>  // ❌ WRONG
```

## 🚨 PROJECT-SPECIFIC FAILURES - Don't Do These

### 1. Wrong Package Structure
```bash
# WRONG: Typical React project structure
src/
├── components/
├── pages/
└── utils/

# RIGHT: This project's structure
packages/
├── core/src/app/
├── analytics/src/
└── locale/src/
```

### 2. Wrong Component Patterns
```typescript
// WRONG: Modern React with hooks
const Checkout = () => {
    const [state, setState] = useState({});
    useEffect(() => {}, []);
    return <div>Checkout</div>;
};

// RIGHT: This project uses class components
class Checkout extends Component<Props, State> {
    state = {};
    componentDidMount() {}
    render() { return <div>Checkout</div>; }
}
```

### 3. Wrong Import Paths
```typescript
// WRONG: Typical React imports
import { useState } from 'react';
import { useTranslation } from 'react-i18next';

// RIGHT: This project's specific imports
import { Component } from 'react';
import { TranslatedString } from '@bigcommerce/checkout-sdk';
```

## 🎯 PROJECT-SPECIFIC TEMPLATES - Copy These Exactly

### 1. Main Component Template
```typescript
// packages/core/src/app/[feature]/ComponentName.tsx
import React, { Component } from 'react';
import { TranslatedString } from '@bigcommerce/checkout-sdk';
import { withAnalytics } from '@bigcommerce/checkout/analytics';

interface ComponentNameProps {
    // Props specific to this project
}

interface ComponentNameState {
    // State specific to this project
}

class ComponentName extends Component<ComponentNameProps, ComponentNameState> {
    state: ComponentNameState = {
        // Initial state
    };

    componentDidMount(): void {
        // Lifecycle logic
    }

    render(): React.ReactNode {
        return (
            <div className="componentName">
                <TranslatedString id="checkout.feature.title" />
            </div>
        );
    }
}

export default withAnalytics(ComponentName);
```

### 2. SCSS Template
```scss
// packages/core/src/scss/components/[feature]/_componentName.scss
@import '../../settings/global';

.componentName {
    padding: spacing("single");
    color: $color-textPrimary;
    
    &-header {
        font-size: fontSize("large");
        margin-bottom: spacing("base");
    }
}
```

### 3. Settings Template
```scss
// packages/core/src/scss/settings/[feature]/_settings.scss
// =============================================================================
// FEATURE NAME (Settings)
// =============================================================================

$feature-padding: spacing("single");
$feature-margin: spacing("base");
$feature-backgroundColor: $color-white;
```

## 🚀 PROJECT-SPECIFIC CHECKLIST

Before generating code for THIS project, AI agents MUST:

- [ ] **Use packages/ structure** - NOT src/ structure
- [ ] **Use class components** - NOT functional components with hooks
- [ ] **Use TranslatedString** - NOT react-i18next
- [ ] **Use withAnalytics HOC** - NOT custom analytics
- [ ] **Use spacing() functions** - NOT hardcoded pixels
- [ ] **Use $color- variables** - NOT hex colors
- [ ] **Place files in packages/core/src/app/** - NOT src/components/

## 🎯 PROJECT-SPECIFIC SUCCESS METRICS

Code is correct for THIS project when:
- ✅ Uses packages/ structure (not src/)
- ✅ Uses class components (not hooks)
- ✅ Uses TranslatedString (not react-i18next)
- ✅ Uses withAnalytics HOC
- ✅ Uses spacing() and $color- variables
- ✅ Files are in packages/core/src/app/

## 🚨 THE ONLY RULES THAT MATTER FOR THIS PROJECT

### 1. Package Structure
```bash
packages/core/src/app/[feature]/ComponentName.tsx  # ✅ RIGHT
src/components/ComponentName.tsx                   # ❌ WRONG
```

### 2. Component Type
```typescript
class Component extends Component<Props, State> {}  # ✅ RIGHT
const Component = () => {}                          # ❌ WRONG
```

### 3. Translation System
```typescript
<TranslatedString id="key" />                       # ✅ RIGHT
<Trans>key</Trans>                                  # ❌ WRONG
```

### 4. Design Tokens
```scss
padding: spacing("single");                         # ✅ RIGHT
padding: 16px;                                      # ❌ WRONG
```

That's it. These are the ONLY project-specific rules that matter.

## 🔄 REVERSE COMMAND - Discover Project Nuances

When working on this project, use this command to discover project-specific patterns:

```bash
# Discover project-specific nuances and patterns
find packages/ -name "*.tsx" -o -name "*.ts" -o -name "*.scss" | head -20 | xargs grep -l "class.*extends Component\|TranslatedString\|withAnalytics\|spacing(\|@import.*settings" | head -10
```

## 🎯 PROJECT-SPECIFIC DISCOVERY TASK

### Task: Analyze Codebase for Unique Patterns

**Objective**: Discover all project-specific nuances that differ from standard React practices.

**Steps**:

1. **Package Structure Analysis**
   ```bash
   # Find all package directories
   ls packages/ | grep -E "(integration|utils|core|analytics|locale)"
   
   # Count package types
   ls packages/ | grep -c "integration"
   ls packages/ | grep -c "utils"
   ```

2. **Component Pattern Analysis**
   ```bash
   # Find class components vs functional components
   find packages/core/src/app -name "*.tsx" -exec grep -l "class.*extends Component" {} \; | wc -l
   find packages/core/src/app -name "*.tsx" -exec grep -l "const.*=.*=>" {} \; | wc -l
   ```

3. **Import Pattern Analysis**
   ```bash
   # Find project-specific imports
   find packages/ -name "*.tsx" -o -name "*.ts" | xargs grep -h "import.*@bigcommerce" | sort | uniq -c | sort -nr
   ```

4. **SCSS Pattern Analysis**
   ```bash
   # Find design token usage
   find packages/core/src/scss -name "*.scss" | xargs grep -h "spacing(\|fontSize(\|$color-" | sort | uniq -c | sort -nr
   ```

5. **File Structure Analysis**
   ```bash
   # Analyze file organization patterns
   find packages/core/src -type f -name "*.tsx" | head -20 | sed 's|packages/core/src/||' | sort
   find packages/core/src -type f -name "*.scss" | head -20 | sed 's|packages/core/src/||' | sort
   ```

### Expected Discoveries:

- **Package Naming**: `[payment-method]-integration` pattern
- **Component Types**: Class components for main features
- **Import Sources**: `@bigcommerce/checkout-sdk` dependencies
- **SCSS Tokens**: `spacing()`, `fontSize()`, `$color-` variables
- **File Structure**: `packages/core/src/app/[feature]/` organization

### Output Format:

```markdown
## Discovered Project-Specific Patterns

### 1. Package Structure
- Found X integration packages
- Found Y utility packages
- Pattern: [payment-method]-integration

### 2. Component Patterns
- Class components: X files
- Functional components: Y files
- Main features use class components

### 3. Import Patterns
- Most common: @bigcommerce/checkout-sdk
- HOC pattern: withAnalytics, withBillingAndPaymentContext
- Translation: TranslatedString (not react-i18next)

### 4. SCSS Patterns
- Design tokens: spacing(), fontSize(), $color-
- Import pattern: @import '../../settings/global'
- File structure: components/[feature]/_componentName.scss

### 5. File Organization
- Components: packages/core/src/app/[feature]/
- Styles: packages/core/src/scss/components/[feature]/
- Settings: packages/core/src/scss/settings/[feature]/
```

## 🚀 EXECUTION COMMAND

```bash
# Run the complete discovery task
echo "=== PACKAGE STRUCTURE ===" && \
ls packages/ | grep -E "(integration|utils|core|analytics|locale)" && \
echo -e "\n=== COMPONENT PATTERNS ===" && \
echo "Class components: $(find packages/core/src/app -name "*.tsx" -exec grep -l "class.*extends Component" {} \; | wc -l)" && \
echo "Functional components: $(find packages/core/src/app -name "*.tsx" -exec grep -l "const.*=.*=>" {} \; | wc -l)" && \
echo -e "\n=== IMPORT PATTERNS ===" && \
find packages/ -name "*.tsx" -o -name "*.ts" | xargs grep -h "import.*@bigcommerce" | sort | uniq -c | sort -nr | head -5 && \
echo -e "\n=== SCSS PATTERNS ===" && \
find packages/core/src/scss -name "*.scss" | xargs grep -h "spacing(\|fontSize(\|$color-" | sort | uniq -c | sort -nr | head -5
```

This discovery task will reveal the unique patterns that make this codebase different from standard React projects.
