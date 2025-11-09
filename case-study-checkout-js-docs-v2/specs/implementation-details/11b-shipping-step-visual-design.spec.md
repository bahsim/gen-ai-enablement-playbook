---
**Title:** Spec for: Shipping Step Visual Design
**Purpose:** To define the structure and mandatory content for visual design and layout description of the Shipping step from a user's perspective.
**Audience:** Internal
---

# Spec: Shipping Step Visual Design

## 1. Document Purpose

The document `11b-shipping-step-visual-design.md` must serve as a **detailed visual design reference** for the Shipping step from a user's perspective, including layout, single/multi-shipping modes, shipping options, visual states, and layout variations.

## 2. Mandatory Sections

The document must contain the following sections:

### 1. Visual Layout
*   Must include ASCII diagram showing overall Shipping step layout
*   Must show address form, shipping options, checkboxes, buttons
*   Must use proper vertical alignment in ASCII diagram

### 2. Single Shipping Mode
*   Must describe single shipping mode appearance
*   Must list CSS classes
*   Must describe form structure (address form, shipping method selection, checkboxes)
*   Must describe continue button

### 3. Multi-Shipping Mode
*   Must describe multi-shipping mode appearance
*   Must list CSS classes
*   Must describe toggle switch
*   Must describe consignment cards/panels
*   Must describe item assignment interface
*   Must describe unassigned items section

### 4. Shipping Options Display
*   Must describe shipping options visual appearance
*   Must list CSS classes
*   Must describe option display format (radio buttons, cards)
*   Must describe selected option highlighting
*   Must describe loading skeleton

### 5. Visual States
*   Must describe:
    *   Loading state
    *   Error state
    *   Promotional items conflict modal
    *   Mode switch animation

### 6. Variations
*   Must describe all variations:
    *   Single Shipping Mode (Default)
    *   Multi-Shipping Mode
    *   StripeUPE Special Case
    *   Custom Shipping Selected
    *   Promotional Items Conflict
    *   Digital Goods Only (Step Not Required)
    *   No Shipping Options Available
    *   Shipping Options Loading
*   Each variation must describe visual differences

### 7. Layout Variations
*   Must include ASCII diagrams for:
    *   Address Form Layout (Single Column, Two Columns, Three Columns)
    *   Shipping Options Display (Radio Buttons, Card Style Vertical, Card Style Horizontal, Grid Layout)
    *   Multi-Shipping Consignment Layout (Stacked Cards, Accordion)
    *   Item Assignment Interface (Checkbox List, Dropdown Selection)
    *   Toggle Switch Placement (Top of Form, Above Address)
    *   Order Comments Placement (Below Shipping Options, Below Address, Separate Section)
*   All ASCII diagrams must have proper vertical alignment

## 3. Principles

*   **User perspective:** Describes visual appearance from user's viewpoint
*   **CSS classes:** Includes actual CSS class names
*   **ASCII diagrams:** Uses ASCII art with proper vertical alignment
*   **Complete coverage:** Includes all modes and variations
*   **Visual focus:** Emphasizes visual appearance, not implementation logic
*   **No fabrications:** Only includes verified visual details

## 4. Mandatory Content

### ASCII Diagrams
*   Must use consistent box-drawing characters
*   Must align all lines vertically with box borders
*   Must show form fields, options, and layout clearly
*   Must use proper spacing and indentation

### CSS Classes
*   Must list actual CSS class names for:
    *   Forms (e.g., `shippingForm`, `multiShippingForm`)
    *   Address forms (e.g., `addressForm`)
    *   Shipping options (e.g., `shippingMethodList`, `shippingOption`)
    *   Consignments (e.g., `consignmentList`)
*   Must match actual codebase classes

### Mode Descriptions
*   Must describe visual differences between single and multi-shipping modes
*   Must describe toggle switch appearance
*   Must describe consignment card appearance
*   Must describe item assignment interface

### Layout Variations
*   Must show multiple layout options for:
    *   Address form columns
    *   Shipping options display
    *   Consignment layout
    *   Item assignment
    *   Toggle placement
    *   Order comments placement
*   Each variation must have ASCII diagram

## 5. Verification Criteria

*   CSS class names match actual codebase
*   Modes match Shipping step architectural docs
*   ASCII diagrams are properly aligned vertically
*   All modes and variations are covered
*   Layout variations are realistic and match implementation
*   Visual descriptions match actual UI appearance

## 6. Maintenance Note

This document must be updated when:
*   CSS class names change
*   Shipping modes change
*   Layout structure changes
*   New visual variations are added
*   Shipping options display changes
*   Multi-shipping interface changes

