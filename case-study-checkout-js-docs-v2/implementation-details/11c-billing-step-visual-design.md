---
**Title:** Billing Step Visual Design
**Purpose:** Visual design and layout description of the Billing step from a user's perspective.
**Audience:** All Developers, Designers, QA
**Related:** See `11-visual-design.md` for overall page layout and design system
---

# Billing Step Visual Design

**Visual Layout:**
```
┌──────────────────────────────────────┐
│  [Icon] Billing     [Summary]  [Edit]│
├──────────────────────────────────────┤
│                                      │
│  Payment Method:                     │
│  ○ Credit Card                       │
│  ○ PayPal                            │
│  ○ Apple Pay                         │
│                                      │
│  Billing Address:                    │
│  Country: [Dropdown ▼]              │
│  State/Province: [Dropdown ▼]       │
│  Address Line 1: [Input]            │
│  Address Line 2: [Input]            │
│  City: [Input]                      │
│  Postal Code: [Input]               │
│  Phone: [Input]                     │
│                                      │
│  ☐ Use shipping address for billing │
│                                      │
│  [Continue to Payment]               │
│                                      │
└──────────────────────────────────────┘
```

**Payment Method Variations:**

1. **Default Payment Method:**
   - **CSS Classes**: `billingForm`, `paymentMethodSelector`
   - Payment method selector (radio buttons or cards) at top
   - Standard editable address form
   - "Billing same as shipping" checkbox
   - Continue button

2. **Amazon Pay:**
   - **CSS Classes**: `staticAddressDisplay`
   - Static (read-only) address display (greyed out)
   - Custom fields section below (if Amazon Pay custom fields exist)
   - Only custom fields are editable
   - Continue button

3. **PayPal Fastlane:**
   - **CSS Classes**: `payPalAddressSelector`
   - PayPal saved addresses dropdown/selector
   - "Use new address" option
   - If new address selected: standard address form
   - Continue button

4. **Google Pay (with feature flag):**
   - Standard editable address form
   - No special PayPal-style selector
   - Continue button

5. **Wallet Payment Selected:**
   - **CSS Classes**: `staticAddressDisplay`
   - Static (read-only) address from wallet provider
   - Greyed out appearance
   - Continue button (no address update needed)

**Payment Method Selector:**
- **CSS Classes**: `paymentMethodOption`
- Radio buttons or card-style selection
- Each option shows:
  - Payment method icon/logo
  - Payment method name
  - Description (optional)
- Selected method highlighted
- Clicking changes form rendering below

**Visual States:**
- **Loading**: Disabled form while detecting payment method
- **Error**: Red border on invalid address fields
- **Payment method change**: Smooth form re-render based on selected method
- **Static address**: Greyed out, non-editable appearance

**Variations:**

1. **Default Payment Method:**
   - Payment method selector at top (if multiple methods)
   - Standard editable address form
   - "Billing same as shipping" checkbox (if shipping exists)
   - Continue button

2. **Amazon Pay:**
   - **Visual difference**: Static (read-only) address display
   - Address fields greyed out, non-editable
   - Custom fields section below (if Amazon Pay custom fields exist)
   - Only custom fields are editable
   - No payment method selector (Amazon Pay is the method)
   - Continue button

3. **PayPal Fastlane (Guest User):**
   - PayPal address selector dropdown/radio list
   - Shows saved PayPal addresses
   - "Use new address" option
   - If new address: standard editable form appears
   - PayPal branding/styling

4. **Google Pay (with Feature Flag):**
   - Standard editable address form
   - No special selector (unlike PayPal)
   - Feature flag: `STRIPE-546.allow_billing_address_editing_for_all_Google_Pay_providers`
   - Address-only completion logic

