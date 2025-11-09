---
**Title:** Payment Step Visual Design
**Purpose:** Visual design and layout description of the Payment step from a user's perspective.
**Audience:** All Developers, Designers, QA
**Related:** See `11-visual-design.md` for overall page layout and design system
---

# Payment Step Visual Design

**Visual Layout:**
```
┌──────────────────────────────────────┐
│  [Icon] Payment    [Summary]  [Edit]│
├──────────────────────────────────────┤
│                                      │
│  Payment Method:                     │
│  ○ Credit Card / Stripe              │
│  ○ PayPal                            │
│  ○ Apple Pay                         │
│  ○ Google Pay                        │
│                                      │
│  [Payment Form Based on Selection]   │
│                                      │
│  ┌────────────────────────────────┐ │
│  │ Card Number: [Input]            │ │
│  │ Expiry: [MM/YY] CVV: [Input]  │ │
│  │ Name: [Input]                  │ │
│  └────────────────────────────────┘ │
│                                      │
│  Store Credit Applied: $10.00        │
│  [Remove]                            │
│                                      │
│  ┌────────────────────────────────┐ │
│  │        [Place Order]           │ │
│  └────────────────────────────────┘ │
│                                      │
└──────────────────────────────────────┘
```

**Payment Method Selection:**
- **CSS Classes**: `paymentMethodList`, `paymentMethodOption`
- Radio buttons or card-style selection
- Each option shows:
  - Payment method icon/logo
  - Payment method name
  - Description or benefits
- Selected method highlighted
- Clicking loads payment method-specific form below

**Payment Forms by Method:**

1. **Stripe (Credit Card):**
   - **CSS Classes**: `stripePaymentForm`
   - Stripe Elements styling (custom-styled inputs)
   - Card number input (formatted with spaces)
   - Expiry date input (MM/YY format)
   - CVV input (3-4 digits)
   - Cardholder name input
   - Billing postal code input
   - Real-time validation feedback
   - Stripe branding

2. **PayPal:**
   - **CSS Classes**: `payPalPaymentForm`
   - PayPal button or iframe embedded
   - PayPal branding and styling
   - Click triggers PayPal flow

3. **Braintree:**
   - **CSS Classes**: `braintreePaymentForm`
   - Hosted fields (secure iframes)
   - Card number, expiry, CVV in separate iframes
   - Braintree styling
   - Secure badge/indicator

4. **Apple Pay / Google Pay:**
   - **CSS Classes**: `walletPaymentForm`
   - Branded wallet button
   - Standard wallet button styling from providers
   - Click triggers wallet flow

5. **Buy Now Pay Later:**
   - **CSS Classes**: `bnplPaymentForm`
   - BNPL provider-specific form
   - Provider branding
   - Additional fields as required by provider

**Store Credit Display:**
- **CSS Classes**: `storeCreditDisplay`
- Banner or card showing applied store credit amount
- "Remove" button/link
- Highlighted styling (green or success color)
- Appears above submit button

**Submit Button:**
- **CSS Classes**: `paymentSubmitButton`, `button--primary`
- Large, prominent "Place Order" button
- Full width or centered
- Loading state: spinner, disabled
- Error state: Red border or error message above
- Disabled when payment method not selected or form invalid

**Error Modal:**
- **CSS Classes**: `errorModal`
- Overlay modal with semi-transparent background
- Centered modal with error message
- Recovery action buttons:
  - "Reload checkout" button
  - "Go to cart" button
  - "Close" button
- Modal styling: white background, rounded corners, shadow

**Order Status Indicator:**
- **CSS Classes**: `orderStatusIndicator`
- Processing spinner when order is being created
- Success indicator when order is complete
- Progress message or completion confirmation
- Appears below submit button or replaces form

**Visual States:**
- **Loading**: Skeleton loaders for payment methods, disabled form
- **Submitting**: Disabled form, spinner on submit button, beforeunload warning
- **Error**: Error modal overlay, red error messages
- **Success**: Success indicator, transition to order confirmation
- **Payment method change**: Smooth form re-render, payment method registration

