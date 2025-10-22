# Styling Guidelines (SCSS)

**Purpose**: Write maintainable, consistent styles that follow the project's design system and avoid magic numbers.

## 1) Design System Overview

This project uses a token-based design system. **Always use tokens instead of hardcoded values.**

### Available Tokens
```scss
// Colors (most common)
color("primary")           // Main brand color
color("greys", "dark")     // Text color
color("whites", "bright")  // Background/contrast
color("ui-border-03")      // Borders

// Typography
fontSize("base")           // 1rem (13px)
fontSize("larger")         // 1.923rem (25px)
fontSize("small")          // 1.385rem (18px)
lineHeight("base")         // 1.5
$fontWeight-semibold       // 600

// Spacing (based on 19.5px rhythm)
spacing("single")          // 19.5px (1.5rem)
spacing("double")          // 39px (3rem)
spacing("half")            // 9.75px (0.75rem)

// Breakpoints
breakpoint("small")        // 551px
breakpoint("medium")       // 768px
breakpoint("large")        // 1024px
```

### Why This Matters
- **Consistency**: All spacing follows the same rhythm
- **Maintainability**: Change one token, update everywhere
- **Accessibility**: Proper contrast ratios and focus states
- **Responsive**: Breakpoints work across all components

### Token Discovery
**Can't find the right token?** Check these files:
- `packages/core/src/scss/settings/global/color/_color.scss` - All color tokens
- `packages/core/src/scss/settings/global/typography/_typography.scss` - Font sizes & weights
- `packages/core/src/scss/settings/global/layout/_layout.scss` - Spacing tokens
- `packages/core/src/scss/settings/global/screensizes/_screensizes.scss` - Breakpoints

## 2) Common Patterns & Examples

### Card Component
```scss
.product-card {
    padding: spacing("single");           // 19.5px all around
    border: 1px solid color("ui-border-03");
    border-radius: 6px;
    
    .product-title {
        font-size: fontSize("base");
        font-weight: $fontWeight-semibold;
        color: color("greys", "dark");
        margin-bottom: spacing("half");   // 9.75px
    }
    
    .product-price {
        font-size: fontSize("larger");
        color: color("primary");
    }
}
```

### Form Elements
```scss
.form-field {
    margin-bottom: spacing("single");
    
    .form-label {
        font-size: fontSize("base");
        font-weight: $fontWeight-medium;
        color: color("greys", "dark");
        margin-bottom: spacing("quarter"); // 4.875px
    }
    
    .form-input {
        padding: spacing("half") spacing("single");
        border: 1px solid color("ui-border-03");
        border-radius: 6px;
        font-size: fontSize("base");
        
        &:focus {
            outline: 2px solid color("primary");
            outline-offset: 2px;
        }
    }
}
```

### Responsive Navigation
```scss
.navigation {
    display: flex;
    gap: spacing("single");
    
    @media (max-width: breakpoint("medium")) {
        flex-direction: column;
        gap: spacing("half");
    }
    
    .nav-link {
        padding: spacing("half") spacing("single");
        color: color("greys", "dark");
        text-decoration: none;
        
        &:hover {
            color: color("primary");
        }
    }
}
```

## 3) Layout Patterns

### Full-Height Layout (Content + Footer in 100vh)
**Problem**: Footer should be at bottom without scrolling on short pages.

**Solution**: Use flexbox with proper hierarchy.

```scss
/* layouts/checkout/_checkout.scss */
.layout {
    display: flex;
    flex-direction: column;
    min-height: 100vh;  // Only the top-level container owns viewport height
}

.layout-main {
    display: flex;
    flex-direction: column;
    flex: 1 0 auto;     // Grow to fill available space
    min-height: 0;      // Critical: allows flex children to size correctly
}

/* components/checkout/checkoutFooter/_checkoutFooter.scss */
.checkout-footer {
    margin-top: auto;   // Push to bottom of flex container
    flex-shrink: 0;     // Never collapse
}
```

**Why this works**: 
- `.layout` owns the 100vh constraint
- `.layout-main` flexes to fill remaining space
- Footer gets pushed to bottom via `margin-top: auto`
- No fixed positioning, no padding hacks

## 4) Component Settings (Eliminate Magic Numbers)

**Problem**: Hardcoded values like `padding-bottom: 120px` are unmaintainable.

