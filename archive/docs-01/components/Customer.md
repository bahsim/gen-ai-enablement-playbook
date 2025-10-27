# Customer Component - Implementation Analysis

## Core Architecture

The `Customer` component manages the entire customer authentication flow, including login, guest checkout, account creation, and wallet button integration. It's a sophisticated class component that handles multiple customer view types with complex state management and payment method integration.

### State Management Pattern

```typescript
export interface CustomerState {
    isEmailLoginFormOpen: boolean;        // Email login form visibility
    isReady: boolean;                     // Component readiness state
    hasRequestedLoginEmail: boolean;      // Email login request tracking
}
```

**Key Implementation Details:**
- Manages email login form state separately from main view
- Tracks component readiness for proper initialization
- Uses draft email for form state management

### Props Interface

The component accepts extensive props for customer management:

```typescript
export interface CustomerProps {
    viewType: CustomerViewType;           // Current view type (Login/Guest/CreateAccount)
    step: CheckoutStepStatus;             // Step configuration
    isEmbedded?: boolean;                 // Embedded checkout flag
    isSubscribed: boolean;                // Newsletter subscription state
    isWalletButtonsOnTop: boolean;        // Wallet button positioning
    checkEmbeddedSupport?(methodIds: string[]): void;
    onChangeViewType?(viewType: CustomerViewType): void;
    onAccountCreated?(): void;
    onContinueAsGuest?(): void;
    onContinueAsGuestError?(error: Error): void;
    onReady?(): void;
    onSubscribeToNewsletter(subscribe: boolean): void;
    onSignIn?(): void;
    onSignInError?(error: Error): void;
    onUnhandledError?(error: Error): void;
    onWalletButtonClick?(methodName: string): void;
}
```

### View Type Management

The component manages different customer view types:

```typescript
private renderGuestForm(): ReactNode {
    const { viewType } = this.props;
    const { isEmailLoginFormOpen, isReady } = this.state;
    const shouldRenderGuestForm = viewType === CustomerViewType.Guest;
    const shouldRenderCreateAccountForm = viewType === CustomerViewType.CreateAccount;
    const shouldRenderLoginForm = !shouldRenderGuestForm && !shouldRenderCreateAccountForm;

    return (
        <CustomerSkeleton isLoading={!isReady}>
            {isEmailLoginFormOpen && this.renderEmailLoginLinkForm()}
            {shouldRenderLoginForm && this.renderLoginForm()}
            {shouldRenderGuestForm && this.renderGuestForm()}
            {shouldRenderCreateAccountForm && this.renderCreateAccountForm()}
        </CustomerSkeleton>
    );
}
```

**View Type Strategy:**
- Conditional rendering based on current view type
- Email login form can overlay any view type
- Skeleton loading for better UX during initialization

### Initialization Flow

The component implements sophisticated initialization:

```typescript
async componentDidMount(): Promise<void> {
    const {
        initializeCustomer,
        email,
        onReady = noop,
        onUnhandledError = noop,
        providerWithCustomCheckout,
    } = this.props;

    this.draftEmail = email;

    try {
        if (providerWithCustomCheckout && providerWithCustomCheckout !== PaymentMethodId.StripeUPE) {
            await initializeCustomer({methodId: providerWithCustomCheckout});
        }
    } catch (error) {
        onUnhandledError(error);
    }

    this.setState({ isReady: true });
    onReady();
}
```

**Initialization Strategy:**
- Stores draft email for form state management
- Initializes customer with payment method if needed
- Handles errors gracefully with proper cleanup
- Notifies parent when ready

### Guest Form Rendering

The guest form includes complex conditional rendering:

