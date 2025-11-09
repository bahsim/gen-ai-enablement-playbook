---
**Title:** Spec for: Payment Step Visual Design
**Purpose:** To define the structure and mandatory content for visual design and layout description of the Payment step from a user's perspective.
**Audience:** Internal
---

# Spec: Payment Step Visual Design

## 1. Document Purpose

The document `11d-payment-step-visual-design.md` must serve as a **detailed visual design reference** for the Payment step from a user's perspective, including layout, payment method selection, payment forms by method, store credit, submit button, error modal, order status, visual states, and layout variations.

## 2. Mandatory Sections

The document must contain the following sections:

### 1. Visual Layout
*   Must include ASCII diagram showing overall Payment step layout
*   Must show payment method selector, payment form, store credit, submit button
*   Must use proper vertical alignment in ASCII diagram

### 2. Payment Method Selection
*   Must describe payment method selector appearance
*   Must list CSS classes
*   Must describe selection format (radio buttons, cards)
*   Must describe selected method highlighting
*   Must describe method loading behavior

### 3. Payment Forms by Method
*   Must describe each payment method form:
    *   Stripe (Credit Card)
    *   PayPal
    *   Braintree
    *   Apple Pay / Google Pay
    *   Buy Now Pay Later
*   For each method, must include:
    *   CSS classes
    *   Visual appearance
    *   Form fields
    *   Styling characteristics

### 4. Store Credit Display
*   Must describe store credit display appearance
*   Must list CSS classes
*   Must describe display format (banner, card, inline, badge)
*   Must describe "Remove" button

### 5. Submit Button
*   Must describe submit button appearance
*   Must list CSS classes
*   Must describe button states (loading, error, disabled)
*   Must describe button placement options

### 6. Error Modal
*   Must describe error modal appearance
*   Must list CSS classes
*   Must describe modal layout (centered, full-screen, bottom sheet, inline)
*   Must describe recovery action buttons

### 7. Order Status Indicator
*   Must describe order status indicator appearance
*   Must list CSS classes
*   Must describe indicator placement (replaces form, above form, below button, modal)
*   Must describe processing and success states

### 8. Visual States
*   Must describe:
    *   Loading state
    *   Submitting state
    *   Error state
    *   Success state
    *   Payment method change animation

### 9. Variations
*   Must describe all variations:
    *   Stripe Payment Form
    *   PayPal Payment Form
    *   Braintree Payment Form
    *   Apple Pay
    *   Google Pay
    *   Buy Now Pay Later (BNPL)
    *   Generic Payment Method
    *   Store Credit Applied
    *   No Store Credit
    *   Order Submission in Progress
    *   Order Complete
    *   Payment Error
    *   Cart Total Changed
    *   Spam Protection Error
    *   Payment Method Invalid Error
*   Each variation must describe visual differences

### 10. Layout Variations
*   Must include ASCII diagrams for:
    *   Payment Method List Layout (Radio Buttons, Card Grid, Horizontal Tabs, Sidebar, Dropdown)
    *   Payment Form Placement (Below Selection, Replaces Selection, Side-by-Side)
    *   Stripe Elements Layout (Single Row, Two Rows, Three Rows, Grouped)
    *   Submit Button Placement (Full Width, Centered, Right-Aligned, Sticky)
    *   Store Credit Display (Banner, Card, Inline with Total, Badge)
    *   Error Modal Layout (Centered Modal, Full-Screen Overlay, Bottom Sheet, Inline Error)
    *   Order Status Indicator (Replaces Form, Above Form, Below Button, Modal)
    *   Payment Method Icons (Left of Name, Above Name, Background/Branding)
    *   Form Field Grouping (Ungrouped, Grouped, Fieldset)
    *   Loading State Layout (Skeleton Loaders, Spinner Overlay, Disabled Form)
*   All ASCII diagrams must have proper vertical alignment

## 3. Principles

*   **User perspective:** Describes visual appearance from user's viewpoint
*   **CSS classes:** Includes actual CSS class names
*   **ASCII diagrams:** Uses ASCII art with proper vertical alignment
*   **Complete coverage:** Includes all payment methods and variations
*   **Visual focus:** Emphasizes visual appearance, not implementation logic
*   **No fabrications:** Only includes verified visual details

## 4. Mandatory Content

### ASCII Diagrams
*   Must use consistent box-drawing characters
*   Must align all lines vertically with box borders
*   Must show form fields, buttons, modals, and layout clearly
*   Must use proper spacing and indentation

### CSS Classes
*   Must list actual CSS class names for:
    *   Payment method list (e.g., `paymentMethodList`, `paymentMethodOption`)
    *   Payment forms (e.g., `stripePaymentForm`, `payPalPaymentForm`, `braintreePaymentForm`)
    *   Store credit (e.g., `storeCreditDisplay`)
    *   Submit button (e.g., `paymentSubmitButton`)
    *   Error modal (e.g., `errorModal`)
    *   Order status (e.g., `orderStatusIndicator`)
*   Must match actual codebase classes

### Payment Method Forms
*   Must describe visual differences between payment methods
*   Must describe Stripe Elements styling
*   Must describe hosted fields (Braintree)
*   Must describe wallet button styling
*   Must describe BNPL provider forms

### Layout Variations
*   Must show multiple layout options for:
    *   Payment method selection
    *   Payment form placement
    *   Stripe Elements layout
    *   Submit button placement
    *   Store credit display
    *   Error modal layout
    *   Order status indicator
    *   Form field grouping
    *   Loading states
*   Each variation must have ASCII diagram

## 5. Verification Criteria

*   CSS class names match actual codebase
*   Payment methods match Payment step architectural docs
*   ASCII diagrams are properly aligned vertically
*   All payment methods and variations are covered
*   Layout variations are realistic and match implementation
*   Visual descriptions match actual UI appearance

## 6. Maintenance Note

This document must be updated when:
*   CSS class names change
*   Payment methods change
*   Layout structure changes
*   New visual variations are added
*   Payment form layouts change
*   Error handling UI changes
*   Order status display changes