**Solution**: Create component-specific settings files.

```scss
// settings/checkout/checkoutFooter/_settings.scss
$checkoutFooter-height: calc(#{spacing("single") * 2} + 1rem + 1px);
$checkoutFooter-padding: spacing("single");
$checkoutFooter-maxWidth: 1200px;

// components/checkout/checkoutFooter/_checkoutFooter.scss
.checkout-footer {
    padding: $checkoutFooter-padding 0;
    
    .checkout-footer-content { 
        max-width: $checkoutFooter-maxWidth; 
    }
}
```

**Benefits**:
- Single source of truth for component dimensions
- Easy to adjust without hunting through CSS
- Self-documenting with meaningful names

## 5) Accessibility Essentials

### Focus States
```scss
.interactive-element:focus-visible {
    outline: 2px solid color("primary");
    outline-offset: 2px;
    border-radius: 2px;
}
```

### High Contrast Support
```scss
@media (prefers-contrast: high) {
    .button {
        border: 2px solid currentColor;
        font-weight: $fontWeight-bold;
    }
}
```

### Reduced Motion
```scss
@media (prefers-reduced-motion: reduce) {
    * {
        transition: none !important;
        animation: none !important;
    }
}
```

## 6) Anti-Patterns to Avoid

### ❌ Magic Numbers
```scss
// BAD
.card { padding: 20px; margin-bottom: 15px; }

// GOOD  
.card { padding: spacing("single"); margin-bottom: spacing("three-quarter"); }
```

### ❌ Hardcoded Heights
```scss
// BAD
.content { min-height: 100vh; } // Causes overflow issues

// GOOD
.layout { min-height: 100vh; }  // Only top-level container
.content { flex: 1 0 auto; }    // Let it grow naturally
```

### ❌ Raw Pixel Values
```scss
// BAD
.title { font-size: 18px; }

// GOOD
.title { font-size: fontSize("small"); }
```

## 7) Troubleshooting Common Issues

### "I need a color that's not in the tokens"
```scss
// BAD: Adding random colors
.error-message { color: #ff6b6b; }

// GOOD: Use existing semantic colors
.error-message { color: color("reds", "dark"); }

// BEST: Request new token if needed for consistency
// Add to _color.scss: $color-error: #ff6b6b;
```

### "My spacing doesn't look right"
```scss
// BAD: Mixing different spacing systems
.card { 
    padding: 16px;           // Random value
    margin-bottom: spacing("single"); // Token value
}

// GOOD: Consistent spacing rhythm
.card { 
    padding: spacing("single");
    margin-bottom: spacing("single");
}
```

### "My component breaks on mobile"
```scss
// BAD: No responsive consideration
.sidebar { width: 300px; }

// GOOD: Mobile-first approach
.sidebar { 
    width: 100%;
    @media (min-width: breakpoint("medium")) {
        width: 300px;
    }
}
```

## 8) Quick Reference

| Need | Use | Example |
|------|-----|---------|
| Spacing | `spacing()` | `padding: spacing("single")` |
| Colors | `color()` | `color: color("greys", "dark")` |
| Typography | `fontSize()`, `lineHeight()` | `font-size: fontSize("base")` |
| Responsive | `breakpoint()` | `@media (max-width: breakpoint("medium"))` |
| Constants | Component settings | `$checkoutFooter-height` |

## 9) Decision Framework

**Before writing any CSS, ask:**

1. **Is there a token for this?** → Use the token
2. **Is this a component-specific constant?** → Create component settings
3. **Does this need to be responsive?** → Use breakpoint() mixin
4. **Is this interactive?** → Add focus states
5. **Will this scale?** → Use flexbox/grid with tokens

**Golden Rule**: If you're typing a number, ask yourself "Is there a token for this?"

## 10) File Organization

```
packages/core/src/scss/
├── settings/
│   ├── global/           # Design system tokens
│   └── checkout/         # Component-specific settings
├── components/
│   └── checkout/         # Component styles
└── layouts/
    └── checkout/         # Layout patterns
```

**New component checklist:**
- [ ] Create `settings/checkout/<component>/_settings.scss`
- [ ] Create `components/checkout/<component>/_<component>.scss`
- [ ] Import in `_components.scss`
- [ ] Use tokens, not magic numbers
- [ ] Add responsive behavior
- [ ] Include accessibility features
