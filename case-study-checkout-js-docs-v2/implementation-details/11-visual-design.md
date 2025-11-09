---
**Title:** Checkout Visual Design - User Perspective
**Purpose:** Visual design and layout description of the checkout page from a user's perspective, including header, footer, sections, and visual states.
**Audience:** All Developers, Designers, QA
---

# Checkout Visual Design - User Perspective

## Overall Page Layout

### Page Structure
```
┌──────────────────────────────────────────┐
│           Header                         │
│  (Logo, navigation, cart icon)           │
├──────────────────┬───────────────────────┤
│                  │                       │
│  Checkout Steps  │   Order Summary       │
│  (Left Column)   │   (Right Column)      │
│                  │                       │
│  - Customer      │   - Cart Items        │
│  - Shipping      │   - Subtotal          │
│  - Billing       │   - Shipping          │
│  - Payment       │   - Tax               │
│                  │   - Total             │
│                  │   - Promotions        │
│                  │                       │
├──────────────────┴───────────────────────┤
│           Footer                         │
│  (Links, legal, copyright)               │
└──────────────────────────────────────────┘
```

### Layout Details

**Main Container:**
- **CSS Class**: `div.layout.optimizedCheckout-contentPrimary`
- Light grey background (Citadel color variables)
- Full width, responsive
- Padding around content sections

**Two-Column Layout:**
- **Left column** (Checkout Steps): 
  - **CSS Class**: `div.layout-main` containing `ol.checkout-steps`
  - ~60-65% width on desktop
- **Right column** (Order Summary): 
  - **CSS Class**: `aside.layout-cart`
  - ~35-40% width on desktop
  - Sticky positioning on desktop
- **Mobile**: Stacks vertically, order summary appears first

**Visual Separation:**
- White panels/cards for both sections
- Rounded corners (Citadel border-radius variables)
- Subtle shadows or borders
- Clear spacing between sections (Citadel spacing variables)

## Header

**Appearance:**
- Top of page, full width
- White or light background
- **CSS Class**: `h1.checkout-heading` for "Secure Checkout" title
- Contains:
  - Store logo (left)
  - "Secure Checkout" or page title (center/left)
  - Cart icon with item count (right, optional)
  - Navigation links (optional)

**Styling:**
- Fixed or sticky positioning (stays visible on scroll)
- Border bottom for separation
- Height: ~60-80px
- Uses Citadel typography variables for heading styles

## Checkout Steps Section (Left Column)

### Container
- **CSS Class**: `ol.checkout-steps` (ordered list of steps)
- **Background**: White panel/card
- **Border**: Subtle border or shadow
- **Border radius**: Citadel border-radius variables (~4-8px)
- **Padding**: Citadel spacing variables (~20-30px)
- **Spacing**: Steps stacked vertically with spacing between

### Step Structure

Each step is a collapsible panel:

**CSS Classes:**
- **Step container**: `li.checkout-step.optimizedCheckout-checkoutStep`
- **Step type modifier**: `checkout-step--customer`, `checkout-step--shipping`, etc.
- **Active state**: `checkout-step--active`
- **Header**: `div.stepHeader` or `div.checkout-view-header`

```
┌──────────────────────────────────────┐
│  [Icon] Customer    [Summary]  [Edit]│  ← Header (3 columns)
├──────────────────────────────────────┤
│                                      │
│  Step Content (form, fields, etc.)   │  ← Content (expanded when active)
│                                      │
└──────────────────────────────────────┘
```

**Step Header (3-column layout):**
- **CSS Classes**: 
  - Container: `div.stepHeader` with state classes (`stepHeader--active`, `stepHeader--inactive`, `is-readonly`, `is-clickable`)
  - Left column: `div.stepHeader-figure.stepHeader-column` - Step icon + step title
  - Middle column: `div.stepHeader-body.stepHeader-column` - Step summary (when complete)
  - Right column: `div.stepHeader-actions.stepHeader-column` - Edit button
- **Left column**: Step icon + step title (e.g., "Customer", "Shipping")
- **Middle column**: Step summary (when complete, e.g., "john@example.com")
- **Right column**: Edit button (when editable and complete)

**Visual States:**

1. **Active Step:**
   - Expanded content visible
   - Header highlighted (blue border or background tint)
   - All form fields visible and editable
   - Submit/continue button visible

2. **Complete Step:**
   - Collapsed by default (content hidden)
   - Check icon next to step title
   - Summary text in middle column (e.g., email, address)
   - Edit button visible in right column
   - Header clickable to expand/edit