**Variations:**

1. **Stripe Payment Form:**
   - **Visual**: Stripe Elements styling (custom-styled inputs)
   - Card number: Formatted with spaces (e.g., "4242 4242 4242 4242")
   - Expiry: MM/YY format
   - CVV: 3-4 digit input
   - Cardholder name: Text input
   - Billing postal code: Text input
   - Real-time validation: Green checkmarks, red X icons
   - Stripe branding badge

2. **PayPal Payment Form:**
   - **Visual**: PayPal button or iframe embedded
   - Large PayPal-branded button
   - Or PayPal iframe for payment flow
   - PayPal logo and styling
   - Click triggers PayPal popup/redirect

3. **Braintree Payment Form:**
   - **Visual**: Hosted fields (secure iframes)
   - Card number: Secure iframe input
   - Expiry: Secure iframe input
   - CVV: Secure iframe input
   - Braintree secure badge
   - PCI compliance indicator

4. **Apple Pay:**
   - **Visual**: Apple Pay branded button
   - Standard Apple Pay button styling
   - Black button with Apple Pay logo
   - Click triggers Apple Pay sheet

5. **Google Pay:**
   - **Visual**: Google Pay branded button
   - Standard Google Pay button styling
   - Google Pay logo and colors
   - Click triggers Google Pay sheet

6. **Buy Now Pay Later (BNPL):**
   - **Visual**: Provider-specific form
   - Klarna, Afterpay, Affirm, etc.
   - Provider branding and styling
   - Additional fields as required (e.g., phone number, date of birth)
   - Provider-specific validation

7. **Generic Payment Method:**
   - **Visual**: Standard form fields
   - Payment method-specific inputs
   - Custom validation rules
   - Provider branding if applicable

8. **Store Credit Applied:**
   - **Visual**: Banner or card above submit button
   - Green/success color styling
   - Shows amount: "Store Credit: $10.00"
   - "Remove" button/link
   - Highlighted appearance

9. **No Store Credit:**
   - No store credit display
   - Submit button directly visible

10. **Order Submission in Progress:**
    - Form disabled
    - Submit button shows spinner
    - "Processing..." message
    - Beforeunload warning if user tries to leave

11. **Order Complete:**
    - Form replaced with success indicator
    - Check icon and confirmation message
    - Order details displayed
    - Redirect to order confirmation page

12. **Payment Error:**
    - Error modal overlay
    - Error message (localized)
    - Recovery buttons: "Reload checkout", "Go to cart", "Close"
    - Modal styling: centered, white background, shadow

13. **Cart Total Changed:**
    - Payment methods reload automatically
    - Skeleton loaders while reloading
    - Form may update based on new methods
    - Notification message (optional)

14. **Spam Protection Error:**
    - Error modal with spam protection message
    - "Reload checkout" button (reloads to reset spam state)
    - Prevents further submission attempts

15. **Payment Method Invalid Error:**
    - Payment methods reload automatically
    - Error message: "Payment method no longer available"
    - User must select different method

**Layout Variations:**

