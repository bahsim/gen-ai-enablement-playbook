---
**Title:** Shipping Step Visual Design
**Purpose:** Visual design and layout description of the Shipping step from a user's perspective.
**Audience:** All Developers, Designers, QA
**Related:** See `11-visual-design.md` for overall page layout and design system
---

# Shipping Step Visual Design

**Visual Layout:**
```
┌──────────────────────────────────────┐
│  [Icon] Shipping    [Summary]  [Edit]│
├──────────────────────────────────────┤
│                                      │
│  ☐ Use multiple addresses            │
│                                      │
│  Shipping Address:                  │
│  Country: [Dropdown ▼]              │
│  State/Province: [Dropdown ▼]       │
│  Address Line 1: [Input]            │
│  Address Line 2: [Input]            │
│  City: [Input]                      │
│  Postal Code: [Input]               │
│  Phone: [Input]                     │
│                                      │
│  Shipping Options:                   │
│  ○ Standard Shipping - $5.99        │
│  ○ Express Shipping - $12.99        │
│  ○ Overnight - $24.99               │
│                                      │
│  ☐ Use shipping address for billing │
│                                      │
│  Order Comments (optional):          │
│  ┌────────────────────────────────┐ │
│  │                                │ │
│  └────────────────────────────────┘ │
│                                      │
│  [Continue to Billing]              │
│                                      │
└──────────────────────────────────────┘
```

**Single Shipping Mode:**
- **CSS Classes**: `shippingForm`, `addressForm`
- Standard address form with country/state dropdowns
- Shipping method selection (radio buttons or cards)
- "Billing same as shipping" checkbox
- Order comments textarea (optional)
- Continue button at bottom

**Multi-Shipping Mode:**
- **CSS Classes**: `multiShippingForm`, `consignmentList`
- Toggle switch at top to enable multi-shipping
- Each consignment in separate card/panel:
  - Consignment header with address label
  - Address form for that consignment
  - Shipping method selection for that consignment
  - Item assignment section (checkboxes or drag-drop)
  - Delete consignment button
- Unassigned items section at bottom
- "Create new consignment" button
- Continue button at bottom

**Shipping Options Display:**
- **CSS Classes**: `shippingMethodList`, `shippingOption`
- Radio buttons or card-style selection
- Each option shows:
  - Option name (bold)
  - Option description (smaller text)
  - Price (right-aligned, bold)
- Selected option highlighted (blue border or background)
- Loading skeleton while fetching options

**Visual States:**
- **Loading**: Skeleton loaders for shipping options
- **Error**: Red border on invalid address fields, error message
- **Promotional items conflict**: Modal overlay with message and action buttons
- **Mode switch**: Smooth transition animation between single/multi mode

**Variations:**

1. **Single Shipping Mode (Default):**
   - One address form
   - One shipping method selection
   - "Billing same as shipping" checkbox
   - Standard continue button

2. **Multi-Shipping Mode:**
   - Toggle switch at top: "Use multiple addresses"
   - Multiple consignment cards/panels
   - Each consignment has own address + shipping method
   - Item assignment interface
   - Unassigned items section
   - "Create new consignment" button

3. **StripeUPE Special Case:**
   - **When**: StripeUPE provider configured AND customer has no email AND countries loaded
   - Renders Stripe shipping form instead of standard form
   - Stripe-branded form styling
   - Stripe-specific address collection

4. **Custom Shipping Selected:**
   - Same visual layout as single/multi mode
   - Step becomes non-editable (no edit button in header)
   - Visual indicator that shipping is locked
   - Warning message about manual processing

5. **Promotional Items Conflict:**
   - Modal overlay appears on mount if conflict detected
   - Modal message: "Multi-shipping unavailable with promotional items"
   - "Switch to single shipping" button in modal
   - Forces switch to single mode
   - Modal styling: centered, white background, shadow

6. **Digital Goods Only (Step Not Required):**
   - Step not displayed at all
   - Skipped in checkout flow

7. **No Shipping Options Available:**
   - Address form visible
   - "No shipping options available" message
   - Disabled continue button
   - Error styling

8. **Shipping Options Loading:**
   - Address form visible
   - Skeleton loaders where shipping options will appear
   - Disabled continue button
   - Loading spinner

**Layout Variations:**

1. **Address Form Layout:**

   **Single Column (Default):**
   ```
   ┌──────────────────────────────────────┐
   │  Country: [Dropdown ▼]              │
   │  State/Province: [Dropdown ▼]         │
   │  Address Line 1: [Input]            │
   │  Address Line 2: [Input]            │
   │  City: [Input]                      │
   │  Postal Code: [Input]               │
   │  Phone: [Input]                     │
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
   │  Phone: [Input]                     │
   └──────────────────────────────────────┘
   ```

   **Three Columns (Compact):**
   ```
   ┌──────────────────────────────────────┐
   │  Country: [Dropdown ▼]              │
   │  State/Province: [Dropdown ▼]       │
   │  Address Line 1: [Input]            │
   │  Address Line 2: [Input]            │
   │  City: [Input]  State: [Input]  Zip: [Input]│
   │  Phone: [Input]                     │
   └──────────────────────────────────────┘
   ```