3. **Inactive Step:**
   - Collapsed (content hidden)
   - Greyed out appearance
   - No summary text
   - Header clickable to navigate (if prerequisites met)
   - Disabled appearance if prerequisites not met

4. **Read-only Step:**
   - Complete but not editable
   - No edit button
   - Summary text visible
   - Header not clickable

**Step Content:**
- **CSS Classes**: Step-specific content containers (e.g., `customerView-body`, form containers)
- Form fields with labels above inputs
  - **Input classes**: `optimizedCheckout-form-input` or component-specific classes
  - Uses Citadel form styling variables
- Validation errors shown below fields (red text, Citadel error color)
- Submit/continue button at bottom
  - **Button classes**: `button` with variants (`button--primary`, `button--secondary`)
  - Uses Citadel button styling
- Loading states: disabled inputs, spinner on button
- Smooth transitions when expanding/collapsing (CSS transitions)

## Order Summary Section (Right Column)

### Container
- **CSS Class**: `aside.layout-cart`
- **Background**: White panel/card
- **Border**: Subtle border or shadow
- **Border radius**: Citadel border-radius variables (~4-8px)
- **Padding**: Citadel spacing variables (~20-30px)
- **Position**: Sticky (stays visible when scrolling on desktop)

### Content Structure

```
┌──────────────────────────┐
│  Order Summary           │
├──────────────────────────┤
│                          │
│  Cart Items:             │
│  ┌────────────────────┐  │
│  │ [Image] Item Name  │  │
│  │        Qty: 2      │  │
│  │        $XX.XX      │  │
│  └────────────────────┘  │
│  ...                     │
│                          │
│  ─────────────────────   │
│  Subtotal:      $XX.XX   │
│  Shipping:      $XX.XX   │
│  Tax:           $XX.XX   │
│  ─────────────────────   │
│  Total:         $XX.XX   │
│                          │
│  Promotions:             │
│  - Discount code         │
│                          │
└──────────────────────────┘
```

**Visual Elements:**
- **Cart items**: List with thumbnails, names, quantities, prices
- **Price breakdown**: Line items for subtotal, shipping, tax
- **Total**: Highlighted, larger font, bold
- **Promotions**: Discount codes, applied discounts
- **Store credit**: If applied, shown as separate line item

**Styling:**
- Item thumbnails: ~50-60px square images
- Price alignment: Right-aligned numbers
- Separator lines: Light grey horizontal lines
- Total section: Highlighted background or border

## Step-Specific Visual Design

Step-specific visual design details are documented in separate files:

- **Customer Step**: See `11a-customer-step-visual-design.md`
- **Shipping Step**: See `11b-shipping-step-visual-design.md`
- **Billing Step**: See `11c-billing-step-visual-design.md`
- **Payment Step**: See `11d-payment-step-visual-design.md`

## Footer

**Appearance:**
- Bottom of page, full width
- Light grey or white background
- Contains:
  - Links (Privacy Policy, Terms, Help, etc.)
  - Copyright notice
  - Additional navigation

**Styling:**
- Smaller font size
- Centered or left-aligned text
- Border top for separation
- Padding: ~40-60px

## Responsive Design

### Desktop (>768px)
- Two-column layout side by side
- Order summary sticky on right
- Full header and footer visible
- Steps expand/collapse smoothly

### Tablet (768px - 1024px)
- Two-column layout maintained
- Slightly narrower columns
- Order summary may become scrollable if content is long

### Mobile (<768px)
- Single column layout
- Order summary appears first (before steps)
- Header may collapse to hamburger menu
- Steps stack vertically
- Footer simplified
- Touch-friendly button sizes (min 44px height)

## Visual Design System

### Design System Foundation
- **Base Layer**: `@bigcommerce/citadel` - Provides CSS variables for colors, fonts, spacing
- **Component Layer**: `packages/ui` and `packages/core/src/app/ui` - Reusable components with BEM-style CSS classes
- **Application Layer**: `packages/core/src/scss` - Application-specific overrides

### Colors (Citadel Variables)
- **Primary**: `citadel-color(color-primary)` - buttons, links, active states
- **Secondary**: `citadel-color(color-textSecondary)` - text, borders
- **Background**: `citadel-color(color-background)` - page background
- **Panels**: `citadel-color(color-white)` - step and summary containers
- **Success**: `citadel-color(color-success)` - check icons, success states
- **Error**: `citadel-color(color-error)` - validation errors, error messages
- **Warning**: `citadel-color(color-warning)` - warnings, alerts
- **Text**: `citadel-color(color-textPrimary)` - primary text color