1. **Payment Method List Layout:**

   **Radio Buttons (Vertical - Default):**
   ```
   ┌──────────────────────────────────────┐
   │  Payment Method:                     │
   │  ○ Credit Card / Stripe              │
   │  ○ PayPal                            │
   │  ○ Apple Pay                         │
   │  ○ Google Pay                        │
   └──────────────────────────────────────┘
   ```

   **Card Grid (2-3 Columns):**
   ```
   ┌──────────────────────────────────────┐
   │  Payment Method:                     │
   │  ┌──────────┐ ┌──────────┐          │
   │  │○ Credit  │ │○ PayPal  │          │
   │  │  Card    │ │          │          │
   │  └──────────┘ └──────────┘          │
   │  ┌──────────┐ ┌──────────┐          │
   │  │○ Apple   │ │○ Google  │          │
   │  │  Pay     │ │  Pay     │          │
   │  └──────────┘ └──────────┘          │
   └──────────────────────────────────────┘
   ```

   **Horizontal Tabs:**
   ```
   ┌──────────────────────────────────────┐
   │  [Credit Card] [PayPal] [Apple Pay]  │
   │  [Google Pay]                        │
   │  ──────────────────────────────────  │
   │  [Selected Method Form]              │
   └──────────────────────────────────────┘
   ```

   **Sidebar (Two-Column):**
   ```
   ┌──────────────────┬──────────────────┐
   │  Payment Method:  │  Payment Form:   │
   │  ○ Credit Card    │  [Card Form]     │
   │  ○ PayPal         │                  │
   │  ○ Apple Pay      │                  │
   └──────────────────┴──────────────────┘
   ```

   **Dropdown:**
   ```
   ┌──────────────────────────────────────┐
   │  Payment Method:                     │
   │  [Credit Card / Stripe ▼]           │
   │  [Payment Form]                     │
   └──────────────────────────────────────┘
   ```

2. **Payment Form Placement:**

   **Below Selection (Default):**
   ```
   ┌──────────────────────────────────────┐
   │  Payment Method: [Selection]        │
   │  ──────────────────────────────────  │
   │  Card Number: [Input]              │
   │  Expiry: [Input]  CVV: [Input]    │
   │  Name: [Input]                     │
   └──────────────────────────────────────┘
   ```

   **Replaces Selection:**
   ```
   ┌──────────────────────────────────────┐
   │  [Selected: Credit Card] [Change]    │
   │  ──────────────────────────────────  │
   │  Card Number: [Input]              │
   │  Expiry: [Input]  CVV: [Input]    │
   │  Name: [Input]                     │
   └──────────────────────────────────────┘
   ```

   **Side-by-Side:**
   ```
   ┌──────────────────┬──────────────────┐
   │  Payment Method:  │  Payment Form:   │
   │  ○ Credit Card    │  Card Number:    │
   │  ○ PayPal         │  [Input]         │
   │                   │  Expiry: [Input]  │
   └──────────────────┴──────────────────┘
   ```

3. **Stripe Elements Layout:**

   **Single Row (Compact):**
   ```
   ┌──────────────────────────────────────┐
   │  Card Number: [Input]              │
   │  Expiry: [MM/YY]  CVV: [Input]    │
   │  Name: [Input]                     │
   └──────────────────────────────────────┘
   ```

   **Two Rows:**
   ```
   ┌──────────────────────────────────────┐
   │  Card Number: [Input]              │
   │  Expiry: [MM/YY]  CVV: [Input]    │
   │  Name: [Input]                     │
   └──────────────────────────────────────┘
   ```

   **Three Rows (Spacious):**
   ```
   ┌──────────────────────────────────────┐
   │  Card Number: [Input]              │
   │  Expiry: [MM/YY]                    │
   │  CVV: [Input]                       │
   │  Name: [Input]                     │
   └──────────────────────────────────────┘
   ```

   **Grouped (Expiry + CVV):**
   ```
   ┌──────────────────────────────────────┐
   │  Card Number: [Input]              │
   │  ┌──────────┬──────────┐            │
   │  │ Expiry:  │ CVV:     │            │
   │  │ [MM/YY]  │ [Input]  │            │
   │  └──────────┴──────────┘            │
   │  Name: [Input]                     │
   └──────────────────────────────────────┘
   ```

