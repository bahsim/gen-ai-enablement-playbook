# Core Package Data Flow Architecture - State Management

## Architecture Overview

**Purpose**: Documents the complete data flow and state management architecture of the BigCommerce Core Package.

**Architecture**: SDK-first state management with BigCommerce SDK, React Context for component coordination, and HOC patterns for state access.

## State Management Layers

### 1. Application State Layer

#### CheckoutApp State
**Purpose**: Application-level state and service initialization
**Architecture**: Class component with service management
**Source Code**: `packages/core/src/app/checkout/CheckoutApp.tsx`

**State Properties**:
- **checkoutService**: BigCommerce SDK service instance
- **embeddedStylesheet**: Embedded checkout styling
- **embeddedSupport**: Embedded checkout support
- **errorLogger**: Error logging service

#### OrderConfirmationApp State
**Purpose**: Order confirmation application-level state and service initialization
**Architecture**: Class component with service management
**Source Code**: `packages/core/src/app/order/OrderConfirmationApp.tsx`

**State Properties**:
- **checkoutService**: BigCommerce SDK service instance for order data
- **accountService**: Guest account creation service
- **embeddedStylesheet**: Embedded checkout styling
- **errorLogger**: Error logging service

#### CheckoutApp Initialization Flow
```mermaid
sequenceDiagram
    participant App as CheckoutApp
    participant SDK as BigCommerce SDK
    participant Analytics as Analytics Provider
    participant Locale as Locale Provider
    participant Error as Error Boundary
    
    App->>SDK: createCheckoutService()
    App->>Analytics: AnalyticsProvider setup
    App->>Locale: LocaleProvider setup
    App->>Error: ErrorBoundary setup
    App->>App: Initialize services
```

#### OrderConfirmationApp Initialization Flow
```mermaid
sequenceDiagram
    participant App as OrderConfirmationApp
    participant SDK as BigCommerce SDK
    participant Account as Account Service
    participant Analytics as Analytics Provider
    participant Locale as Locale Provider
    participant Error as Error Boundary
    
    App->>SDK: createCheckoutService()
    App->>Account: AccountService setup
    App->>Analytics: AnalyticsProvider setup
    App->>Locale: LocaleProvider setup
    App->>Error: ErrorBoundary setup
    App->>App: Initialize services
```

### 2. Global State Layer

#### CheckoutState Interface
**Purpose**: Global checkout state management
**Architecture**: Class component state with complex state coordination
**Source Code**: `packages/core/src/app/checkout/Checkout.tsx`

```typescript
interface CheckoutState {
    activeStepType?: CheckoutStepType;
    isBillingSameAsShipping: boolean;
    customerViewType?: CustomerViewType;
    defaultStepType?: CheckoutStepType;
    error?: Error;
    flashMessages?: FlashMessage[];
    isMultiShippingMode: boolean;
    isCartEmpty: boolean;
    isRedirecting: boolean;
    hasSelectedShippingOptions: boolean;
    isSubscribed: boolean;
    buttonConfigs: PaymentMethod[];
}
```

#### State Management Flow
```mermaid
flowchart TD
    UserAction[User Action] --> ComponentState[Component State Update]
    ComponentState --> CheckoutState[CheckoutState Update]
    CheckoutState --> SDKUpdate[BigCommerce SDK Update]
    SDKUpdate --> StateSync[State Synchronization]
    StateSync --> UIRender[UI Re-render]
    UIRender --> Analytics[Analytics Tracking]
```

### 3. Context State Layer

#### BillingAndPayment Context
**Purpose**: Shared state for billing and payment components
**Architecture**: React Context with providers and consumers
**Source Code**: `packages/core/src/app/billing-and-payment/context/BillingAndPaymentContext.ts`

**Context Properties**:
- **isBillingAndPaymentCombined**: Boolean flag for combined billing and payment
- **isBillingReady**: Boolean flag for billing readiness
- **isPaymentReady**: Boolean flag for payment readiness
- **setBillingReady**: Function to set billing ready state
- **setPaymentReady**: Function to set payment ready state
- **submitBilling**: Optional billing submission handler
- **submitPayment**: Optional payment submission handler
- **setSubmitBilling**: Function to set billing submission handler
- **setSubmitPayment**: Function to set payment submission handler

#### Context State Flow
```mermaid
graph TB
    subgraph "BillingAndPayment Context"
        Provider[Context Provider]
        Consumer[Context Consumer]
        State[Shared State]
    end
    
    Provider --> State
    State --> Consumer
    Consumer --> Component[Component Update]
    Component --> Provider
```

