# Checkout Layout Principles

## Overview
This document defines the fundamental layout principles for the checkout page based on the design analysis.

## Layout Structure

### Main Content Area
The main content area is positioned between the header and footer, containing:

#### Header
- **Position**: Above the main content area
- **Content**: Page title and navigation elements
- **Scope**: Header details are out of scope for this documentation

#### Two Primary Sections

##### 1. Checkout Steps Section
- **Position**: Left side of main content area
- **Purpose**: Multi-step checkout process
- **Content**: Form progression through checkout steps

##### 2. Order Summary Section
- **Position**: Right side of main content area  
- **Purpose**: Order details and pricing summary
- **Content**: Cart items, totals, and order information

#### Footer
- **Position**: Below the main content area
- **Scope**: Footer details are out of scope for this documentation

## Design Principles

### Layout Principles
- **Two-column layout** for main content area
- **Clear visual separation** between sections
- **Consistent spacing** and alignment
- **Responsive design** considerations

### Visual Hierarchy
- **Header** provides page context and navigation
- **Checkout Steps** guides user through process
- **Order Summary** provides transaction details
- **Footer** contains additional navigation and legal information

## Implementation Notes

### Component Structure
```
Main Content Area
├── Header
├── Two-Column Layout
│   ├── Checkout Steps (Left)
│   └── Order Summary (Right)
└── Footer
```

### HTML Structure Mapping
```
div.layout.optimizedCheckout-contentPrimary
├── h1.checkout-heading ("Secure Checkout")
├── div.layout-main
│   └── ol.checkout-steps (Checkout Steps Section)
└── aside.layout-cart (Order Summary Section)
└── CheckoutFooter
```

### Customer Checkout Step Structure
```
Customer Checkout Step
├── Customer Header (customerView-body)
└── Email (new position - below header)
```

### CSS Class Mapping
- **Main Content Area**: `div.layout-main` (contains checkout steps)
- **Checkout Steps Section**: `ol.checkout-steps` (inside layout-main)
- **Order Summary Section**: `aside.layout-cart` (sibling to layout-main)

### Layout Implementation Details
- **Two-Column Layout**: Achieved by having `div.layout-main` and `aside.layout-cart` as siblings
- **Main Container**: `div.layout.optimizedCheckout-contentPrimary` handles the overall layout
- **Background**: Light grey background should be applied to the main container, not individual sections
- **Panel Styling**: White panels with rounded corners should be applied to both `.checkout-steps` and `.layout-cart`

### Key Considerations
- **Responsive behavior**: Sections stack on mobile
- **Visual balance**: Equal importance to both sections
- **User flow**: Clear progression through checkout steps
- **Information hierarchy**: Order summary supports decision making

### **Best Practices**
- **Reuse Existing Variables**: Leverage existing design system variables instead of creating duplicates
- **BEM Methodology**: Follow BEM naming convention for all CSS classes
- **No Variable Duplication**: Avoid creating new variables that duplicate existing functionality
- **Nesting**: Use proper SCSS nesting (max 3 levels deep)
- **Mixins**: Use existing mixins and create new ones only when necessary
- **Responsive Design**: Use existing breakpoint mixins and variables

### **Existing Design System Variables to Reuse**
- **Colors**: Use existing `$color-*` variables from the design system
- **Typography**: Use existing `$font-*` variables for font families, sizes, and weights
- **Spacing**: Use existing `$spacing-*` variables for consistent spacing
- **Border Radius**: Use existing `$border-radius-*` variables for corner styling
- **Breakpoints**: Use existing `$breakpoint-*` variables for responsive design
- **Checklist Variables**: Reuse existing `$checklist-*` variables where applicable

### **Variable Reuse Strategy**
- Map design tokens to existing SCSS variables
- Only create new variables when no existing equivalent exists
- Maintain consistency with existing naming conventions
- Reference existing variable files for available options

## **Files to Modify**

### **Component Files**
- `packages/core/src/app/checkout/Checkout.tsx` - Update page title and step indicators
- `packages/core/src/app/billing-and-payment/BillingAndPayment.tsx` - Rename section to "Payment"
- `packages/core/src/app/billing/BillingForm.tsx` - Move email field under Customer section

### **SCSS Files**
- `packages/core/src/scss/components/checkout/checklist/_checklist.scss` - Hide step numerations
- `packages/core/src/scss/components/checkout/_checkout.scss` - Add grey background styling