4. **Submit Button Placement:**

   **Full Width:**
   ```
   ┌──────────────────────────────────────┐
   │  [Payment Form]                      │
   │  ──────────────────────────────────  │
   │  ┌────────────────────────────────┐ │
   │  │      [Place Order]              │ │
   │  └────────────────────────────────┘ │
   └──────────────────────────────────────┘
   ```

   **Centered:**
   ```
   ┌──────────────────────────────────────┐
   │  [Payment Form]                      │
   │                                      │
   │         ┌──────────────┐             │
   │         │ Place Order  │             │
   │         └──────────────┘             │
   └──────────────────────────────────────┘
   ```

   **Right-Aligned:**
   ```
   ┌──────────────────────────────────────┐
   │  [Payment Form]                      │
   │                                      │
   │                    ┌──────────────┐  │
   │                    │ Place Order  │  │
   │                    └──────────────┘  │
   └──────────────────────────────────────┘
   ```

   **Sticky (Mobile):**
   ```
   ┌──────────────────────────────────────┐
   │  [Payment Form]                      │
   │  ...                                 │
   │  ──────────────────────────────────  │
   │  ┌────────────────────────────────┐ │
   │  │      [Place Order]              │ │ ← Sticky at bottom
   │  └────────────────────────────────┘ │
   └──────────────────────────────────────┘
   ```

5. **Store Credit Display:**

   **Banner:**
   ```
   ┌──────────────────────────────────────┐
   │  [Payment Form]                      │
   │  ──────────────────────────────────  │
   │  ┌────────────────────────────────┐ │
   │  │ Store Credit Applied: $10.00   │ │
   │  │ [Remove]                        │ │
   │  └────────────────────────────────┘ │
   │  [Place Order]                      │
   └──────────────────────────────────────┘
   ```

   **Card:**
   ```
   ┌──────────────────────────────────────┐
   │  [Payment Form]                      │
   │  ┌────────────────────────────────┐ │
   │  │ Store Credit                    │ │
   │  │ $10.00 Applied                 │ │
   │  │ [Remove]                        │ │
   │  └────────────────────────────────┘ │
   │  [Place Order]                      │
   └──────────────────────────────────────┘
   ```

   **Inline with Total:**
   ```
   ┌──────────────────────────────────────┐
   │  [Payment Form]                      │
   │  ──────────────────────────────────  │
   │  Subtotal:      $100.00              │
   │  Store Credit:  -$10.00            │
   │  Total:          $90.00             │
   │  [Place Order]                      │
   └──────────────────────────────────────┘
   ```

   **Badge:**
   ```
   ┌──────────────────────────────────────┐
   │  [Payment Form]                      │
   │  Total: $90.00 [Store Credit: $10]  │
   │  [Place Order]                      │
   └──────────────────────────────────────┘
   ```

6. **Error Modal Layout:**

   **Centered Modal:**
   ```
   ┌──────────────────────────────────────┐
   │  [Semi-transparent overlay]          │
   │                                      │
   │         ┌──────────────────┐        │
   │         │  Error Message   │        │
   │         │  [Reload] [Cart] │        │
   │         │  [Close]         │        │
   │         └──────────────────┘        │
   └──────────────────────────────────────┘
   ```

   **Full-Screen Overlay:**
   ```
   ┌──────────────────────────────────────┐
   │  [Dark overlay - full screen]        │
   │                                      │
   │         ┌──────────────────┐        │
   │         │  Error Message   │        │
   │         │  [Reload] [Cart] │        │
   │         └──────────────────┘        │
   └──────────────────────────────────────┘
   ```

   **Bottom Sheet (Mobile):**
   ```
   ┌──────────────────────────────────────┐
   │  [Payment Form]                      │
   │  ──────────────────────────────────  │
   │  ┌────────────────────────────────┐ │
   │  │  Error Message                 │ │
   │  │  [Reload] [Cart] [Close]      │ │
   │  └────────────────────────────────┘ │
   └──────────────────────────────────────┘
   ```

   **Inline Error:**
   ```
   ┌──────────────────────────────────────┐
   │  [Payment Form]                      │
   │  ⚠ Error: Payment failed            │
   │  [Reload] [Cart]                    │
   │  [Place Order]                      │
   └──────────────────────────────────────┘
   ```

