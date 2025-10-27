# CheckoutStep Component - Implementation Analysis

## Core Architecture

The `CheckoutStep` component manages individual checkout steps with sophisticated animation handling, focus management, and responsive design. It's a class component that orchestrates step rendering, transitions, and user interactions.

### Props Interface

```typescript
export interface CheckoutStepProps {
    children?: ReactNode;                    // Step content
    heading?: ReactNode;                     // Step heading
    isActive?: boolean;                      // Active state
    isBusy: boolean;                         // Busy/loading state
    isComplete?: boolean;                    // Completion state
    isEditable?: boolean;                    // Editability state
    suggestion?: ReactNode;                  // Step suggestion content
    summary?: ReactNode;                     // Step summary content
    type: CheckoutStepType;                  // Step type
    onExpanded?(step: CheckoutStepType): void; // Expansion callback
    onEdit?(step: CheckoutStepType): void;   // Edit callback
}

export interface CheckoutStepState {
    isClosed: boolean;                       // Closed state for animations
}
```

### State Management

The component manages internal state for animation and interaction:

```typescript
export default class CheckoutStep extends Component<CheckoutStepProps, CheckoutStepState> {
    state = {
        isClosed: true,
    };

    private containerRef = createRef<HTMLLIElement>();
    private contentRef = createRef<HTMLDivElement>();
    private timeoutRef?: number;
    private timeoutDelay?: number;
```

**State Strategy:**
- **isClosed**: Controls animation states and content visibility
- **Refs**: DOM references for focus management and positioning
- **Timeouts**: Animation timing and focus delays

### Lifecycle Management

The component implements sophisticated lifecycle management:

```typescript
componentDidMount(): void {
    const { isActive } = this.props;

    if (isActive) {
        this.focusStep();
    }
}

componentDidUpdate(prevProps: Readonly<CheckoutStepProps>): void {
    const { isActive } = this.props;

    if (isActive && isActive !== prevProps.isActive) {
        this.focusStep();
    }
}

componentWillUnmount(): void {
    if (this.timeoutRef) {
        window.clearTimeout(this.timeoutRef);
        this.timeoutRef = undefined;
    }
}
```

**Lifecycle Strategy:**
- **Mount**: Focus step if initially active
- **Update**: Focus step when becoming active
- **Unmount**: Clean up timeouts to prevent memory leaks

### Rendering Logic

The component renders step structure with conditional content:

```typescript
render(): ReactNode {
    const { heading, isActive, isComplete, isEditable, onEdit, suggestion, summary, type } =
        this.props;

    const { isClosed } = this.state;

    return (
        <li
            className={classNames('checkout-step', 'optimizedCheckout-checkoutStep', {
                [`checkout-step--${type}`]: !!type,
                ['checkout-step--active']: isActive,
            })}
            ref={this.containerRef}
        >
            <div className="checkout-view-header">
                <CheckoutStepHeader
                    heading={heading}
                    isActive={isActive}
                    isComplete={isComplete}
                    isEditable={isEditable}
                    onEdit={onEdit}
                    summary={summary}
                    type={type}
                />
            </div>

            {suggestion && isClosed && !isActive && (
                <div className="checkout-suggestion" data-test="step-suggestion">
                    {suggestion}
                </div>
            )}

            {this.renderContent()}
        </li>
    );
}
```

**Rendering Strategy:**
- **Conditional Classes**: Dynamic CSS classes based on state
- **Header Rendering**: CheckoutStepHeader with all props
- **Suggestion Display**: Shows suggestions when closed and inactive
- **Content Rendering**: Separate method for content management

### Content Rendering with Animation

The component implements sophisticated content rendering with CSS transitions:

```typescript
private renderContent(): ReactNode {
    const { children, isActive, isBusy } = this.props;

    return (
        <MobileView>
            {(matched) => (
                <CSSTransition
                    addEndListener={this.handleTransitionEnd}
                    classNames="checkout-view-content"
                    enter={!matched}
                    exit={!matched}
                    in={isActive}
                    mountOnEnter
                    onExited={ this.onAnimationEnd }
                    timeout={ {} }
                    unmountOnExit
                >
                    <div
                        aria-busy={isBusy}
                        className="checkout-view-content"
                        ref={this.contentRef}
                    >
                        {isActive ? children : null}
                    </div>
                </CSSTransition>
            )}
        </MobileView>
    );
}
```

**Content Rendering Strategy:**
- **Mobile Detection**: Different behavior for mobile vs desktop
- **CSS Transitions**: Smooth enter/exit animations
- **Conditional Rendering**: Only renders children when active
- **Accessibility**: ARIA busy state for loading indication

### Focus Management

The component implements sophisticated focus management:

```typescript
private focusStep(): void {
    const delay = isMobileView() ? 0 : this.getTransitionDelay();

    this.setState({ isClosed: false });

    this.timeoutRef = window.setTimeout(() => {
        const input = this.getChildInput();
        const position = this.getScrollPosition();
        const { type, onExpanded = noop } = this.props;

        if (input) {
            input.focus();
        }

        if (position !== undefined && !isNaN(position)) {
            window.scrollTo(0, position);
        }

        onExpanded(type);

        this.timeoutRef = undefined;
    }, delay);
}
```

