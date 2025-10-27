# ThankYouHeader Component

Simple header block for the order confirmation page.

## Props
```ts
interface HeaderProps {
    name?: string;
}
```

## Behavior
- Renders h1 "Order confirmation" and a subtitle.
- If `name` is provided, subtitle includes the customer's name.

## Source
- packages/core/src/app/order/ThankYouHeader.tsx
