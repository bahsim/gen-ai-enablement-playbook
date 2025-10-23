# Core Package Documentation

## Overview

This folder contains comprehensive documentation for the BigCommerce Core Package, organized into distinct flows and shared components.

## Folder Structure

```
core/
├── architecture/           # Core package architecture documentation
│   ├── core-package-architecture.md
│   ├── core-module-breakdown.md
│   ├── core-data-flow-architecture.md
│   └── core-integration-architecture.md
├── checkout/              # Checkout flow documentation
│   ├── 01-checkout.md
│   ├── 02-shipping.md
│   ├── 03-billing-and-payment.md
│   ├── 04-billing.md
│   └── 05-payment.md
├── order-confirmation/    # Order confirmation flow documentation
│   └── order-confirmation.md
├── shared/                # Shared components documentation
│   ├── README.md
│   ├── 01-address.md
│   ├── 02-cart.md
│   ├── 03-customer.md
│   ├── 04-analytics.md
│   └── 05-ui.md
└── README.md              # This file
```

## Documentation Organization

### 1. Architecture Documentation (`architecture/`)
**Purpose**: High-level architectural documentation for the entire core package
**Contents**:
- **Core Package Architecture**: Overall system architecture and design
- **Module Breakdown**: Detailed breakdown of all core modules
- **Data Flow Architecture**: Data flow and state management architecture
- **Integration Architecture**: External system integration architecture

### 2. Checkout Flow Documentation (`checkout/`)
**Purpose**: Documentation for the checkout flow components and functionality
**Contents**:
- **Checkout Component**: Main checkout orchestrator and flow management
- **Shipping Component**: Shipping address and method selection
- **Billing Component**: Billing address collection and validation
- **Payment Component**: Payment method selection and processing
- **BillingAndPayment Component**: Combined billing and payment step

### 3. Order Confirmation Flow Documentation (`order-confirmation/`)
**Purpose**: Documentation for the order confirmation flow components and functionality
**Contents**:
- **OrderConfirmation Component**: Order completion and confirmation display
- **Guest Account Creation**: Guest account creation after order completion
- **Order Summary**: Order summary and item display
- **Embedded Checkout**: Embedded checkout integration and messaging

### 4. Shared Components Documentation (`shared/`)
**Purpose**: Documentation for components shared between checkout and order confirmation flows
**Contents**:
- **Address Component**: Address management and validation
- **Cart Component**: Cart display and item management
- **Customer Component**: Customer authentication and account management
- **Analytics Component**: User behavior tracking and analytics
- **UI Component**: Shared UI components and utilities

## Flow Distinction

### Checkout Flow
- **Purpose**: Handles the complete checkout process from cart to order creation
- **Key Components**: Checkout, Shipping, Billing, Payment, BillingAndPayment
- **Flow Steps**: Customer → Shipping → BillingAndPayment → OrderDetails
- **End State**: Order creation and checkout completion

### Order Confirmation Flow
- **Purpose**: Handles order confirmation display and guest account creation
- **Key Components**: OrderConfirmation, OrderConfirmationApp
- **Flow Steps**: Order loading → Confirmation display → Guest account creation
- **End State**: Order confirmation and account creation completion

### Shared Components
- **Purpose**: Provides common functionality used by both flows
- **Key Components**: Address, Cart, Customer, Analytics, UI
- **Usage**: Used throughout both checkout and order confirmation flows
- **Benefits**: Consistent experience and code reuse

## Integration Patterns

### 1. Flow Independence
- **Separate Applications**: Checkout and Order Confirmation are separate applications
- **Independent State**: Each flow manages its own state
- **Separate Entry Points**: Different entry points and initialization

### 2. Shared Component Usage
- **Common Functionality**: Shared components provide common functionality
- **Flow Agnostic**: Shared components work in both flows
- **Consistent Experience**: Shared components ensure consistent user experience

### 3. State Management
- **Flow State**: Each flow manages its own state
- **Shared State**: Shared components use common state management patterns
- **State Coordination**: State coordination between flows when needed

## Development Guidelines

### 1. Component Organization
- **Flow-Specific**: Components specific to one flow go in that flow's folder
- **Shared Components**: Components used by both flows go in the shared folder
- **Architecture**: High-level architecture documentation in the architecture folder

### 2. Documentation Standards
- **Consistent Structure**: All documentation follows consistent structure
- **Source Code References**: All documentation includes source code references
- **No Fabrications**: All documentation is based on actual codebase
- **Comprehensive Coverage**: Complete coverage of all components and functionality

### 3. Maintenance
- **Regular Updates**: Documentation updated with code changes
- **Accuracy Verification**: Regular verification of documentation accuracy
- **Completeness**: Regular review for documentation completeness

## Future Considerations

### 1. Enhanced Organization
- **Additional Flows**: Support for additional flows if needed
- **Better Separation**: Enhanced separation between flows
- **Improved Shared Components**: Enhanced shared component functionality

### 2. Documentation Improvements
- **Enhanced Documentation**: Improved documentation quality and coverage
- **Better Examples**: Enhanced usage examples and patterns
- **Developer Tools**: Better development tools and utilities

### 3. Architecture Evolution
- **Architecture Updates**: Updates to architecture documentation
- **New Patterns**: Documentation of new architectural patterns
- **Best Practices**: Documentation of best practices and guidelines
