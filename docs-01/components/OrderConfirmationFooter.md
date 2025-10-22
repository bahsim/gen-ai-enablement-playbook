# OrderConfirmationFooter Component

## Overview
The `OrderConfirmationFooter` component provides the footer section of the order confirmation page, containing legal links, company information, and copyright details. It serves as the final element in the order confirmation flow.

## Component Purpose
- **Primary Role**: Display footer information and legal links
- **Legal Compliance**: Provide access to terms, privacy policy, and legal documents
- **Company Information**: Display company details and copyright information
- **Navigation Support**: Offer links to important pages and resources
- **Brand Consistency**: Maintain consistent footer styling across checkout

## Props Interface

### OrderConfirmationFooterProps
```typescript
interface OrderConfirmationFooterProps {
    className?: string;                                    // Optional CSS classes for styling
}
```

## Component Structure

### Footer Content Layout
```typescript
const OrderConfirmationFooter: FunctionComponent<OrderConfirmationFooterProps> = ({ className }) => (
    <footer className={`orderConfirmation-footer ${className || ''}`}>
        <div className="orderConfirmation-footer-content">
            <div className="orderConfirmation-footer-links">
                {/* Legal and navigation links */}
            </div>
            <div className="orderConfirmation-footer-copyright">
                {/* Copyright information */}
            </div>
        </div>
    </footer>
);
```

## Key Features

### 1. Legal Links Section
- **Terms of Use**: Link to terms and conditions
- **Privacy Policy**: Link to privacy policy document
- **About Us**: Link to company information
- **Accreditation**: Link to company credentials
- **FAQ**: Link to frequently asked questions
- **Contact Us**: Link to customer support

### 2. Company Information
- **Copyright Notice**: Displays copyright year and company name
- **Company Branding**: Maintains brand consistency
- **Legal Compliance**: Ensures required legal information is displayed

### 3. Styling and Layout
- **Responsive Design**: Adapts to different screen sizes
- **CSS Classes**: Uses BigCommerce design system classes
- **Custom Styling**: Supports additional CSS classes via props
- **Visual Hierarchy**: Clear separation between links and copyright

## CSS Classes and Styling

### Primary Classes
- **orderConfirmation-footer**: Main footer container
- **orderConfirmation-footer-content**: Footer content wrapper
- **orderConfirmation-footer-links**: Links section container
- **orderConfirmation-footer-link**: Individual link styling
- **orderConfirmation-footer-copyright**: Copyright section

### Styling Features
- **Flexbox Layout**: Responsive layout system
- **Link Styling**: Consistent link appearance and hover effects
- **Typography**: Proper font sizing and spacing
- **Spacing**: Consistent margins and padding

## Link Management

### Current Links
```typescript
<div className="orderConfirmation-footer-links">
    <a href="#" className="orderConfirmation-footer-link">Terms of Use</a>
    <a href="#" className="orderConfirmation-footer-link">Privacy Policy</a>
    <a href="#" className="orderConfirmation-footer-link">About Us</a>
    <a href="#" className="orderConfirmation-footer-link">Accreditation</a>
    <a href="#" className="orderConfirmation-footer-link">FAQ</a>
    <a href="#" className="orderConfirmation-footer-link">Contact Us</a>
</div>
```

### Link Implementation Notes
- **Placeholder URLs**: Currently uses "#" as placeholder
- **Link Structure**: Consistent link styling and behavior
- **Accessibility**: Proper link semantics and navigation

## Copyright Information

### Copyright Display
```typescript
<div className="orderConfirmation-footer-copyright">
    © 1996-2025 Pearson All rights reserved.
</div>
```

### Copyright Features
- **Dynamic Year**: Updates automatically (currently hardcoded)
- **Company Name**: Displays company branding
- **Legal Text**: Standard copyright notice
- **Rights Statement**: Clear rights declaration

## Responsive Design

### Mobile Optimization
- **Stacked Layout**: Links and copyright stack vertically on small screens
- **Touch Friendly**: Appropriate touch target sizes
- **Readable Text**: Maintains readability across devices