2. **Shipping Options Display:**

   **Radio Buttons (Vertical):**
   ```
   ┌──────────────────────────────────────┐
   │  Shipping Options:                   │
   │  ○ Standard Shipping - $5.99        │
   │  ○ Express Shipping - $12.99        │
   │  ○ Overnight - $24.99               │
   └──────────────────────────────────────┘
   ```

   **Card Style (Vertical):**
   ```
   ┌──────────────────────────────────────┐
   │  Shipping Options:                   │
   │  ┌────────────────────────────────┐ │
   │  │ ○ Standard Shipping             │ │
   │  │   Ground delivery (5-7 days)     │ │
   │  │                        $5.99     │ │
   │  └────────────────────────────────┘ │
   │  ┌────────────────────────────────┐ │
   │  │ ○ Express Shipping             │ │
   │  │   Fast delivery (2-3 days)     │ │
   │  │                       $12.99   │ │
   │  └────────────────────────────────┘ │
   └──────────────────────────────────────┘
   ```

   **Card Style (Horizontal):**
   ```
   ┌──────────────────────────────────────┐
   │  Shipping Options:                   │
   │  ┌──────────┐ ┌──────────┐ ┌──────┐│
   │  │○ Standard│ │○ Express │ │○ Over││
   │  │  $5.99   │ │  $12.99  │ │$24.99││
   │  └──────────┘ └──────────┘ └──────┘│
   └──────────────────────────────────────┘
   ```

   **Grid Layout:**
   ```
   ┌──────────────────────────────────────┐
   │  Shipping Options:                   │
   │  ┌──────────┐ ┌──────────┐          │
   │  │○ Standard│ │○ Express │          │
   │  │  $5.99   │ │  $12.99  │          │
   │  └──────────┘ └──────────┘          │
   │  ┌──────────┐                       │
   │  │○ Overnight│                       │
   │  │  $24.99  │                       │
   │  └──────────┘                       │
   └──────────────────────────────────────┘
   ```

3. **Multi-Shipping Consignment Layout:**

   **Stacked Cards:**
   ```
   ┌──────────────────────────────────────┐
   │  ☐ Use multiple addresses            │
   │                                      │
   │  ┌────────────────────────────────┐ │
   │  │ Consignment 1                  │ │
   │  │ Address: [Form]                │ │
   │  │ Shipping: [Options]            │ │
   │  │ Items: ☑ Item A  ☑ Item B      │ │
   │  └────────────────────────────────┘ │
   │                                      │
   │  ┌────────────────────────────────┐ │
   │  │ Consignment 2                  │ │
   │  │ Address: [Form]                │ │
   │  │ Shipping: [Options]            │ │
   │  │ Items: ☑ Item C                │ │
   │  └────────────────────────────────┘ │
   └──────────────────────────────────────┘
   ```

   **Accordion:**
   ```
   ┌──────────────────────────────────────┐
   │  ☐ Use multiple addresses            │
   │                                      │
   │  ▼ Consignment 1 (2 items)          │
   │    Address: [Form]                  │
   │    Shipping: [Options]              │
   │                                      │
   │  ▶ Consignment 2 (1 item)           │
   │                                      │
   │  [Create New Consignment]            │
   └──────────────────────────────────────┘
   ```

4. **Item Assignment Interface:**

   **Checkbox List:**
   ```
   ┌──────────────────────────────────────┐
   │  Assign Items:                       │
   │  ☐ Item A - $10.00                  │
   │  ☐ Item B - $20.00                  │
   │  ☐ Item C - $15.00                  │
   │  [Assign to Consignment 1]          │
   └──────────────────────────────────────┘
   ```

   **Dropdown Selection:**
   ```
   ┌──────────────────────────────────────┐
   │  Item A - $10.00                     │
   │  Consignment: [Select ▼]             │
   │                                      │
   │  Item B - $20.00                     │
   │  Consignment: [Select ▼]             │
   └──────────────────────────────────────┘
   ```

5. **Toggle Switch Placement:**

   **Top of Form:**
   ```
   ┌──────────────────────────────────────┐
   │  ☐ Use multiple addresses            │
   │  ──────────────────────────────────  │
   │  Shipping Address:                  │
   │  [Address Form]                     │
   └──────────────────────────────────────┘
   ```

   **Above Address:**
   ```
   ┌──────────────────────────────────────┐
   │  Shipping Address:                  │
   │  ☐ Use multiple addresses            │
   │  ──────────────────────────────────  │
   │  [Address Form]                     │
   └──────────────────────────────────────┘
   ```

6. **Order Comments Placement:**

   **Below Shipping Options:**
   ```
   ┌──────────────────────────────────────┐
   │  Shipping Options: [Selection]      │
   │                                      │
   │  Order Comments (optional):          │
   │  ┌────────────────────────────────┐ │
   │  │                                │ │
   │  └────────────────────────────────┘ │
   │  [Continue]                         │
   └──────────────────────────────────────┘
   ```

   **Below Address:**
   ```
   ┌──────────────────────────────────────┐
   │  Address: [Form]                    │
   │                                      │
   │  Order Comments (optional):          │
   │  ┌────────────────────────────────┐ │
   │  │                                │ │
   │  └────────────────────────────────┘ │
   │  Shipping Options: [Selection]      │
   └──────────────────────────────────────┘
   ```

   **Separate Section:**
   ```
   ┌──────────────────────────────────────┐
   │  Address: [Form]                    │
   │  Shipping Options: [Selection]      │
   │  ──────────────────────────────────  │
   │  Additional Information:             │
   │  Order Comments:                     │
   │  ┌────────────────────────────────┐ │
   │  │                                │ │
   │  └────────────────────────────────┘ │
   └──────────────────────────────────────┘
   ```