**Focus Strategy:**
- **Mobile Optimization**: No delay on mobile devices
- **Input Focus**: Focuses first input element
- **Scroll Positioning**: Scrolls to optimal position
- **Expansion Callback**: Notifies parent of step expansion

### Input Element Detection

The component finds the first input element for focus:

```typescript
private getChildInput(): HTMLElement | undefined {
    const container = this.containerRef.current;

    if (!container) {
        return;
    }

    const input = container.querySelector<HTMLElement>('input, select, textarea');

    return input || undefined;
}
```

**Input Detection Strategy:**
- **Query Selector**: Finds first input, select, or textarea
- **Type Safety**: Proper TypeScript typing
- **Null Handling**: Graceful handling of missing elements

### Scroll Position Calculation

The component calculates optimal scroll position:

```typescript
private getScrollPosition(): number | undefined {
    const container = this.getParentContainer();
    const { isComplete } = this.props;

    if (!container || window !== window.top) {
        return;
    }

    const topOffset = isComplete ? 0 : window.innerHeight / 5;
    const containerOffset =
        container.getBoundingClientRect().top + (window.scrollY || window.pageYOffset);

    return containerOffset - topOffset;
}
```

**Scroll Strategy:**
- **Parent Container**: Finds appropriate container for positioning
- **Completion State**: Different offset for complete vs incomplete steps
- **Window Context**: Only works in top-level window
- **Offset Calculation**: Positions step optimally in viewport

### Parent Container Detection

The component finds the parent container for positioning:

```typescript
private getParentContainer(): HTMLElement | undefined {
    let container: HTMLElement | null = this.containerRef.current;

    while (container && container.parentElement) {
        if (container.parentElement.classList.contains('checkout-step')) {
            return container.parentElement;
        }

        container = container.parentElement;
    }

    return this.containerRef.current ? this.containerRef.current : undefined;
}
```

**Container Strategy:**
- **DOM Traversal**: Walks up the DOM tree
- **Class Detection**: Looks for checkout-step class
- **Fallback**: Returns current container if no parent found

### Transition Delay Calculation

The component calculates CSS transition delays:

```typescript
private getTransitionDelay(): number {
    if (this.timeoutDelay !== undefined) {
        return this.timeoutDelay;
    }

    // Cache the result to avoid unnecessary reflow
    this.timeoutDelay =
        parseFloat(
            this.contentRef.current
                ? getComputedStyle(this.contentRef.current).transitionDuration
                : '0s',
        ) * 1000;

    return this.timeoutDelay;
}
```

**Delay Strategy:**
- **Caching**: Caches calculated delay to avoid reflow
- **CSS Parsing**: Reads transition duration from computed styles
- **Millisecond Conversion**: Converts seconds to milliseconds
- **Fallback**: Uses 0s if no element available

### Transition End Handling

The component handles CSS transition end events:

```typescript
private handleTransitionEnd: (node: HTMLElement, done: () => void) => void = (node, done) => {
    node.addEventListener('transitionend', ({ target }) => {
        if (target === node) {
            done();
        }
    });
};
```

**Transition Strategy:**
- **Event Listener**: Listens for transitionend events
- **Target Validation**: Ensures event is from correct element
- **Callback Execution**: Calls done callback when transition completes

### Animation End Handling

The component handles animation completion:

```typescript
private onAnimationEnd = (): void => {
    const { isActive } = this.props;

    if (!isActive) {
        this.setState({ isClosed: true });
    }
}
```

**Animation Strategy:**
- **State Update**: Updates closed state when animation ends
- **Active Check**: Only closes if step is not active
- **State Consistency**: Maintains proper state after animations

### Performance Optimizations

1. **Timeout Caching**: Caches transition delay calculations
2. **Conditional Rendering**: Only renders content when active
3. **Ref Management**: Efficient DOM reference handling
4. **Animation Optimization**: Smooth CSS transitions

### Accessibility Features

1. **Focus Management**: Automatic focus on step activation
2. **ARIA Busy**: Indicates loading state
3. **Keyboard Navigation**: Proper focus order
4. **Screen Reader Support**: Proper semantic markup

### Responsive Design

1. **Mobile Detection**: Different behavior for mobile devices
2. **Touch Optimization**: Touch-friendly interactions
3. **Viewport Adaptation**: Responsive scroll positioning
4. **Animation Control**: Disabled animations on mobile

### Integration Points

The component integrates with:
- **CheckoutStepHeader**: Step header rendering
- **MobileView**: Responsive design handling
- **CSSTransition**: Animation management
- **Parent Components**: Step state management

## Source Files

- **Main Implementation**: `packages/core/src/app/checkout/CheckoutStep.tsx`
- **Test File**: `packages/core/src/app/checkout/CheckoutStep.test.tsx`
- **Dependencies**: React Transition Group, Mobile View, CheckoutStepHeader