### 4. HOC State Management Layer

#### withCheckout HOC
**Purpose**: Primary state management pattern for accessing BigCommerce SDK state
**Architecture**: Higher-Order Component with SDK state injection
**Source Code**: `packages/core/src/app/checkout/withCheckout.tsx`

**HOC Properties**:
- **CheckoutContext**: BigCommerce SDK context injection
- **State Access**: Direct access to SDK state and methods
- **State Updates**: SDK state update methods
- **Error Handling**: SDK error handling integration

#### withCheckout State Flow
```mermaid
sequenceDiagram
    participant Component as React Component
    participant HOC as withCheckout HOC
    participant Context as CheckoutContext
    participant SDK as BigCommerce SDK
    
    Component->>HOC: Component wrapped with HOC
    HOC->>Context: Inject CheckoutContext
    Context->>SDK: Access SDK state
    SDK->>Context: Return state and methods
    Context->>HOC: Provide state to component
    HOC->>Component: Component receives SDK state
```

### 5. Component State Layer

#### Local Component State
**Purpose**: Component-specific state management
**Architecture**: React hooks and local state
**Source Code**: `packages/core/src/app/billing/BillingForm.tsx`, `packages/core/src/app/payment/PaymentForm.tsx`

**Actual Hook Usage**:
- **useState**: Local state management (e.g., `isResettingAddress`, `isAddressSelectedFromDropdown`)
- **useEffect**: Side effect management (form validation, API calls)
- **useCallback**: Function memoization (e.g., `handlePaymentMethodSelect`)
- **useMemo**: Value memoization for expensive calculations
- **memo**: Component memoization for performance optimization

#### Form State Management
**Purpose**: Form state management with Formik integration
**Architecture**: Formik with React state coordination
**Key Features**:
- **Form Data**: Form input data synchronization
- **Validation State**: Form validation state management
- **Error State**: Form error state handling
- **Submission State**: Form submission state management

#### Component State Flow
```mermaid
sequenceDiagram
    participant User as User Interaction
    participant Component as Component
    participant State as Local State
    participant Context as Context State
    participant SDK as BigCommerce SDK
    
    User->>Component: User Action
    Component->>State: Update Local State
    Component->>Context: Update Context State
    Component->>SDK: Update SDK State
    SDK->>Component: State Response
    Component->>User: UI Update
```

## Data Flow Architecture

### 1. Checkout Flow Data Architecture

#### Checkout Flow State Management
**Purpose**: Complete checkout process state management
**Architecture**: SDK-first with React component coordination
**Source Code**: `packages/core/src/app/checkout/Checkout.tsx`

**Checkout Flow Properties**:
- **Step Management**: CheckoutStepType enum management
- **Customer Management**: CustomerViewType enum management
- **State Coordination**: Complex state coordination between modules
- **Error Handling**: Comprehensive error handling and recovery

#### Checkout Flow Data Flow
```mermaid
flowchart TD
    renderCheckout[renderCheckout] --> CheckoutApp[CheckoutApp]
    CheckoutApp --> Checkout[Checkout Component]
    Checkout --> CheckoutFlow[Checkout Flow]
    CheckoutFlow --> BillingAndPayment[BillingAndPayment]
    CheckoutFlow --> Customer[Customer]
    CheckoutFlow --> Shipping[Shipping]
    CheckoutFlow --> Cart[Cart]
    
    CheckoutFlow --> SDK[BigCommerce SDK]
    SDK --> StateUpdate[State Update]
    StateUpdate --> UIUpdate[UI Update]
```

### 2. Order Confirmation Flow Data Architecture

#### Order Confirmation Flow State Management
**Purpose**: Order confirmation and post-purchase state management
**Architecture**: Independent flow with SDK state access
**Source Code**: `packages/core/src/app/order/OrderConfirmation.tsx`

**Order Confirmation Flow Properties**:
- **Order State**: OrderConfirmationState interface
- **Guest Account**: Guest account creation state
- **Order Summary**: Order details and pricing state
- **Thank You**: Post-purchase experience state

#### Order Confirmation Flow Data Flow
```mermaid
flowchart TD
    renderOrderConfirmation[renderOrderConfirmation] --> OrderConfirmationApp[OrderConfirmationApp]
    OrderConfirmationApp --> OrderConfirmation[OrderConfirmation Component]
    OrderConfirmation --> OrderSummary[Order Summary]
    OrderConfirmation --> GuestSignup[Guest Signup]
    OrderConfirmation --> ThankYou[Thank You]
    
    OrderConfirmation --> SDK[BigCommerce SDK]
    SDK --> OrderStateUpdate[Order State Update]
    OrderStateUpdate --> OrderUIUpdate[Order UI Update]
```

