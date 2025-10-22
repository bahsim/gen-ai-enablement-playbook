# OrderConfirmation Component - Implementation Analysis

## Core Architecture

The `OrderConfirmation` component is the final step in the checkout flow, displaying order details, payment instructions, and providing guest signup functionality. It's a complex class component that handles order loading, embedded checkout communication, and customer account creation.

### State Management Pattern

```typescript
export interface OrderConfirmationState {
    error?: Error;              // Global error state
    hasSignedUp?: boolean;      // Guest signup completion state
    isSigningUp?: boolean;      // Guest signup in progress state
}
```

**Key Implementation Details:**
- Manages error state for order loading and signup failures
- Tracks guest signup flow state
- Handles loading states for async operations

### Props Interface

The component accepts props for order confirmation functionality:

```typescript
export interface OrderConfirmationProps {
    containerId: string;                                    // Embedded checkout container ID
    embeddedStylesheet: EmbeddedCheckoutStylesheet;        // Stylesheet for embedded checkout
    errorLogger: ErrorLogger;                              // Error logging service
    orderId: number;                                       // Order ID to load
    createAccount(values: SignUpFormValues): Promise<CreatedCustomer>;  // Account creation
    createEmbeddedMessenger(options: EmbeddedCheckoutMessengerOptions): EmbeddedCheckoutMessenger;
}

interface WithCheckoutOrderConfirmationProps {
    order?: Order;                                         // Loaded order data
    config?: StoreConfig;                                  // Store configuration
    loadOrder(orderId: number): Promise<CheckoutSelectors>;  // Order loading function
    isLoadingOrder(): boolean;                            // Loading state checker
}
```

### Initialization Flow

The component implements sophisticated initialization with embedded checkout support:

```typescript
componentDidMount(): void {
    const {
        containerId,
        createEmbeddedMessenger,
        embeddedStylesheet,
        loadOrder,
    orderId,
        analyticsTracker
    } = this.props;

    loadOrder(orderId)
        .then(({ data }) => {
            const { links: { siteLink = '' } = {} } = data.getConfig() || {};
            const messenger = createEmbeddedMessenger({ parentOrigin: siteLink });

            this.embeddedMessenger = messenger;

            messenger.receiveStyles((styles) => embeddedStylesheet.append(styles));
            messenger.postFrameLoaded({ contentId: containerId });

            analyticsTracker.orderPurchased();
        })
        .catch(this.handleUnhandledError);
}
```

**Initialization Strategy:**
- Loads order data asynchronously
- Sets up embedded checkout messaging
- Handles style injection for embedded checkout
- Tracks analytics for completed purchase
- Provides error handling for failed loads

### Order Loading and Display

The component handles order loading with proper loading states:

```typescript
render(): ReactNode {
    const { order, config, isLoadingOrder } = this.props;

    if (!order || !config || isLoadingOrder()) {
        return <LoadingSpinner isLoading={true} />;
    }

    const paymentInstructions = getPaymentInstructions(order);
    const {
        storeProfile: { storePhoneNumber },
        shopperConfig,
        links: { siteLink },
    } = config;

        return (
        <div className="orderConfirmation-container">
            <div
                className={classNames('layout optimizedCheckout-contentPrimary', {
                    'is-embedded': isEmbedded(),
                })}
            >
                <div className="layout-main-order-confirmation">
                    <div className="orderConfirmation">
                        <ThankYouHeader name={order.billingAddress.firstName} />

                        <OrderStatus
                            config={config}
                            order={order}
                            userEmail={order.billingAddress.email || ''}
                            supportPhoneNumber={storePhoneNumber}
                        />

                        {paymentInstructions && (
                            <OrderConfirmationSection>
                                <div
                                    dangerouslySetInnerHTML={{
                                        __html: DOMPurify.sanitize(paymentInstructions),
                                    }}
                                    data-test="payment-instructions"
                                />
                            </OrderConfirmationSection>
                        )}

                        {this.renderGuestSignUp({
                            shouldShowPasswordForm: order.customerCanBeCreated,
                            customerCanBeCreated: !order.customerId,
                            shopperConfig,
                        })}

                        <div className="continueButtonContainer">
                            <form action={siteLink} method="get" target="_top">
                                <Button type="submit" variant={ButtonVariant.Secondary}>
                                    <TranslatedString id="order_confirmation.continue_shopping" />
                                </Button>
                            </form>
                        </div>
                </div>
        </div>

                {this.renderErrorModal()}
            </div>
            <CheckoutFooter />
        </div>
    );
}
```

**Rendering Strategy:**
- Shows loading spinner while order loads
- Displays order status and payment instructions
- Handles guest signup flow
- Provides continue shopping functionality
- Sanitizes HTML content for security

### Guest Signup Flow

The component implements sophisticated guest signup functionality:

```typescript
private renderGuestSignUp({
    customerCanBeCreated,
    shouldShowPasswordForm,
    shopperConfig,
}: {
    customerCanBeCreated: boolean;
    shouldShowPasswordForm: boolean;
    shopperConfig: ShopperConfig;
}): ReactNode {
    const { isSigningUp, hasSignedUp } = this.state;

    const { order } = this.props;

    return (
        <>
            {shouldShowPasswordForm && !hasSignedUp && (
                <GuestSignUpForm
                    customerCanBeCreated={customerCanBeCreated}
                    isSigningUp={isSigningUp}
                    onSignUp={this.handleSignUp}
                    passwordRequirements={getPasswordRequirementsFromConfig(shopperConfig)}
                />
            )}

            {hasSignedUp &&
                (order?.customerId ? <PasswordSavedSuccessAlert /> : <SignedUpSuccessAlert />)}
        </>
    );
}
```