```typescript
private renderGuestForm(): ReactNode {
    const {
        canSubscribe,
        checkEmbeddedSupport,
        checkoutButtonIds,
        deinitializeCustomer,
        email,
        initializeCustomer,
        isContinuingAsGuest = false,
        isExecutingPaymentMethodCheckout = false,
        isInitializing = false,
        isSubscribed,
        isWalletButtonsOnTop,
        privacyPolicyUrl,
        requiresMarketingConsent,
        onUnhandledError = noop,
        onWalletButtonClick = noop,
        step,
        isFloatingLabelEnabled,
        isExpressPrivacyPolicy,
        isPaymentDataRequired,
        shouldRenderStripeForm,
        providerWithCustomCheckout,
        checkoutSettings,
    } = this.props;

    const checkoutButtons = isWalletButtonsOnTop || !isPaymentDataRequired
      ? null
      : <CheckoutButtonList
        checkEmbeddedSupport={checkEmbeddedSupport}
        checkoutSettings={checkoutSettings}
        deinitialize={deinitializeCustomer}
        initialize={initializeCustomer}
        isInitializing={isInitializing}
        methodIds={checkoutButtonIds}
        onClick={onWalletButtonClick}
        onError={onUnhandledError}
      />;

    const isLoadingGuestForm = isContinuingAsGuest || isExecutingPaymentMethodCheckout;

    return (
        shouldRenderStripeForm ?
            <StripeGuestForm
                canSubscribe={canSubscribe}
                checkoutButtons={checkoutButtons}
                continueAsGuestButtonLabelId="customer.continue"
                defaultShouldSubscribe={isSubscribed}
                deinitialize={deinitializeCustomer}
                email={this.draftEmail || email}
                initialize={initializeCustomer}
                isExpressPrivacyPolicy={isExpressPrivacyPolicy}
                isLoading={isContinuingAsGuest || isInitializing || isExecutingPaymentMethodCheckout}
                onChangeEmail={this.handleChangeEmail}
                onContinueAsGuest={this.handleContinueAsGuest}
                onShowLogin={this.handleShowLogin}
                privacyPolicyUrl={privacyPolicyUrl}
                requiresMarketingConsent={requiresMarketingConsent}
                step={step}
            />
            :
        <GuestForm
            canSubscribe={canSubscribe}
            checkoutButtons={checkoutButtons}
            continueAsGuestButtonLabelId="customer.continue"
            defaultShouldSubscribe={isSubscribed}
            email={this.draftEmail || email}
            isExpressPrivacyPolicy={isExpressPrivacyPolicy}
            isFloatingLabelEnabled={isFloatingLabelEnabled}
            isLoading={isLoadingGuestForm}
            onChangeEmail={this.handleChangeEmail}
            onContinueAsGuest={this.handleContinueAsGuest}
            onShowLogin={this.handleShowLogin}
            privacyPolicyUrl={privacyPolicyUrl}
            requiresMarketingConsent={requiresMarketingConsent}
            shouldShowEmailWatermark={isPayPalFastlaneMethod(providerWithCustomCheckout)}
            step={step}
        />
    );
}
```

**Guest Form Strategy:**
- Conditional rendering between Stripe and standard guest forms
- Wallet button integration with conditional display
- Email watermark for PayPal Fastlane
- Comprehensive loading state management

### Email Login Form Management

The component handles email login form state:

```typescript
private handleShowEmailLoginForm: () => void = () => {
    this.setState({ isEmailLoginFormOpen: true });
};

private handleHideEmailLoginForm: () => void = () => {
    this.setState({ isEmailLoginFormOpen: false });
};

private renderEmailLoginLinkForm(): ReactNode {
    const {
        isSendingSignInEmail = false,
        isSignInEmailEnabled = false,
        signInEmailError,
        onSignInError = noop,
        onUnhandledError = noop,
        sendLoginEmail,
        clearError,
    } = this.props;

    return (
        <EmailLoginForm
            isSendingSignInEmail={isSendingSignInEmail}
            isSignInEmailEnabled={isSignInEmailEnabled}
            onCancel={this.handleHideEmailLoginForm}
            onSendLoginEmail={this.handleSendLoginEmail}
            onShowLogin={this.handleShowLogin}
            signInEmailError={signInEmailError}
        />
    );
}
```

**Email Login Strategy:**
- Overlay form that can appear over any view type
- Handles email sending and error states
- Provides cancel and login options
- Manages form visibility state

### Account Creation Flow

The component handles account creation with validation:

```typescript
private renderCreateAccountForm(): ReactNode {
    const {
        canSubscribe,
        checkoutButtonIds,
        customerAccountFields,
        defaultShouldSubscribe,
        isCreatingAccount = false,
        isInitializing = false,
        isSubscribed,
        privacyPolicyUrl,
        requiresMarketingConsent,
        onUnhandledError = noop,
        onWalletButtonClick = noop,
        step,
        isFloatingLabelEnabled,
        isExpressPrivacyPolicy,
        isPaymentDataRequired,
        shouldRenderStripeForm,
        providerWithCustomCheckout,
        checkoutSettings,
    } = this.props;

    const checkoutButtons = isWalletButtonsOnTop || !isPaymentDataRequired
        ? null
        : <CheckoutButtonList
            checkEmbeddedSupport={this.props.checkEmbeddedSupport}
            checkoutSettings={checkoutSettings}
            deinitialize={this.props.deinitializeCustomer}
            initialize={this.props.initializeCustomer}
            isInitializing={isInitializing}
            methodIds={checkoutButtonIds}
            onClick={onWalletButtonClick}
            onError={onUnhandledError}
        />;

    return (
        shouldRenderStripeForm ?
            <StripeGuestForm
                canSubscribe={canSubscribe}
                checkoutButtons={checkoutButtons}
                continueAsGuestButtonLabelId="customer.create_account"
                defaultShouldSubscribe={isSubscribed}
                deinitialize={this.props.deinitializeCustomer}
                email={this.draftEmail}
                initialize={this.props.initializeCustomer}
                isExpressPrivacyPolicy={isExpressPrivacyPolicy}
                isLoading={isCreatingAccount || isInitializing}
                onChangeEmail={this.handleChangeEmail}
                onContinueAsGuest={this.handleCreateAccount}
                onShowLogin={this.handleShowLogin}
                privacyPolicyUrl={privacyPolicyUrl}
                requiresMarketingConsent={requiresMarketingConsent}
                step={step}
            />
            :
        <CreateAccountForm
            canSubscribe={canSubscribe}
            checkoutButtons={checkoutButtons}
            createAccountButtonLabelId="customer.create_account"
            customerAccountFields={customerAccountFields}
            defaultShouldSubscribe={isSubscribed}
            email={this.draftEmail}
            isExpressPrivacyPolicy={isExpressPrivacyPolicy}
            isFloatingLabelEnabled={isFloatingLabelEnabled}
            isLoading={isCreatingAccount || isInitializing}
            onChangeEmail={this.handleChangeEmail}
            onCreateAccount={this.handleCreateAccount}
            onShowLogin={this.handleShowLogin}
            privacyPolicyUrl={privacyPolicyUrl}
            requiresMarketingConsent={requiresMarketingConsent}
            shouldShowEmailWatermark={isPayPalFastlaneMethod(providerWithCustomCheckout)}
            step={step}
        />
    );
}
```