5. **PayPal Payment Methods (All Variants):**
   - Braintree PayPal, Braintree PayPal Credit, Braintree Venmo
   - PayPal Commerce, PayPal Commerce Credit, PayPal Commerce Venmo
   - Standard editable address form
   - Address-only completion (doesn't check wallet status)
   - PayPal branding

6. **Wallet Payment Selected (Apple Pay, Google Pay, etc.):**
   - Static (read-only) address from wallet provider
   - All address fields greyed out
   - No editable fields
   - Step appears complete automatically
   - No edit button (non-editable)

7. **Multiple Payment Methods Available:**
   - Payment method selector visible at top
   - Radio buttons or cards showing all methods
   - Selecting method changes form rendering below
   - Smooth transition between methods

8. **Single Payment Method:**
   - No payment method selector
   - Form renders directly based on that method
   - Cleaner, simpler layout

9. **Billing Same as Shipping (Checked):**
   - Address form pre-filled from shipping address
   - Fields still editable
   - Visual indicator showing address copied
   - Can uncheck to edit independently

10. **No Shipping Step (Digital Goods):**
    - No "Billing same as shipping" checkbox
    - Standard address form only
    - Payment method selector (if multiple methods)

**Layout Variations:**

1. **Payment Method Selector Placement:**

   **Top of Form (Default):**
   ```
   ┌──────────────────────────────────────┐
   │  Payment Method:                     │
   │  ○ Credit Card  ○ PayPal            │
   │  ──────────────────────────────────  │
   │  Billing Address:                   │
   │  [Address Form]                     │
   └──────────────────────────────────────┘
   ```

   **Sidebar (Two-Column):**
   ```
   ┌──────────────────┬──────────────────┐
   │  Payment Method:  │  Billing Address:│
   │  ○ Credit Card    │  [Address Form]  │
   │  ○ PayPal         │                   │
   └──────────────────┴──────────────────┘
   ```

   **Inline:**
   ```
   ┌──────────────────────────────────────┐
   │  Billing Address:                   │
   │  Payment Method: ○ Credit Card      │
   │  [Address Form]                     │
   └──────────────────────────────────────┘
   ```

2. **Payment Method Display:**

   **Radio Buttons (Vertical):**
   ```
   ┌──────────────────────────────────────┐
   │  Payment Method:                     │
   │  ○ Credit Card                       │
   │  ○ PayPal                            │
   │  ○ Apple Pay                         │
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
   │  ┌──────────┐                       │
   │  │○ Apple   │                       │
   │  │  Pay     │                       │
   │  └──────────┘                       │
   └──────────────────────────────────────┘
   ```

   **Horizontal Tabs:**
   ```
   ┌──────────────────────────────────────┐
   │  [Credit Card] [PayPal] [Apple Pay] │
   │  ──────────────────────────────────  │
   │  [Selected Method Form]              │
   └──────────────────────────────────────┘
   ```

   **Dropdown:**
   ```
   ┌──────────────────────────────────────┐
   │  Payment Method:                     │
   │  [Credit Card ▼]                     │
   │  [Address Form]                     │
   └──────────────────────────────────────┘
   ```

3. **Address Form Layout:**

   **Single Column (Default):**
   ```
   ┌──────────────────────────────────────┐
   │  Country: [Dropdown ▼]              │
   │  State/Province: [Dropdown ▼]       │
   │  Address Line 1: [Input]            │
   │  Address Line 2: [Input]            │
   │  City: [Input]                      │
   │  Postal Code: [Input]               │
   └──────────────────────────────────────┘
   ```

   **Two Columns:**
   ```
   ┌──────────────────────────────────────┐
   │  Country: [Dropdown ▼]              │
   │  State/Province: [Dropdown ▼]       │
   │  Address Line 1: [Input]            │
   │  Address Line 2: [Input]            │
   │  City: [Input]    Postal Code: [Input]│
   └──────────────────────────────────────┘
   ```

   **Three Columns:**
   ```
   ┌──────────────────────────────────────┐
   │  Country: [Dropdown ▼]              │
   │  State/Province: [Dropdown ▼]       │
   │  Address Line 1: [Input]            │
   │  Address Line 2: [Input]            │
   │  City: [Input]  State: [Input]  Zip: [Input]│
   └──────────────────────────────────────┘
   ```

4. **Static Address Display:**

   **Read-only Fields:**
   ```
   ┌──────────────────────────────────────┐
   │  Billing Address:                   │
   │  Country: [United States] (disabled)│
   │  State: [California] (disabled)      │
   │  Address: [123 Main St] (disabled) │
   │  City: [San Francisco] (disabled)   │
   └──────────────────────────────────────┘
   ```

   **Text Display:**
   ```
   ┌──────────────────────────────────────┐
   │  Billing Address:                   │
   │  123 Main St                        │
   │  San Francisco, CA 94102            │
   │  United States                       │
   └──────────────────────────────────────┘
   ```

   **Card Format:**
   ```
   ┌──────────────────────────────────────┐
   │  ┌────────────────────────────────┐ │
   │  │ Billing Address                │ │
   │  │ 123 Main St                    │ │
   │  │ San Francisco, CA 94102        │ │
   │  │ United States                  │ │
   │  └────────────────────────────────┘ │
   └──────────────────────────────────────┘
   ```

5. **PayPal Address Selector:**

   **Dropdown:**
   ```
   ┌──────────────────────────────────────┐
   │  Select PayPal Address:             │
   │  [123 Main St, SF ▼]                │
   │  [456 Oak Ave, LA]                  │
   │  [Use new address]                  │
   └──────────────────────────────────────┘
   ```

   **Radio List:**
   ```
   ┌──────────────────────────────────────┐
   │  Select PayPal Address:             │
   │  ○ 123 Main St, San Francisco       │
   │  ○ 456 Oak Ave, Los Angeles         │
   │  ○ Use new address                  │
   └──────────────────────────────────────┘
   ```

   **Card Grid:**
   ```
   ┌──────────────────────────────────────┐
   │  Select PayPal Address:             │
   │  ┌──────────┐ ┌──────────┐          │
   │  │○ 123 Main│ │○ 456 Oak  │          │
   │  │  St, SF  │ │  Ave, LA  │          │
   │  └──────────┘ └──────────┘          │
   │  ┌──────────┐                       │
   │  │○ Use New│                       │
   │  │  Address│                       │
   │  └──────────┘                       │
   └──────────────────────────────────────┘
   ```

6. **Custom Fields Placement (Amazon Pay):**

   **Below Address:**
   ```
   ┌──────────────────────────────────────┐
   │  Billing Address: [Static Display]   │
   │                                      │
   │  Custom Fields:                      │
   │  Tax ID: [Input]                    │
   │  Company: [Input]                   │
   └──────────────────────────────────────┘
   ```

   **Separate Section:**
   ```
   ┌──────────────────────────────────────┐
   │  Billing Address: [Static Display]   │
   │  ──────────────────────────────────  │
   │  Additional Information:            │
   │  Tax ID: [Input]                    │
   │  Company: [Input]                   │
   └──────────────────────────────────────┘
   ```

   **Side-by-Side:**
   ```
   ┌──────────────────────────────────────┐
   │  Billing Address: [Static Display]   │
   │  ┌──────────┬──────────┐            │
   │  │ Tax ID:  │ Company: │            │
   │  │ [Input]  │ [Input]  │            │
   │  └──────────┴──────────┘            │
   └──────────────────────────────────────┘
   ```

7. **"Billing Same as Shipping" Checkbox:**

   **Above Address Form:**
   ```
   ┌──────────────────────────────────────┐
   │  ☐ Use shipping address for billing │
   │  ──────────────────────────────────  │
   │  Billing Address:                   │
   │  [Address Form]                     │
   └──────────────────────────────────────┘
   ```

   **Below Address Form:**
   ```
   ┌──────────────────────────────────────┐
   │  Billing Address:                   │
   │  [Address Form]                     │
   │  ──────────────────────────────────  │
   │  ☐ Use shipping address for billing │
   └──────────────────────────────────────┘
   ```

   **Inline with Header:**
   ```
   ┌──────────────────────────────────────┐
   │  [Icon] Billing  ☐ Same as shipping  │
   │  ──────────────────────────────────  │
   │  [Address Form]                     │
   └──────────────────────────────────────┘
   ```

