# ErrorModal Component - Implementation Analysis

## Core Architecture

The `ErrorModal` component is a sophisticated error display system that handles different types of errors with appropriate rendering, accessibility features, and user interaction patterns. It's a PureComponent that provides consistent error display across the checkout flow.

### Props Interface

The component accepts props for error display configuration:

```typescript
export interface ErrorModalProps {
    error?: Error | RequestError | CustomError;  // Error object to display
    message?: ReactNode;                         // Custom error message
    title?: ReactNode;                          // Custom error title
    shouldShowErrorCode?: boolean;              // Whether to show error code
    onClose?(event: Event, props: ErrorModalOnCloseProps): void;  // Close handler
}

export interface ErrorModalOnCloseProps {
    error: Error;  // Error object passed to close handler
}
```

### Error Type Handling

The component handles multiple error types with specific rendering logic:

```typescript
// Custom error with title
const { error, title = error && isCustomError(error) && error.title } = this.props;

// HTML error with translation key
{error && isHtmlError(error) &&
    <TranslatedHtml id={error.data.translationKey} />
}

// Request error with headers
if (isRequestError(error) && error.headers?.['x-request-id']) {
    return (
        <ErrorCode
            code={error.headers['x-request-id']}
            label={<TranslatedString id="common.request_id" />}
        />
    );
}
```

**Error Type Strategy:**
- **CustomError**: Uses custom title and message
- **HtmlError**: Renders translated HTML content
- **RequestError**: Shows request ID for debugging
- **Standard Error**: Uses default error handling

### Modal Structure

The component renders a modal with header, body, and footer:

```typescript
render(): ReactNode {
    const { error } = this.props;

    return (
        <Modal
            additionalModalClassName="modal--error"
            aria={this.aria}
            footer={this.renderFooter()}
            header={this.renderHeader()}
            isOpen={!!error}
            onRequestClose={this.handleOnRequestClose}
        >
            {this.renderBody()}
        </Modal>
    );
}
```

**Modal Strategy:**
- Conditional rendering based on error presence
- Custom CSS class for error styling
- ARIA attributes for accessibility
- Proper modal structure with header, body, footer

### Header Rendering

The component renders an error header with icon and title:

```typescript
private renderHeader(): ReactNode {
    const { error, title = error && isCustomError(error) && error.title } = this.props;

    return (
        <ModalHeader>
            <IconError
                additionalClassName="icon--error modal-header-icon"
                size={IconSize.Small}
            />
            <span aria-live="assertive" role="alert">
                {title || <TranslatedString id="common.error_heading" />}
            </span>
        </ModalHeader>
    );
}
```

**Header Strategy:**
- Error icon for visual indication
- Custom title from error or default
- ARIA live region for screen readers
- Alert role for accessibility

### Body Rendering

The component renders error content with multiple message types:

```typescript
private renderBody(): ReactNode {
    const { error, message = error && error.message } = this.props;

    return (
        <>
            {error && isHtmlError(error) &&
                <TranslatedHtml id={error.data.translationKey} />
            }
            {message && (
                <p aria-live="assertive" id="errorModalMessage" role="alert">
                    {message}
                </p>
            )}

            <div className="optimizedCheckout-contentSecondary">{this.renderErrorCode()}</div>
        </>
    );
}
```

**Body Strategy:**
- HTML error with translated content
- Standard error message
- Error code display
- ARIA live regions for accessibility
- Proper content hierarchy

### Error Code Rendering

The component renders error codes for debugging:

```typescript
private renderErrorCode(): ReactNode {
    const { error, shouldShowErrorCode = true } = this.props;

    if (!error || !shouldShowErrorCode) {
        return;
    }

    if (isRequestError(error) && error.headers?.['x-request-id']) {
        return (
            <ErrorCode
                code={error.headers['x-request-id']}
                label={<TranslatedString id="common.request_id" />}
            />
        );
    }

    const errorCode = computeErrorCode(error);

    if (!errorCode) {
        return;
    }

    return <ErrorCode code={errorCode} />;
}
```

**Error Code Strategy:**
- Request ID for API errors
- Computed error code for other errors
- Conditional rendering based on configuration
- Proper labeling for different code types

### Footer Rendering

The component renders a simple OK button:

```typescript
private renderFooter(): ReactNode {
    return (
        <Button onClick={this.handleOnRequestClose} size={ButtonSize.Small}>
            <TranslatedString id="common.ok_action" />
        </Button>
    );
}
```

**Footer Strategy:**
- Simple OK button for dismissal
- Small button size for modal context
- Localized button text
- Click handler for close action

### Close Handling

The component handles modal close events:

```typescript
private handleOnRequestClose: (event: SyntheticEvent) => void = (event) => {
    const { error, onClose = noop } = this.props;

    if (error) {
        onClose(event.nativeEvent, { error });
    }
};
```

**Close Strategy:**
- Extracts native event from synthetic event
- Passes error object to close handler
- Provides fallback for missing handler
- Maintains error context

### Accessibility Features

The component implements comprehensive accessibility features:

```typescript
private aria = {
    labelledby: 'errorModalMessage',
};

// In header
<span aria-live="assertive" role="alert">
    {title || <TranslatedString id="common.error_heading" />}
</span>

// In body
<p aria-live="assertive" id="errorModalMessage" role="alert">
    {message}
</p>
```

**Accessibility Strategy:**
- ARIA labelledby for modal association
- Live regions for dynamic content
- Alert role for error messages
- Proper focus management
- Screen reader support

### Error Type Detection

The component uses utility functions for error type detection:

```typescript
// Custom error detection
isCustomError(error) && error.title

// HTML error detection
isHtmlError(error)

// Request error detection
isRequestError(error) && error.headers?.['x-request-id']
```

**Error Detection Strategy:**
- Type guards for different error types
- Specific handling for each error type
- Fallback to default error handling
- Consistent error processing

### Performance Optimizations

1. **PureComponent**: Prevents unnecessary re-renders
2. **Conditional Rendering**: Only renders when error exists
3. **Efficient Error Processing**: Minimal error type checking
4. **Memoized Callbacks**: Stable event handlers

### Styling System

The component uses a comprehensive styling system:

```typescript
additionalModalClassName="modal--error"
className="icon--error modal-header-icon"
className="optimizedCheckout-contentSecondary"
```

**Styling Strategy:**
- Error-specific modal styling
- Icon styling for visual indication
- Content hierarchy styling
- Consistent class naming

### Integration Points

The component integrates with:
- **Modal System**: Base modal functionality
- **Icon System**: Error icon display
- **Translation System**: Localized content
- **Error Utilities**: Error type detection
- **Button System**: Action buttons

## Source Files

- **Main Implementation**: `packages/core/src/app/common/error/ErrorModal.tsx`
- **Error Code**: `packages/core/src/app/common/error/ErrorCode.tsx`
- **Error Utilities**: `packages/core/src/app/common/error/`
- **Modal System**: `packages/core/src/app/ui/modal/Modal.tsx`
- **Icon System**: `packages/core/src/app/ui/icon/IconError.tsx`