**Account Creation Strategy:**
- Similar to guest form but with account creation validation
- Uses customer account fields for validation
- Handles both Stripe and standard forms
- Manages loading states for account creation

### Wallet Button Integration

The component integrates wallet buttons conditionally:

```typescript
const checkoutButtons = isWalletButtonsOnTop || !isPaymentDataRequired
    ? null
    : <CheckoutButtonList
        checkEmbeddedSupport={checkEmbeddedSupport}
        checkoutSettings={checkoutSettings}
        deinitialize={deinitializeCustomer}
        initialize={initializeCustomer}
        isInitializing={isInitializing}
        methodIds={checkoutButtonIds}
        onClick={onWalletButtonClick}
        onError={onUnhandledError}
    />;
```

**Wallet Button Strategy:**
- Only renders when not on top and payment data is required
- Passes all necessary configuration to button list
- Handles initialization and deinitialization
- Provides error handling and click callbacks

### Email Management

The component manages email state across different forms:

```typescript
private handleChangeEmail: (email: string) => void = (email) => {
    this.draftEmail = email;
};

private handleContinueAsGuest: (values: GuestFormValues) => void = async (values) => {
    const {
        continueAsGuest,
        onContinueAsGuest = noop,
        onContinueAsGuestError = noop,
        onUnhandledError = noop,
    } = this.props;

    try {
        await continueAsGuest(values);
        onContinueAsGuest();
    } catch (error) {
        if (isErrorWithType(error) && error.type === 'checkout_not_available') {
            onContinueAsGuestError(error);
        } else {
            onUnhandledError(error);
        }
    }
};
```

**Email Management Strategy:**
- Maintains draft email across form switches
- Handles email changes in real-time
- Provides email to all forms consistently
- Manages email state during form submissions

### Error Handling

The component implements comprehensive error handling:

```typescript
private handleCreateAccount: (values: CreateAccountFormValues) => void = async (values) => {
    const {
        createAccount,
        onAccountCreated = noop,
        onUnhandledError = noop,
    } = this.props;

    try {
        await createAccount(mapCreateAccountFromFormValues(values));
        onAccountCreated();
    } catch (error) {
        onUnhandledError(error);
    }
};
```

**Error Handling Strategy:**
- Specific error handling for different operations
- Error propagation to parent components
- Graceful error recovery
- User-friendly error messages

### Performance Optimizations

1. **Conditional Rendering**: Only renders necessary components based on view type
2. **Skeleton Loading**: Provides loading states for better UX
3. **State Management**: Minimal state with derived values from props
4. **Form State**: Efficient email state management across forms

### Integration Points

The component integrates with:
- **BigCommerce SDK**: Customer authentication and account creation
- **Payment Methods**: Wallet button integration
- **Stripe**: Special form handling
- **PayPal Fastlane**: Email watermark display
- **Analytics**: User action tracking

## Source Files

- **Main Implementation**: `packages/core/src/app/customer/Customer.tsx`
- **Guest Form**: `packages/core/src/app/customer/GuestForm.tsx`
- **Login Form**: `packages/core/src/app/customer/LoginForm.tsx`
- **Create Account Form**: `packages/core/src/app/customer/CreateAccountForm.tsx`
- **Email Login Form**: `packages/core/src/app/customer/EmailLoginForm.tsx`
- **Stripe Guest Form**: `packages/core/src/app/customer/StripeGuestForm.tsx`
- **Checkout Button List**: `packages/core/src/app/customer/CheckoutButtonList.tsx`