7. **Order Status Indicator:**

   **Replaces Form:**
   ```
   ┌──────────────────────────────────────┐
   │  ✓ Processing order...               │
   │  Please wait...                     │
   └──────────────────────────────────────┘
   ```

   **Above Form:**
   ```
   ┌──────────────────────────────────────┐
   │  ⏳ Processing order...              │
   │  ──────────────────────────────────  │
   │  [Payment Form - disabled]          │
   └──────────────────────────────────────┘
   ```

   **Below Button:**
   ```
   ┌──────────────────────────────────────┐
   │  [Payment Form]                      │
   │  [Place Order] [Processing...]       │
   │  ⏳ Processing order...              │
   └──────────────────────────────────────┘
   ```

   **Modal:**
   ```
   ┌──────────────────────────────────────┐
   │  [Semi-transparent overlay]          │
   │         ┌──────────────────┐         │
   │         │ ⏳ Processing... │         │
   │         │ Please wait...  │         │
   │         └──────────────────┘         │
   └──────────────────────────────────────┘
   ```

8. **Payment Method Icons:**

   **Left of Name:**
   ```
   ┌──────────────────────────────────────┐
   │  [Icon] Credit Card                  │
   │  [Icon] PayPal                       │
   │  [Icon] Apple Pay                    │
   └──────────────────────────────────────┘
   ```

   **Above Name (Card Style):**
   ```
   ┌──────────────────────────────────────┐
   │  ┌──────────┐ ┌──────────┐          │
   │  │  [Icon]   │ │  [Icon]   │          │
   │  │ Credit    │ │ PayPal    │          │
   │  │  Card     │ │           │          │
   │  └──────────┘ └──────────┘          │
   └──────────────────────────────────────┘
   ```

   **Background/Branding:**
   ```
   ┌──────────────────────────────────────┐
   │  ┌──────────┐ ┌──────────┐          │
   │  │[Branded] │ │[Branded] │          │
   │  │ Credit   │ │ PayPal   │          │
   │  │  Card    │ │          │          │
   │  └──────────┘ └──────────┘          │
   └──────────────────────────────────────┘
   ```

9. **Form Field Grouping:**

   **Ungrouped:**
   ```
   ┌──────────────────────────────────────┐
   │  Card Number: [Input]              │
   │  Expiry: [Input]                   │
   │  CVV: [Input]                      │
   │  Name: [Input]                     │
   └──────────────────────────────────────┘
   ```

   **Grouped:**
   ```
   ┌──────────────────────────────────────┐
   │  Card Details:                      │
   │  Card Number: [Input]              │
   │  Expiry: [Input]  CVV: [Input]    │
   │  ──────────────────────────────────  │
   │  Cardholder Name: [Input]          │
   └──────────────────────────────────────┘
   ```

   **Fieldset:**
   ```
   ┌──────────────────────────────────────┐
   │  ┌─ Card Information ─────────────┐ │
   │  │ Card Number: [Input]           │ │
   │  │ Expiry: [Input]  CVV: [Input] │ │
   │  │ Name: [Input]                 │ │
   │  └───────────────────────────────┘ │
   └──────────────────────────────────────┘
   ```

10. **Loading State Layout:**

    **Skeleton Loaders:**
    ```
    ┌──────────────────────────────────────┐
    │  Payment Method:                     │
    │  ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░ │
    │  ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░ │
    │  ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░ │
    └──────────────────────────────────────┘
    ```

    **Spinner Overlay:**
    ```
    ┌──────────────────────────────────────┐
    │  [Payment Form - visible]            │
    │  [Spinner overlay - semi-transparent] │
    └──────────────────────────────────────┘
    ```

    **Disabled Form:**
    ```
    ┌──────────────────────────────────────┐
    │  Payment Method: [Disabled]         │
    │  Card Number: [Disabled]            │
    │  [Place Order] [⏳ Loading...]     │
    └──────────────────────────────────────┘
    ```

