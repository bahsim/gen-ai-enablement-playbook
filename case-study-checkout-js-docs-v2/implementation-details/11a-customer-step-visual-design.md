---
**Title:** Customer Step Visual Design
**Purpose:** Visual design and layout description of the Customer step from a user's perspective.
**Audience:** All Developers, Designers, QA
**Related:** See `11-visual-design.md` for overall page layout and design system
---

# Customer Step Visual Design

**Visual Layout:**
```
┌──────────────────────────────────────┐
│  [Icon] Customer    [Summary]  [Edit]│
├──────────────────────────────────────┤
│                                      │
│  [Wallet Buttons - Top]              │
│  ┌────────────────────────────────┐ │
│  │ [Apple Pay] [Google Pay] [PayPal]│ │
│  └────────────────────────────────┘ │
│                                      │
│  ───────── OR ─────────              │
│                                      │
│  Email Address:                      │
│  ┌────────────────────────────────┐ │
│  │                                │ │
│  └────────────────────────────────┘ │
│                                      │
│  ☐ Subscribe to newsletter          │
│  ☐ Abandoned cart emails            │
│                                      │
│  [Continue as Guest]                │
│                                      │
│  [Sign In] [Create Account]          │
│                                      │
│  [Wallet Buttons - Bottom]          │
│                                      │
└──────────────────────────────────────┘
```

**View Types:**

1. **Guest View:**
   - **CSS Classes**: `customerView-body`, `guestForm`
   - Large email input field (centered or full width)
   - Subscription checkboxes below email
   - "Continue as Guest" primary button
   - Links to "Sign In" and "Create Account" below button
   - Wallet buttons at top and/or bottom of form
   - Clean, minimal design focused on email entry

2. **Login View:**
   - **CSS Classes**: `loginForm`
   - Email input field
   - Password input field
   - "Sign In" primary button
   - "Continue as guest" link below button
   - "Create account" link below button
   - Error message display area (if login fails)

3. **Create Account View:**
   - **CSS Classes**: `createAccountForm`
   - Email input field
   - Password input field
   - Confirm password input field
   - First name input field
   - Last name input field
   - Password requirements display (strength indicator)
   - "Create Account" primary button
   - Validation errors shown below each field

4. **SuggestedLogin View:**
   - Similar to Login view
   - Additional message/banner encouraging sign-in
   - "Continue as guest" option still available
   - Less prominent than EnforcedLogin

5. **EnforcedLogin View:**
   - Similar to Login view
   - No "Continue as guest" option
   - Prominent message explaining why login is required
   - Error styling (rate limiting message)

6. **CancellableEnforcedLogin View:**
   - Similar to EnforcedLogin
   - Cancel button available (conditional)
   - Error styling (forbidden message)

**Wallet Buttons:**
- **CSS Classes**: `checkoutButtonList`, `checkoutButton`, `checkoutButtonContainer`
- Branded payment provider buttons (Apple Pay, Google Pay, PayPal, etc.)
- Standard button sizes and styling from payment providers
- Can appear at top, bottom, or both positions
- Horizontal layout, responsive wrapping
- Click triggers payment provider flow

**Visual States:**
- **Loading**: Disabled form, spinner on submit button
- **Error**: Red error message below form or in modal
- **Success**: Smooth transition to next step
- **View transition**: Fade animation between view types

**Variations:**

1. **Guest View (Default):**
   - Email input prominent, centered
   - Subscription checkboxes visible
   - Wallet buttons at top and/or bottom
   - "Continue as Guest" primary button
   - Links to sign in/create account

2. **Login View:**
   - Email + password inputs
   - "Sign In" primary button
   - "Continue as guest" and "Create account" links
   - Error message area below form

3. **Create Account View:**
   - Full form: email, password, confirm password, first name, last name
   - Password strength indicator
   - "Create Account" primary button
   - Validation errors per field

4. **SuggestedLogin View:**
   - Similar to Login view
   - Banner/message: "We found an account with this email. Sign in for faster checkout."
   - "Continue as guest" option still available
   - Less prominent than enforced login

5. **EnforcedLogin View:**
   - Similar to Login view
   - Prominent error message: "Rate limit exceeded. Please sign in to continue."
   - No "Continue as guest" option
   - Error styling (red border, warning icon)