**Guest Signup Strategy:**
- Conditional rendering based on order state
- Password requirements from store config
- Success state management
- Different alerts for different signup outcomes

### Account Creation Handling

The component handles account creation with comprehensive error handling:

```typescript
private handleSignUp: (values: SignUpFormValues) => void = ({ password, confirmPassword }) => {
    const { createAccount, config } = this.props;

    const shopperConfig = config && config.shopperConfig;
    const passwordRequirements =
        (shopperConfig &&
            shopperConfig.passwordRequirements &&
            shopperConfig.passwordRequirements.error) ||
        '';

    this.setState({
        isSigningUp: true,
    });

    createAccount({
        password,
        confirmPassword,
    })
        .then(() => {
            this.setState({
                hasSignedUp: true,
                isSigningUp: false,
            });
        })
        .catch((error) => {
            this.setState({
                error:
                    error.status < 500
                        ? new AccountCreationRequirementsError(error, passwordRequirements)
                        : new AccountCreationFailedError(error),
                hasSignedUp: false,
                isSigningUp: false,
            });
        });
};
```

**Account Creation Strategy:**
- Validates password requirements from store config
- Handles different error types with specific error classes
- Manages loading and success states
- Provides user-friendly error messages

### Error Handling

The component implements comprehensive error handling:

```typescript
private handleUnhandledError: (error: Error) => void = (error) => {
    const { errorLogger } = this.props;

    this.setState({ error });
    errorLogger.log(error);

    if (this.embeddedMessenger) {
        this.embeddedMessenger.postError(error);
    }
};

private renderErrorModal(): ReactNode {
    const { error } = this.state;

    return (
        <ErrorModal
            error={error}
            onClose={this.handleErrorModalClose}
            shouldShowErrorCode={false}
        />
    );
}

private handleErrorModalClose: () => void = () => {
    this.setState({ error: undefined });
};
```

**Error Handling Strategy:**
- Centralized error handling for all operations
- Error logging integration
- Embedded checkout error communication
- User-friendly error display
- Error state cleanup

### Payment Instructions Display

The component displays payment instructions with security measures:

```typescript
{paymentInstructions && (
    <OrderConfirmationSection>
        <div
            dangerouslySetInnerHTML={{
                __html: DOMPurify.sanitize(paymentInstructions),
            }}
            data-test="payment-instructions"
        />
    </OrderConfirmationSection>
)}
```

**Payment Instructions Strategy:**
- Conditional rendering based on payment method
- HTML sanitization for security
- Test ID for automation
- Proper section wrapping

### Embedded Checkout Integration

The component integrates with embedded checkout messaging:

```typescript
const messenger = createEmbeddedMessenger({ parentOrigin: siteLink });

this.embeddedMessenger = messenger;

messenger.receiveStyles((styles) => embeddedStylesheet.append(styles));
messenger.postFrameLoaded({ contentId: containerId });
```

**Embedded Integration Strategy:**
- Style injection from parent window
- Frame loaded notification
- Error communication to parent
- Proper cleanup on unmount

### Analytics Integration

The component tracks purchase completion:

```typescript
analyticsTracker.orderPurchased();
```

**Analytics Strategy:**
- Tracks successful order completion
- Integrates with analytics system
- Provides purchase funnel data
- Supports conversion tracking

### Props Mapping

The component uses props mapping for checkout integration:

```typescript
export function mapToOrderConfirmationProps(
    context: CheckoutContextProps,
): WithCheckoutOrderConfirmationProps | null {
    const {
        checkoutState: {
            data: { getOrder, getConfig },
            statuses: { isLoadingOrder },
        },
        checkoutService,
    } = context;

    const config = getConfig();
    const order = getOrder();

    return {
        config,
        isLoadingOrder,
        loadOrder: checkoutService.loadOrder,
        order,
    };
}
```

**Props Mapping Strategy:**
- Extracts order and config data
- Provides loading state access
- Maps checkout service methods
- Handles null state gracefully

### Performance Optimizations

1. **Conditional Rendering**: Only renders content when order is loaded
2. **Error Boundaries**: Proper error handling prevents crashes
3. **State Management**: Efficient state updates
4. **Memory Management**: Proper cleanup of embedded messenger

### Security Features

1. **HTML Sanitization**: DOMPurify for payment instructions
2. **Error Handling**: Prevents sensitive data exposure
3. **Input Validation**: Password requirements validation
4. **XSS Prevention**: Sanitized HTML content

### Integration Points

The component integrates with:
- **BigCommerce SDK**: Order loading and configuration
- **Embedded Checkout**: Messaging and styling
- **Analytics**: Purchase tracking
- **Error Logging**: Centralized error handling
- **Guest Signup**: Account creation flow

## Source Files

- **Main Implementation**: `packages/core/src/app/order/OrderConfirmation.tsx`
- **Order Status**: `packages/core/src/app/order/OrderStatus.tsx`
- **Thank You Header**: `packages/core/src/app/order/ThankYouHeader.tsx`
- **Order Confirmation Section**: `packages/core/src/app/order/OrderConfirmationSection.tsx`
- **Payment Instructions**: `packages/core/src/app/order/getPaymentInstructions.ts`
- **Guest Signup**: `packages/core/src/app/guestSignup/`