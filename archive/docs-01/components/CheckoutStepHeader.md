# CheckoutStepHeader Component

## Overview
The `CheckoutStepHeader` component displays the header for each checkout step, including the step title, completion status, summary information, and edit controls. It provides visual feedback about step progress and allows users to navigate between steps.

## Component Purpose
- **Primary Role**: Display step header with title and status information
- **Visual Feedback**: Show step completion status and current state
- **Navigation Control**: Enable step editing and navigation
- **Status Display**: Present step summary and completion indicators
- **Accessibility**: Provide proper ARIA attributes and keyboard navigation

## Props Interface

### CheckoutStepHeaderProps
```typescript
interface CheckoutStepHeaderProps {
    heading: ReactNode;                                    // Step title/heading content
    isActive?: boolean;                                    // Whether step is currently active
    isComplete?: boolean;                                  // Whether step has been completed
    isEditable?: boolean;                                  // Whether step can be edited
    summary?: ReactNode;                                   // Summary content for completed steps
    type: CheckoutStepType;                                // Type of checkout step
    onEdit?(type: CheckoutStepType): void;                 // Edit callback function
}
```

### CheckoutStepType
```typescript
enum CheckoutStepType {
    CUSTOMER = 'customer',                                 // Customer information step
    SHIPPING = 'shipping',                                 // Shipping address and method step
    BILLING = 'billing',                                   // Billing address step
    PAYMENT = 'payment',                                    // Payment method step
}
```

## Key Features

### 1. Step Status Display
- **Active State**: Highlights currently active step
- **Complete State**: Shows completion status with check icon
- **Inactive State**: Displays inactive step styling
- **Progress Indicator**: Visual progress through checkout flow

### 2. Interactive Controls
- **Edit Button**: Allows editing of completed steps
- **Click Navigation**: Enables step-to-step navigation
- **Keyboard Support**: Full keyboard accessibility
- **Touch Friendly**: Optimized for mobile interactions

### 3. Content Management
- **Dynamic Heading**: Flexible heading content support
- **Summary Display**: Shows step summary when completed
- **Conditional Rendering**: Content based on step state
- **Responsive Layout**: Adapts to different screen sizes

### 4. Visual Design
- **BigCommerce Design System**: Consistent with brand styling
- **Icon Integration**: Check icon for completion status
- **Typography**: Proper heading hierarchy and font styles
- **Layout System**: Flexible column-based layout

## Component Structure

### Header Layout
```typescript
<div className={classNames('stepHeader', {
    'is-readonly': !isEditable,
    'is-clickable': isEditable && !isActive,
    'stepHeader--active': isActive,
    'stepHeader--inactive': !isActive,
})}>
    <div className="stepHeader-figure stepHeader-column">
        {/* Icon and title */}
    </div>
    <div className="stepHeader-body stepHeader-column">
        {/* Summary content */}
    </div>
    <div className="stepHeader-actions stepHeader-column">
        {/* Edit button */}
    </div>
</div>
```

### Column Layout
- **stepHeader-figure**: Contains icon and title
- **stepHeader-body**: Contains summary information
- **stepHeader-actions**: Contains action buttons

## CSS Classes and Styling

### Primary Classes
- **stepHeader**: Main header container
- **stepHeader-figure**: Icon and title column
- **stepHeader-body**: Summary content column
- **stepHeader-actions**: Action buttons column
- **stepHeader-column**: Column layout utility

### State-Based Classes
```typescript
className={classNames('stepHeader', {
    'is-readonly': !isEditable,                           // Read-only state
    'is-clickable': isEditable && !isActive,              // Clickable state
    'stepHeader--active': isActive,                       // Active state
    'stepHeader--inactive': !isActive,                    // Inactive state
})}
```

### Typography Classes
```typescript
className={classNames('stepHeader-title optimizedCheckout-headingPrimary', {
    'header': newFontStyle && (isActive || isComplete),           // Primary header style
    'header-secondary': newFontStyle && !isActive && !isComplete  // Secondary header style
})}
```

## User Interaction Handling

### Click Navigation
```typescript
onClick={preventDefault(isEditable && onEdit ? () => onEdit(type) : noop)}
```

### Edit Button
```typescript
{isEditable && !isActive && (
    <div className="stepHeader-actions stepHeader-column">
        <Button
            aria-expanded={isActive}
            size={ButtonSize.Tiny}
            testId="step-edit-button"
            variant={ButtonVariant.Secondary}
        >
            <TranslatedString id="common.edit_action" />
        </Button>
    </div>
)}
```

### Accessibility Features
- **aria-expanded**: Indicates step expansion state
- **testId**: Provides testing identifiers
- **Keyboard Navigation**: Full keyboard support
- **Screen Reader**: Proper semantic structure

## Content Rendering Logic