6. **CancellableEnforcedLogin View:**
   - Similar to EnforcedLogin view
   - "Cancel" button available (conditional)
   - Error message: "Access forbidden. Please sign in or cancel."
   - Error styling

7. **Authenticated User:**
   - Customer info display (read-only)
   - No form, just customer details
   - Step appears complete automatically

8. **Wallet Payment Selected:**
   - Form hidden or minimal
   - Step appears complete
   - No edit button (non-editable)

**Layout Variations:**

1. **Wallet Buttons Placement:**

   **Top Only:**
   ```
   ┌──────────────────────────────────────┐
   │  [Apple Pay] [Google Pay] [PayPal]  │
   │  ───────── OR ─────────              │
   │  Email: [Input]                      │
   │  [Continue as Guest]                 │
   └──────────────────────────────────────┘
   ```

   **Bottom Only:**
   ```
   ┌──────────────────────────────────────┐
   │  Email: [Input]                      │
   │  [Continue as Guest]                 │
   │  ───────── OR ─────────              │
   │  [Apple Pay] [Google Pay] [PayPal]  │
   └──────────────────────────────────────┘
   ```

   **Both:**
   ```
   ┌──────────────────────────────────────┐
   │  [Apple Pay] [Google Pay] [PayPal]  │
   │  ───────── OR ─────────              │
   │  Email: [Input]                      │
   │  [Continue as Guest]                 │
   │  ───────── OR ─────────              │
   │  [Apple Pay] [Google Pay] [PayPal]  │
   └──────────────────────────────────────┘
   ```

2. **Email Input Alignment:**

   **Centered:**
   ```
   ┌──────────────────────────────────────┐
   │         Email Address:                │
   │    ┌────────────────────────────┐    │
   │    │                            │    │
   │    └────────────────────────────┘    │
   │         [Continue as Guest]          │
   └──────────────────────────────────────┘
   ```

   **Left-Aligned:**
   ```
   ┌──────────────────────────────────────┐
   │  Email Address:                      │
   │  ┌────────────────────────────────┐ │
   │  │                                │ │
   │  └────────────────────────────────┘ │
   │  [Continue as Guest]                │
   └──────────────────────────────────────┘
   ```

3. **Form Field Layout:**

   **Single Column (Default):**
   ```
   ┌──────────────────────────────────────┐
   │  Email: [Input]                      │
   │  Password: [Input]                   │
   │  Confirm Password: [Input]          │
   │  First Name: [Input]                 │
   │  Last Name: [Input]                  │
   └──────────────────────────────────────┘
   ```

   **Two Columns (Create Account):**
   ```
   ┌──────────────────────────────────────┐
   │  Email: [Input]                      │
   │  Password: [Input]                   │
   │  Confirm Password: [Input]           │
   │  First Name: [Input]  Last Name: [Input]│
   └──────────────────────────────────────┘
   ```

4. **Button and Link Placement:**

   **Primary Button Centered:**
   ```
   ┌──────────────────────────────────────┐
   │  Email: [Input]                      │
   │                                      │
   │         [Continue as Guest]         │
   │                                      │
   │  [Sign In] [Create Account]          │
   └──────────────────────────────────────┘
   ```

   **Primary Button Left-Aligned:**
   ```
   ┌──────────────────────────────────────┐
   │  Email: [Input]                      │
   │  [Continue as Guest]                 │
   │  [Sign In] [Create Account]          │
   └──────────────────────────────────────┘
   ```

5. **Subscription Checkboxes:**

   **Below Email:**
   ```
   ┌──────────────────────────────────────┐
   │  Email: [Input]                      │
   │  ☐ Subscribe to newsletter          │
   │  ☐ Abandoned cart emails            │
   │  [Continue as Guest]                │
   └──────────────────────────────────────┘
   ```

   **Separate Section:**
   ```
   ┌──────────────────────────────────────┐
   │  Email: [Input]                      │
   │  ──────────────────────────────────  │
   │  Newsletter Preferences:             │
   │  ☐ Subscribe to newsletter          │
   │  ☐ Abandoned cart emails            │
   │  ──────────────────────────────────  │
   │  [Continue as Guest]                │
   └──────────────────────────────────────┘
   ```

   **Inline:**
   ```
   ┌──────────────────────────────────────┐
   │  Email: [Input]                      │
   │  ☐ Newsletter  ☐ Abandoned Cart    │
   │  [Continue as Guest]                │
   └──────────────────────────────────────┘
   ```