### 3. Top-Down Data Flow

#### Props and Context Flow
```mermaid
graph TD
    CheckoutApp[CheckoutApp] --> Checkout[Checkout Component]
    Checkout --> BillingAndPayment[BillingAndPayment]
    Checkout --> Customer[Customer]
    Checkout --> Shipping[Shipping]
    Checkout --> Order[Order]
    
    CheckoutApp --> Context[Context Providers]
    Context --> BillingAndPayment
    Context --> Customer
    Context --> Shipping
    Context --> Order
```

#### Data Flow Properties
- **Props**: Component prop data flow
- **Context**: Shared context data flow
- **Configuration**: Configuration data flow
- **Services**: Service data flow

### 2. Bottom-Up Data Flow

#### Event and Callback Flow
```mermaid
graph TD
    UserAction[User Action] --> Component[Component Event]
    Component --> Parent[Parent Component]
    Parent --> Checkout[Checkout Component]
    Checkout --> SDK[BigCommerce SDK]
    SDK --> State[State Update]
    State --> UI[UI Re-render]
```

#### Event Flow Properties
- **User Events**: User interaction events
- **Form Events**: Form submission and validation events
- **Navigation Events**: Step navigation events
- **Error Events**: Error handling events

### 3. Cross-Module Data Flow

#### Inter-Module Communication
```mermaid
graph TB
    subgraph "Core Modules"
        Checkout[Checkout]
        BillingAndPayment[BillingAndPayment]
        Customer[Customer]
        Shipping[Shipping]
        Order[Order]
        Cart[Cart]
    end
    
    Checkout <--> BillingAndPayment
    Checkout <--> Customer
    Checkout <--> Shipping
    Checkout <--> Order
    Checkout <--> Cart
    
    BillingAndPayment <--> Customer
    BillingAndPayment <--> Shipping
    Customer <--> Shipping
    Shipping <--> Order
```

#### Communication Patterns
- **Direct Communication**: Direct component communication
- **Context Communication**: Context-based communication
- **Event Communication**: Event-driven communication
- **Service Communication**: Service-based communication

### 4. External Data Flow

#### BigCommerce SDK Integration
```mermaid
sequenceDiagram
    participant Component as React Component
    participant SDK as BigCommerce SDK
    participant API as BigCommerce API
    participant State as Component State
    
    Component->>SDK: SDK Method Call
    SDK->>API: API Request
    API->>SDK: API Response
    SDK->>State: State Update
    State->>Component: Component Re-render
```

#### External Integration Flow
- **API Calls**: External API integration
- **Data Synchronization**: Data sync with external services
- **Error Handling**: External service error handling
- **State Persistence**: State persistence with external services

## State Synchronization

### 1. SDK State Synchronization

#### BigCommerce SDK State Management
**Purpose**: Synchronize React state with BigCommerce SDK state
**Architecture**: SDK service integration with React state
**Source Code**: `packages/core/src/app/checkout/withCheckout.tsx`

**Synchronization Flow**:
```mermaid
flowchart TD
    SDKState[SDK State] --> ReactState[React State]
    ReactState --> ComponentState[Component State]
    ComponentState --> UIState[UI State]
    UIState --> UserAction[User Action]
    UserAction --> SDKUpdate[SDK Update]
    SDKUpdate --> SDKState
```

### 2. Form State Synchronization

#### Form State Management
**Purpose**: Synchronize form state across components
**Architecture**: Formik integration with React state
**Key Features**:
- **Form Data**: Form input data synchronization
- **Validation State**: Form validation state synchronization
- **Error State**: Form error state synchronization
- **Submission State**: Form submission state synchronization

#### Form State Flow
```mermaid
sequenceDiagram
    participant Form as Form Component
    participant Formik as Formik State
    participant Context as Form Context
    participant Validation as Validation State
    
    Form->>Formik: Form Input
    Formik->>Context: Update Context
    Context->>Validation: Validate Data
    Validation->>Formik: Validation Result
    Formik->>Form: Update Form State
```

### 3. Cross-Component State Synchronization

#### Shared State Management
**Purpose**: Synchronize state across multiple components
**Architecture**: React Context with state management
**Key Features**:
- **Shared State**: State shared across components
- **State Updates**: Coordinated state updates
- **State Validation**: State validation and error handling
- **State Persistence**: State persistence and recovery

