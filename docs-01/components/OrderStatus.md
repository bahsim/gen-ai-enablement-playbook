# OrderStatus Component

Displays the order number and follow-up messaging after a successful purchase, within OrderConfirmationSection.

## Props
```ts
interface OrderStatusProps {
    config: StoreConfig;
    userEmail: string;
    supportPhoneNumber?: string;
    order: Order;
}
```

## Behavior
- Renders order_confirmation.order_number_text via TranslatedHtml with orderNumber substitution when order.orderId is present.
- Shows a copy notice to userEmail and a support section with optional supportPhoneNumber.

## Source
- packages/core/src/app/order/OrderStatus.tsx