### Desktop Layout
- **Horizontal Arrangement**: Links arranged horizontally
- **Proper Spacing**: Adequate spacing between elements
- **Visual Balance**: Balanced layout and proportions

## Accessibility Features

### Semantic HTML
- **Footer Element**: Proper `<footer>` semantic element
- **Link Elements**: Standard `<a>` tags for navigation
- **Content Structure**: Logical content hierarchy

### Screen Reader Support
- **Link Descriptions**: Clear, descriptive link text
- **Content Order**: Logical reading order
- **Navigation Support**: Proper navigation structure

## Testing Strategy

### Unit Tests
- **Component Rendering**: Tests component rendering with various props
- **Link Display**: Tests all legal links are present
- **Copyright Display**: Tests copyright information
- **Styling**: Tests CSS class application

### Integration Tests
- **Checkout Flow**: Tests footer in complete order confirmation
- **Responsive Design**: Tests mobile and desktop layouts
- **Link Functionality**: Tests link behavior and navigation

### E2E Tests
- **User Navigation**: Tests footer link navigation
- **Mobile Experience**: Tests mobile footer interaction
- **Accessibility**: Tests screen reader compatibility

## Dependencies

### Internal Dependencies
- React framework and types
- BigCommerce design system classes

### External Dependencies
- None - pure React component

## Usage Example

```typescript
import { OrderConfirmationFooter } from '@bigcommerce/checkout/order';

// Basic usage
<OrderConfirmationFooter />

// With custom styling
<OrderConfirmationFooter className="custom-footer-styles" />
```

## Customization Options

### CSS Class Extension
```typescript
// Add custom styles via className prop
<OrderConfirmationFooter className="my-custom-footer" />
```

### Styling Overrides
```scss
// Custom footer styling
.my-custom-footer {
    .orderConfirmation-footer {
        background-color: #f5f5f5;
        border-top: 1px solid #ddd;
    }
    
    .orderConfirmation-footer-link {
        color: #333;
        text-decoration: none;
        
        &:hover {
            color: #007cba;
            text-decoration: underline;
        }
    }
}
```

## Best Practices

### 1. Legal Compliance
- Ensure all required legal links are present
- Keep copyright information current
- Validate link destinations
- Maintain legal document accessibility

### 2. User Experience
- Provide clear, descriptive link text
- Maintain consistent styling with brand
- Ensure responsive design across devices
- Optimize for accessibility

### 3. Performance
- Keep component lightweight
- Minimize re-renders
- Use efficient CSS classes
- Optimize for mobile performance

### 4. Maintenance
- Regular link validation
- Copyright year updates
- Link destination verification
- Accessibility compliance checks

## Common Issues and Solutions

### 1. Broken Links
- **Issue**: Legal links point to invalid destinations
- **Solution**: Implement proper URL routing
- **Prevention**: Regular link validation and testing

### 2. Copyright Year Updates
- **Issue**: Copyright year becomes outdated
- **Solution**: Implement dynamic year generation
- **Prevention**: Automated year updates

### 3. Mobile Layout Issues
- **Issue**: Footer doesn't display properly on mobile
- **Solution**: Implement responsive CSS
- **Prevention**: Mobile-first design approach

### 4. Accessibility Problems
- **Issue**: Screen readers can't navigate footer
- **Solution**: Implement proper ARIA labels
- **Prevention**: Accessibility testing and validation

## Future Enhancements

### 1. Dynamic Link Management
- **Configurable Links**: Make links configurable via props
- **Dynamic Routing**: Implement proper routing for links
- **Link Validation**: Add link validation and error handling

### 2. Enhanced Styling
- **Theme Support**: Support for different themes
- **Custom Styling**: More flexible styling options
- **Animation**: Add subtle animations and transitions

### 3. Internationalization
- **Multi-language Support**: Support for different languages
- **Locale-specific Links**: Region-specific legal documents
- **RTL Support**: Right-to-left language support

This component provides essential footer functionality for the order confirmation page, ensuring legal compliance and maintaining brand consistency throughout the checkout experience.