### Typography (Citadel Variables)
- **Headings**: `$font-heading-*` variables - Bold, 18-24px
- **Body text**: `$font-body-*` variables - Regular, 14-16px
- **Labels**: `$font-label-*` variables - Medium weight, 14px
- **Small text**: `$font-small-*` variables - Regular, 12px (footer, helper text)
- **Font family**: Citadel font stack variables

### Spacing (Citadel Variables)
- **Section padding**: `$spacing-large` or `$spacing-xlarge` (20-30px)
- **Field spacing**: `$spacing-medium` or `$spacing-large` (16-20px)
- **Step spacing**: `$spacing-medium` to `$spacing-large` (16-24px)
- **Button padding**: `$spacing-medium` vertical, `$spacing-large` horizontal (12-16px / 24-32px)

### Interactive Elements

**Buttons:**
- **CSS Classes**: `button`, `button--primary`, `button--secondary`
- **Primary**: `button--primary` - Blue background (Citadel primary color), white text, rounded corners (Citadel border-radius)
- **Secondary**: `button--secondary` - Grey border, grey text, white background
- **Disabled**: `button--disabled` or `is-disabled` - Greyed out, reduced opacity
- **Loading**: `is-loading` - Spinner icon, disabled state
- Uses Citadel button styling variables

**Input Fields:**
- **CSS Classes**: `optimizedCheckout-form-input` or component-specific input classes
- White background (Citadel white color)
- Border: 1px solid (Citadel border color variable)
- Border radius: Citadel border-radius variables (typically 4px)
- Padding: Citadel spacing variables (typically 12px)
- **Focus state**: Blue border (Citadel primary color), subtle shadow
- **Error state**: Red border (Citadel error color), error message below
- Uses Citadel form input styling

**Links:**
- **CSS Classes**: Standard anchor tags or link components
- Blue color (Citadel primary color), underlined on hover
- Active state: Darker blue
- Uses Citadel link styling variables

## Visual States and Feedback

### Loading States
- Skeleton loaders for content being fetched
- Spinner on buttons during submission
- Disabled form fields
- Greyed out appearance

### Error States
- Red border on invalid fields
- Error message below field (red text)
- Error modal for critical errors
- Recovery action buttons in error modal

### Success States
- Green check icon for completed steps
- Success message after submission
- Smooth transitions between states

### Hover States
- Buttons: Slightly darker background
- Links: Underline appears
- Clickable steps: Cursor changes to pointer
- Input fields: Border color change

### Focus States
- Input fields: Blue border, subtle shadow
- Buttons: Outline or border highlight
- Keyboard navigation: Visible focus indicators

## Accessibility Considerations

- **Color contrast**: WCAG AA compliant (4.5:1 for text) - enforced by Citadel design system
- **Focus indicators**: Visible outlines for keyboard navigation (Citadel focus styles)
- **Screen reader support**: Semantic HTML (`ol.checkout-steps`, `aside.layout-cart`), ARIA labels
- **Touch targets**: Minimum 44x44px for mobile (Citadel touch target guidelines)
- **Text size**: Minimum 14px, scalable (Citadel typography variables)
- **Error communication**: Both visual and text-based (error classes and ARIA error attributes)
- **Form labels**: Proper `htmlFor` attributes linking labels to inputs (Citadel form patterns)

## CSS Class Reference

### Layout Classes
- `layout.optimizedCheckout-contentPrimary` - Main container
- `layout-main` - Left column container
- `layout-cart` - Right column (order summary)
- `checkout-steps` - Steps ordered list
- `checkout-heading` - Page heading

### Step Classes
- `checkout-step` - Individual step container
- `optimizedCheckout-checkoutStep` - BigCommerce step styling
- `checkout-step--{type}` - Step type modifier (customer, shipping, billing, payment)
- `checkout-step--active` - Active step state
- `stepHeader` - Step header container
- `stepHeader-figure` - Header left column (icon + title)
- `stepHeader-body` - Header middle column (summary)
- `stepHeader-actions` - Header right column (edit button)
- `stepHeader-column` - Column layout utility
- `is-readonly` - Read-only state modifier
- `is-clickable` - Clickable state modifier

### Form Classes
- `optimizedCheckout-form-input` - Form input styling
- `button`, `button--primary`, `button--secondary` - Button variants
- `is-loading`, `is-disabled` - Button states
- Form fieldset and legend classes from design system

### Design System Variables
All styling uses Citadel CSS variables accessed via:
- `citadel-color(color-*)` - Color variables
- `$font-*` - Typography variables
- `$spacing-*` - Spacing variables
- `$border-radius-*` - Border radius variables