#### Cross-Component Flow
```mermaid
graph TB
    ComponentA[Component A] --> SharedState[Shared State]
    ComponentB[Component B] --> SharedState
    ComponentC[Component C] --> SharedState
    
    SharedState --> ComponentA
    SharedState --> ComponentB
    SharedState --> ComponentC
```

## Error Handling and Recovery

### 1. Error State Management

#### Error State Flow
```mermaid
flowchart TD
    Error[Error Occurrence] --> ErrorBoundary[Error Boundary]
    ErrorBoundary --> ErrorLogging[Error Logging]
    ErrorLogging --> ErrorDisplay[Error Display]
    ErrorDisplay --> ErrorRecovery[Error Recovery]
    ErrorRecovery --> StateReset[State Reset]
    StateReset --> NormalFlow[Normal Flow]
```

#### Error Handling Properties
- **Error Boundaries**: React error boundary implementation
- **Error Logging**: Error logging and monitoring with SentryErrorLogger
- **Error Display**: User-friendly error messages with ErrorModal
- **Error Recovery**: Error recovery mechanisms
- **Custom Errors**: CustomError and RequestError handling
- **Error Types**: Specific error type handling (CartChangedError, etc.)

### 2. State Recovery

#### State Recovery Flow
```mermaid
sequenceDiagram
    participant Error as Error State
    participant Recovery as Recovery Logic
    participant State as State Reset
    participant Component as Component Update
    participant User as User Experience
    
    Error->>Recovery: Trigger Recovery
    Recovery->>State: Reset State
    State->>Component: Update Component
    Component->>User: Restore Experience
```

#### Recovery Mechanisms
- **State Reset**: Reset to previous valid state
- **Component Recovery**: Component-level recovery
- **Service Recovery**: External service recovery
- **User Recovery**: User-guided recovery

## Performance Optimization

### 1. State Optimization

#### Memoization Strategy
```mermaid
graph TB
    State[Component State] --> MemoCheck[Memoization Check]
    MemoCheck -->|Changed| ReRender[Re-render Component]
    MemoCheck -->|Unchanged| SkipRender[Skip Re-render]
    
    Props[Component Props] --> MemoCheck
    Context[Context State] --> MemoCheck
```

#### Optimization Techniques
- **React.memo()**: Component memoization (e.g., `PaymentForm` with `memo`)
- **useMemo()**: Value memoization for expensive calculations
- **useCallback()**: Function memoization (e.g., `handlePaymentMethodSelect`)
- **State Optimization**: State update optimization
- **Component Memoization**: Prevent unnecessary re-renders
- **Function Memoization**: Prevent function recreation on every render

### 2. Data Flow Optimization

#### Efficient Data Flow
```mermaid
flowchart TD
    Data[Data Source] --> Filter[Data Filtering]
    Filter --> Transform[Data Transformation]
    Transform --> Memoize[Data Memoization]
    Memoize --> Component[Component Update]
    Component --> Render[Render Optimization]
```

#### Flow Optimization
- **Data Filtering**: Filter unnecessary data
- **Data Transformation**: Optimize data transformation
- **Memoization**: Memoize expensive calculations
- **Render Optimization**: Optimize rendering performance

## Testing Strategy

### 1. State Testing

#### State Management Testing
```mermaid
graph TB
    Test[Test Case] --> StateTest[State Testing]
    StateTest --> ComponentTest[Component Testing]
    ComponentTest --> IntegrationTest[Integration Testing]
    IntegrationTest --> E2ETest[E2E Testing]
```

#### Testing Approaches
- **Unit Testing**: Individual state testing
- **Integration Testing**: State integration testing
- **Component Testing**: Component state testing
- **E2E Testing**: End-to-end state testing

### 2. Data Flow Testing

#### Flow Testing Strategy
```mermaid
sequenceDiagram
    participant Test as Test Case
    participant Component as Component
    participant State as State Management
    participant SDK as BigCommerce SDK
    participant UI as UI Update
    
    Test->>Component: Trigger Action
    Component->>State: Update State
    State->>SDK: Sync with SDK
    SDK->>State: Update Response
    State->>UI: Trigger Update
    Test->>UI: Verify Result
```

#### Flow Testing Properties
- **Data Flow Testing**: Test data flow between components
- **State Transition Testing**: Test state transitions
- **Integration Testing**: Test external integrations
- **Error Flow Testing**: Test error handling flows
