---
**Title:** Spec for: Billing Step Visual Design
**Purpose:** To define the structure and mandatory content for visual design and layout description of the Billing step from a user's perspective.
**Audience:** Internal
---

# Spec: Billing Step Visual Design

## 1. Document Purpose

The document `11c-billing-step-visual-design.md` must serve as a **detailed visual design reference** for the Billing step from a user's perspective, including layout, payment method variations, address handling, visual states, and layout variations.

## 2. Mandatory Sections

The document must contain the following sections:

### 1. Visual Layout
*   Must include ASCII diagram showing overall Billing step layout
*   Must show payment method selector, address form, checkboxes, buttons
*   Must use proper vertical alignment in ASCII diagram

### 2. Payment Method Variations
*   Must describe each payment method variation:
    *   Default Payment Method
    *   Amazon Pay
    *   PayPal Fastlane
    *   Google Pay (with feature flag)
    *   Wallet Payment Selected
*   For each variation, must include:
    *   CSS classes
    *   Visual appearance
    *   Form structure
    *   Editable vs. read-only fields

### 3. Payment Method Selector
*   Must describe payment method selector appearance
*   Must list CSS classes
*   Must describe selection format (radio buttons, cards, tabs, dropdown)
*   Must describe selected method highlighting

### 4. Visual States
*   Must describe:
    *   Loading state
    *   Error state
    *   Payment method change animation
    *   Static address display appearance

### 5. Variations
*   Must describe all variations:
    *   Default Payment Method
    *   Amazon Pay
    *   PayPal Fastlane (Guest User)
    *   Google Pay (with Feature Flag)
    *   PayPal Payment Methods (All Variants)
    *   Wallet Payment Selected
    *   Multiple Payment Methods Available
    *   Single Payment Method
    *   Billing Same as Shipping (Checked)
    *   No Shipping Step (Digital Goods)
*   Each variation must describe visual differences

### 6. Layout Variations
*   Must include ASCII diagrams for:
    *   Payment Method Selector Placement (Top of Form, Sidebar, Inline)
    *   Payment Method Display (Radio Buttons, Card Grid, Horizontal Tabs, Dropdown)
    *   Address Form Layout (Single Column, Two Columns, Three Columns)
    *   Static Address Display (Read-only Fields, Text Display, Card Format)
    *   PayPal Address Selector (Dropdown, Radio List, Card Grid)
    *   Custom Fields Placement (Below Address, Separate Section, Side-by-Side)
    *   "Billing Same as Shipping" Checkbox (Above Address, Below Address, Inline with Header)
*   All ASCII diagrams must have proper vertical alignment

## 3. Principles

*   **User perspective:** Describes visual appearance from user's viewpoint
*   **CSS classes:** Includes actual CSS class names
*   **ASCII diagrams:** Uses ASCII art with proper vertical alignment
*   **Complete coverage:** Includes all payment method variations
*   **Visual focus:** Emphasizes visual appearance, not implementation logic
*   **No fabrications:** Only includes verified visual details

## 4. Mandatory Content

### ASCII Diagrams
*   Must use consistent box-drawing characters
*   Must align all lines vertically with box borders
*   Must show form fields, selectors, and layout clearly
*   Must use proper spacing and indentation

### CSS Classes
*   Must list actual CSS class names for:
    *   Forms (e.g., `billingForm`)
    *   Payment method selector (e.g., `paymentMethodSelector`, `paymentMethodOption`)
    *   Static address display (e.g., `staticAddressDisplay`)
    *   PayPal selector (e.g., `payPalAddressSelector`)
*   Must match actual codebase classes

### Payment Method Variations
*   Must describe visual differences between payment methods
*   Must describe editable vs. read-only fields
*   Must describe static address display appearance
*   Must describe custom fields placement

### Layout Variations
*   Must show multiple layout options for:
    *   Payment method selector placement
    *   Payment method display format
    *   Address form columns
    *   Static address display format
    *   PayPal address selector format
    *   Custom fields placement
    *   "Billing same as shipping" checkbox placement
*   Each variation must have ASCII diagram

## 5. Verification Criteria

*   CSS class names match actual codebase
*   Payment method variations match Billing step architectural docs
*   ASCII diagrams are properly aligned vertically
*   All payment method variations are covered
*   Layout variations are realistic and match implementation
*   Visual descriptions match actual UI appearance

## 6. Maintenance Note

This document must be updated when:
*   CSS class names change
*   Payment method variations change
*   Layout structure changes
*   New visual variations are added
*   Payment method selector changes
*   Address form layouts change

