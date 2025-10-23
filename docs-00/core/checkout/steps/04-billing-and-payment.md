# Billing & Payment Step - Explained

## Core Responsibility & Design

The Billing & Payment step is responsible for collecting the **billing address and the customer's payment information**. This is one of the most complex steps as it integrates with numerous external payment providers.

This step renders the lazy-loaded `BillingAndPayment` component, which contains the logic for both billing forms and payment method selection. It's a critical step that directly precedes the final order submission.

## Implementation Details

-   **Component Rendered**: The `BillingAndPayment` component (`packages/core/src/app/billing-and-payment/BillingAndPayment.tsx`).
-   **Lazy Loading**: This component is **lazy-loaded** with a `webpackChunkName: "billing-payment"` to optimize performance.
-   **State Management**: It is a **controlled component**, managed by the `Checkout` orchestrator. It receives data like billing address and consignments as props. Its primary responsibility is to call event-handler props (`onSubmit`, `onSubmitError`, `onCartChangedError`) to communicate its state back to the parent.
-   **Contextual Coordination**: Internally, it uses a `BillingAndPaymentContext` to share state and coordinate actions between its child `Billing` and `Payment` components. This prevents the need to pass numerous props through the component tree.
-   **Key Feature**: Its primary complexity lies in its ability to dynamically render different payment forms based on the selected payment method. It integrates with dozens of payment providers.
-   **Embedded Logic**: It contains specific logic to handle embedded checkout scenarios, managed via props like `isEmbedded` and `checkEmbeddedSupport`.

## Role in the Checkout Flow

The Billing & Payment step is the **final interactive step** in the checkout flow. It occurs **after** the `OrderDetails` step. Once the customer successfully submits their payment information in this step, the checkout is considered complete, and they are redirected to the order confirmation page.

## Key Takeaway

This step consolidates billing and payment functionality into a single component that is lazy-loaded for performance. It uses a **React Context** to manage its internal complexity while remaining a **controlled component** from the perspective of the `Checkout` orchestrator. The orchestrator provides its data and handles its key events (`onSubmit`, `onSubmitError`) to finalize the checkout.
