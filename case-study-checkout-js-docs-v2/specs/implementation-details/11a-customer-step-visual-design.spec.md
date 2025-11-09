---
**Title:** Spec for: Customer Step Visual Design
**Purpose:** To define the structure and mandatory content for visual design and layout description of the Customer step from a user's perspective.
**Audience:** Internal
---

# Spec: Customer Step Visual Design

## 1. Document Purpose

The document `11a-customer-step-visual-design.md` must serve as a **detailed visual design reference** for the Customer step from a user's perspective, including layout, view types, wallet buttons, visual states, and layout variations.

## 2. Mandatory Sections

The document must contain the following sections:

### 1. Visual Layout
*   Must include ASCII diagram showing overall Customer step layout
*   Must show wallet buttons, email input, checkboxes, buttons, links
*   Must use proper vertical alignment in ASCII diagram

### 2. View Types
*   Must describe each view type:
    *   Guest View
    *   Login View
    *   Create Account View
    *   SuggestedLogin View
    *   EnforcedLogin View
    *   CancellableEnforcedLogin View
*   For each view type, must include:
    *   CSS classes
    *   Form fields
    *   Buttons and links
    *   Visual appearance

### 3. Wallet Buttons
*   Must describe wallet button appearance
*   Must list CSS classes
*   Must describe button placement (top, bottom, both)
*   Must describe responsive behavior

### 4. Visual States
*   Must describe:
    *   Loading state
    *   Error state
    *   Success state
    *   View transition animations

### 5. Variations
*   Must describe all view type variations:
    *   Guest View (Default)
    *   Login View
    *   Create Account View
    *   SuggestedLogin View
    *   EnforcedLogin View
    *   CancellableEnforcedLogin View
    *   Authenticated User
    *   Wallet Payment Selected
*   Each variation must describe visual differences

### 6. Layout Variations
*   Must include ASCII diagrams for:
    *   Wallet Buttons Placement (Top Only, Bottom Only, Both)
    *   Email Input Alignment (Centered, Left-Aligned)
    *   Form Field Layout (Single Column, Two Columns)
    *   Button and Link Placement (Centered, Left-Aligned)
    *   Subscription Checkboxes (Below Email, Separate Section, Inline)
*   All ASCII diagrams must have proper vertical alignment

## 3. Principles

*   **User perspective:** Describes visual appearance from user's viewpoint
*   **CSS classes:** Includes actual CSS class names
*   **ASCII diagrams:** Uses ASCII art with proper vertical alignment
*   **Complete coverage:** Includes all view types and variations
*   **Visual focus:** Emphasizes visual appearance, not implementation logic
*   **No fabrications:** Only includes verified visual details

## 4. Mandatory Content

### ASCII Diagrams
*   Must use consistent box-drawing characters
*   Must align all lines vertically with box borders
*   Must show form fields, buttons, and layout clearly
*   Must use proper spacing and indentation

### CSS Classes
*   Must list actual CSS class names for:
    *   View containers (e.g., `customerView-body`, `guestForm`)
    *   Wallet buttons (e.g., `checkoutButtonList`, `checkoutButton`)
    *   Form elements
*   Must match actual codebase classes

### View Type Descriptions
*   Must describe visual appearance of each view
*   Must list form fields for each view
*   Must describe button and link placement
*   Must describe visual differences between views

### Layout Variations
*   Must show multiple layout options for:
    *   Wallet button placement
    *   Form field alignment
    *   Button placement
    *   Checkbox placement
*   Each variation must have ASCII diagram

## 5. Verification Criteria

*   CSS class names match actual codebase
*   View types match Customer step architectural docs
*   ASCII diagrams are properly aligned vertically
*   All view types are covered
*   Layout variations are realistic and match implementation
*   Visual descriptions match actual UI appearance

## 6. Maintenance Note

This document must be updated when:
*   CSS class names change
*   View types change
*   Layout structure changes
*   New visual variations are added
*   Wallet button placement changes
*   Form field layouts change