### Conditional Content Display
```typescript
// Show summary only for completed, inactive steps
{!isActive && isComplete && summary}

// Show edit button only for editable, inactive steps
{isEditable && !isActive && (
    <Button>Edit</Button>
)}
```

### Icon Display
```typescript
<IconCheck
    additionalClassName={classNames(
        'optimizedCheckout-step',
    )}
/>
```

## Style Context Integration

### Font Style Context
```typescript
const { newFontStyle } = useStyleContext();

// Apply conditional font styling
className={classNames('stepHeader-title optimizedCheckout-headingPrimary', {
    'header': newFontStyle && (isActive || isComplete),
    'header-secondary': newFontStyle && !isActive && !isComplete
})}
```

### Responsive Design
- **Mobile Optimization**: Touch-friendly interactions
- **Flexible Layout**: Adapts to content changes
- **Column System**: Responsive column layout
- **Typography Scaling**: Responsive font sizes

## Performance Optimization

### Memoization
```typescript
export default memo(CheckoutStepHeader);
```

### Conditional Rendering
- **Lazy Loading**: Only render necessary content
- **State-Based Rendering**: Content based on step state
- **Efficient Updates**: Minimal re-renders

## Testing Strategy

### Unit Tests
- **Component Rendering**: Tests component rendering with various props
- **State Display**: Tests different step states
- **Interaction Handling**: Tests click and edit functionality
- **Content Rendering**: Tests conditional content display

### Integration Tests
- **Step Navigation**: Tests step-to-step navigation
- **Edit Functionality**: Tests step editing capabilities
- **State Transitions**: Tests step state changes
- **Accessibility**: Tests keyboard and screen reader support

### E2E Tests
- **User Navigation**: Tests complete step navigation
- **Edit Interactions**: Tests step editing workflow
- **Mobile Experience**: Tests mobile interactions
- **Accessibility**: Tests accessibility compliance

## Dependencies

### Internal Dependencies
- `@bigcommerce/checkout/dom-utils`: Event prevention utilities
- `@bigcommerce/checkout/locale`: Internationalization
- `@bigcommerce/checkout/payment-integration-api`: Style context
- `@bigcommerce/checkout/ui/button`: Button component
- `@bigcommerce/checkout/ui/icon`: Icon components

### External Dependencies
- `react`: React framework
- `classnames`: CSS class management
- `lodash`: Utility functions

## Usage Example

```typescript
import { CheckoutStepHeader } from '@bigcommerce/checkout/checkout';

const checkoutStepHeaderProps = {
    heading: "Shipping Information",
    isActive: false,
    isComplete: true,
    isEditable: true,
    summary: "123 Main St, New York, NY 10001",
    type: CheckoutStepType.SHIPPING,
    onEdit: (step) => console.log(`Editing step: ${step}`),
};

<CheckoutStepHeader {...checkoutStepHeaderProps} />
```

## Best Practices

### 1. State Management
- Use clear, boolean state flags
- Implement proper state transitions
- Handle edge cases gracefully
- Maintain consistent state patterns

### 2. Accessibility
- Provide proper ARIA attributes
- Implement keyboard navigation
- Ensure screen reader compatibility
- Follow WCAG guidelines

### 3. Performance
- Use memoization for optimization
- Implement conditional rendering
- Minimize unnecessary re-renders
- Monitor component performance

### 4. User Experience
- Provide clear visual feedback
- Implement intuitive interactions
- Handle loading states gracefully
- Ensure responsive design

## Common Issues and Solutions

### 1. State Synchronization
- **Issue**: Step state not synchronized with parent
- **Solution**: Use proper callback patterns
- **Prevention**: Implement proper state management

### 2. Click Handling Issues
- **Issue**: Click events not working properly
- **Solution**: Use preventDefault utility
- **Prevention**: Test event handling thoroughly

### 3. Styling Inconsistencies
- **Issue**: Styles not applying correctly
- **Solution**: Check CSS class application
- **Prevention**: Use consistent class naming

### 4. Accessibility Problems
- **Issue**: Screen readers can't navigate steps
- **Solution**: Implement proper ARIA attributes
- **Prevention**: Regular accessibility testing

## Future Enhancements

### 1. Enhanced Interactions
- **Hover Effects**: Enhanced hover interactions
- **Animations**: Smooth transition animations
- **Gesture Support**: Touch gesture support
- **Visual Feedback**: Enhanced visual indicators

### 2. Advanced Styling
- **Theme Support**: Multiple theme support
- **Custom Styling**: More flexible styling options
- **Responsive Enhancements**: Advanced responsive features
- **Animation System**: Comprehensive animation support

### 3. Accessibility Improvements
- **Voice Commands**: Voice navigation support
- **Advanced ARIA**: Enhanced ARIA implementation
- **Screen Reader**: Improved screen reader support
- **Keyboard Shortcuts**: Advanced keyboard navigation

This component provides essential header functionality for checkout steps, ensuring clear navigation, proper status indication, and excellent user experience throughout the checkout process.
