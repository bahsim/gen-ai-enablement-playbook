

# **Comprehensive Documentation Suite for the BigCommerce Checkout JavaScript SDK**

## **I. Executive Summary and Architectural Context**

### **A. Project Scope and SDK Philosophy**

The BigCommerce Checkout JavaScript SDK is a sophisticated, client-side library engineered to empower developers to create customized and unique checkout experiences for BigCommerce storefronts.1 The SDK abstracts the complex interactions required to convert a shopping cart into a completed order, providing a high-level application interface for managing the entire checkout lifecycle.1 This encompasses essential steps such as customer authentication, defining shipping and billing information, managing promotional codes, and processing payment to finalize the purchase.1

Although BigCommerce provides an "Open Checkout" reference implementation built using React, the SDK itself is built with vanilla JavaScript and remains framework agnostic.1 This architectural choice ensures maximum flexibility, allowing developers to integrate the SDK seamlessly with popular frameworks like React, Vue, or Angular, or even classic server-rendered themes based on Stencil.1 The primary purpose is to allow merchants and developers to focus on the unique front-end user experience while the SDK handles all necessary back-end logic.

### **B. The SDK as an Orchestration Layer**

The functionality of the SDK extends beyond simple API communication; it serves as a critical orchestration layer between the custom storefront, the BigCommerce Storefront APIs, and numerous external payment providers. The SDK is explicitly responsible for managing "all the necessary interactions with our Storefront APIs and other payment SDKs".1 This includes handling native integrations with diverse payment methods such as PayPal Express, Braintree, Square, Amazon, Klarna, and AfterPay, among others.1

Due to its role in managing multi-vendor payment logic and complex data validation flows, the CheckoutService instance created by the SDK must be treated as the singular, authoritative source for all checkout state transitions. Developers should understand that interacting with the service abstracts away the underlying complexity of tokenization, payment gateway initialization, and error handling, making the process of building a bespoke checkout predictable and robust.

## **II. Documentation Structure Blueprint**

The documentation suite for bigcommerce/checkout-sdk-js is structured into ten distinct files across five categories, ensuring comprehensive coverage from initial setup to advanced API referencing.

Table: Documentation File Blueprint

| File Path | Category | Primary Focus & Required Content |
| :---- | :---- | :---- |
| docs/00\_Getting\_Started.md | Setup | Installation (NPM/CDN), createCheckoutService initialization, deployment methods (WebDAV). |
| docs/01\_Core\_Concepts/01\_State\_Management.md | Core Concepts | CheckoutState structure, immutability principle, service.subscribe(), service.getState(). |
| docs/01\_Core\_Concepts/02\_Error\_Handling.md | Core Concepts | Asynchronous error patterns, specific error getters (getLoadCheckoutError), finalizeOrderIfNeeded catch block. |
| docs/02\_Architectural\_Overview.md | Architecture | Monorepo structure (Nx confirmation), /packages purpose, build/distribution modules (checkout-sdk, checkout-button). |
| docs/03\_Golden\_Path\_Tutorials/01\_Authentication.md | Tutorials | Customer sign-in, guest flow (continueAsGuest), and passwordless sign-in. |
| docs/03\_Golden\_Path\_Tutorials/02\_Address\_Selection.md | Tutorials | updateShippingAddress, required payload details, handling shipping option recalculation, selectShippingOption. |
| docs/03\_Golden\_Path\_Tutorials/03\_Discounts\_And\_Promotions.md | Tutorials | applyCoupon, applyGiftCertificate, and the essential counterpart methods: removeCoupon, removeGiftCertificate. |
| docs/03\_Golden\_Path\_Tutorials/04\_Payment\_And\_Order.md | Tutorials | loadPaymentMethods, initializePayment, submitOrder, and external flow finalization (finalizeOrderIfNeeded). |
| docs/04\_API\_Reference/CheckoutService.md | API Reference | Formal, exhaustive documentation for all public methods of the CheckoutService interface. |
| docs/05\_Typescript\_Definitions.md | Reference | Key TypeScript interface definitions (CheckoutState, Address, PaymentMethod). |

## **III. Content Specification: docs/00\_Getting\_Started.md**

### **A. Installation and Setup**

Developers can integrate the Checkout SDK using two primary methods, depending on their build environment.

The standard integration for projects utilizing module bundlers (such as Webpack or Rollup) is through the Node Package Manager (NPM). This approach is necessary for modern front-end frameworks:

Bash

npm install \--save @bigcommerce/checkout-sdk

Once installed, developers using TypeScript should import the primary service factory function:

TypeScript

import { createCheckoutService } from '@bigcommerce/checkout-sdk';

For most custom theme deployments, particularly those using Stencil, the recommended strategy involves using the asynchronous CDN loader.1 This method provides a crucial operational advantage: the application automatically receives backward-compatible updates and bug fixes without requiring the merchant to manually perform an upgrade or update the deployed artifact.1

The CDN loader script must be included in the theme's HTML page, typically in templates/pages/checkout.html 2:

HTML

\<script src\="https://checkout-sdk.bigcommerce.com/v1/loader.js"\>\</script\>

### **B. Initialization Flow**

Regardless of the installation method, the core initialization process involves loading the module, creating the service, and loading the initial checkout data using the current cart ID. This process is inherently asynchronous.2

The following boilerplate demonstrates the core steps using the CDN loader pattern 1:

JavaScript

// 1\. Ensure the script tag is loaded:  
// \<script src="https://checkout-sdk.bigcommerce.com/v1/loader.js"\>\</script\>

const initSdk \= async (checkoutId) \=\> {  
  // 2\. Load the main 'checkout-sdk' module from the loader instance  
  // The 'checkout-sdk' module contains all public exports.  
  const module \= await checkoutKitLoader.load('checkout-sdk');

  // 3\. Create the core service instance  
  const service \= module.createCheckoutService();

  // 4\. Load the checkout data, initializing the central state  
  const state \= await service.loadCheckout(checkoutId);

  console.log('Initial Checkout State:', state.data.getCheckout());  
  return service;  
};

// Example Usage (using data injected by the BigCommerce template context):  
// initSdk(window.checkoutConfig.checkoutId);

### **C. Deployment Requirements (Custom Checkout)**

When deploying a bespoke checkout built using the SDK, the compiled JavaScript module must be served securely. The standard procedure involves using WebDAV and the BigCommerce Control Panel configuration.3

1. **File Upload:** The bundled custom JavaScript file (e.g., checkout-loader.js or main.js 4) must be uploaded via WebDAV into the store's remote /content/checkout directory.3  
2. **Control Panel Configuration:** In the Control Panel, under Advanced Settings \> Checkout, the developer must select "Custom Checkout" and provide the Script URL.3  
3. **Caching Mitigation:** It is mandatory to prepend the URL with webdav: and include a unique version number (e.g., \<version\>) in the filename.3 This is crucial for cache invalidation; if the same filename is used repeatedly, users may be served a cached, stale version of the checkout code, leading to runtime errors.3 The recommended format is webdav:checkout/checkout-loader-\<version\>.js.3

## **IV. Content Specification: docs/02\_Architectural\_Overview.md**

### **A. Monorepo Rationale and Structure**

The bigcommerce/checkout-sdk-js project is organized as a monorepo, a structure that allows multiple individual packages (or libraries) to reside within a single repository.1 This architecture is managed primarily by the Nx build system, as evidenced by configuration files such as nx.json, workspace.json, and monorepo-specific changes noted in jest.config.js.1 The utilization of a monorepo facilitates sharing common tooling, enforcing unified coding standards (TypeScript configuration, linting rules), and managing the complex dependency graph between the core SDK and the numerous payment integration packages it supports. This setup ensures stability and consistency across all component libraries.

### **B. The /packages Directory**

The /packages directory forms the core workspace of the monorepo. This directory contains the source code for all individual packages that are ultimately bundled together to form the complete Checkout SDK.1 This includes the core logic, state management utilities, and all vendor-specific payment integrations (e.g., Braintree, PayPal, Amazon). By isolating these components into individual packages, the development team can manage feature additions, testing, and dependency updates for complex integrations without affecting the stability of unrelated modules.

### **C. Distribution Modules**

While the source code is split into many packages, the compiled SDK is distributed as several distinct modules accessible via the checkoutKitLoader.load() method.1 Developers must specify the required module name during initialization:

Table: SDK Distribution Modules

| Module Name | Purpose | Loading Example |
| :---- | :---- | :---- |
| checkout-sdk | The comprehensive primary module. It exports the createCheckoutService and all related interfaces and functions for building a full custom checkout flow. | await checkoutKitLoader.load('checkout-sdk') |
| checkout-button | A smaller, specialized sub-module used specifically for initializing checkout buttons (ee.g., PayPal buttons) typically placed on the storefront or cart pages. | await checkoutKitLoader.load('checkout-button') |
| embedded- | (A partial name suggesting functionality) This module is related to specific embedded contexts or messenger services, likely for communication when the checkout is displayed within an iframe or similar restricted environment. | N/A (Consult latest source for full name) |

## **V. Content Specification: docs/01\_Core\_Concepts/\*.md**

### **A. 01\_State\_Management.md: Immutability and State Subscription**

The CheckoutService maintains a single source of truth for all checkout data through the CheckoutState model.1 This model includes critical information such as cart contents, customer data, shipping consignments, and error flags. A fundamental principle governing the SDK is the immutability of the state: any action performed on the service (e.g., updating an address, applying a coupon) is asynchronous and results in a *new*, immutable snapshot of the CheckoutState being generated and returned.1 Developers are strictly advised against attempting to mutate the state object directly, as this will lead to unpredictable behavior and data inconsistencies.

For developers building reactive user interfaces, state changes can be monitored via the service.subscribe method, which registers a listener function to be executed whenever the state is updated.

TypeScript

import { createCheckoutService, CheckoutState } from '@bigcommerce/checkout-sdk';

// Assume 'service' is initialized  
const unsubscribe \= service.subscribe((state: CheckoutState) \=\> {  
  // Listener receives a new, immutable snapshot of the state  
  const checkout \= state.data.getCheckout();  
  console.log('State updated. New total:', checkout.grandTotal);

  // Example: Safely accessing persistent errors  
  const shippingErrors \= state.errors.getUpdateShippingAddressError();  
  if (shippingErrors) {  
    console.error('Shipping update failed:', shippingErrors);  
  }  
});

// To prevent memory leaks, call the function returned by subscribe when the component unmounts  
// unsubscribe();

If synchronous access to the current state is necessary, the service.getState() method can be called, which instantly returns the most recent CheckoutState object.

### **B. 01\_Core\_Concepts/02\_Error\_Handling.md: Asynchronous Failures**

All public methods on the CheckoutService are asynchronous and return Promises. Therefore, developers must wrap method calls in standard try...catch blocks to handle immediate execution failures, such as network timeouts or API rejections.

In addition to standard promise rejection, the SDK stores critical validation and system errors persistently within the CheckoutState object itself, accessible via error getter methods. This pattern allows the UI to display relevant warnings or messages even after the initial promise has resolved or if a persistent error, such as a load failure, needs to be communicated.

TypeScript

try {  
  await service.loadCheckout(checkoutId);  
} catch (error) {  
  // Handle the generic promise rejection (e.g., network error)  
  console.error('Initial load failed due to system error:', error);  
    
  // Also check state for specific, persistent errors  
  const loadError \= service.getState().errors.getLoadCheckoutError();  
  if (loadError) {  
    // Display user-friendly message based on specific error payload  
    renderUserAlert(loadError.message);  
  }  
}

The handling of hosted payment redirects requires a specialized error pattern involving finalizeOrderIfNeeded, which is detailed further in the payment tutorial section.

## **VI. Content Specification: docs/03\_Golden\_Path\_Tutorials/\*.md**

### **A. 01\_Authentication.md: Guest and Customer Flows**

The SDK supports multiple methods for customer identification at the start of the checkout process. In addition to signing in a known customer (which typically requires a credentials payload), the SDK provides a flow for allowing customers to proceed without creating an account.

The continueAsGuest method facilitates this flow. Documentation review indicated that a complete, explicit code example for this critical method was not readily available in public documentation and is therefore synthesized here.1 The method requires the customer's email address and a flag confirming the guest flow intention.

TypeScript

import { GuestCredentials } from '@bigcommerce/checkout-sdk';

// Required payload structure  
const guestData: GuestCredentials \= {  
  email: 'guest.customer@example.com',  
  requiresSignIn: false, // Ensures the guest flow is used  
};

try {  
  const state \= await service.continueAsGuest(guestData);  
  console.log('Guest customer set. Current customer details:', state.data.getCustomer());  
} catch (error) {  
  console.error('Failed to continue as guest:', error);  
  // Handle server-side errors, such as invalid email format or required sign-in status conflicts.  
}

### **B. 02\_Address\_Selection.md: Shipping and Options**

Setting the shipping address is a multi-step, asynchronous process critical to determining costs and available options.

1. **Updating Shipping Address (updateShippingAddress)**: This method takes a complete Address object (including required fields such as countryCode, postalCode, and stateOrProvinceCode) and attempts to update the checkout object. Upon success, this action triggers the calculation of available shipping options based on the new location.  
2. **Selecting Shipping Option (selectShippingOption)**: A key point of complexity for developers is ensuring they correctly identify and use the available shipping options post-address update. The output of updateShippingAddress does not explicitly return the required option ID; instead, developers must inspect the *new state* object returned by the promise, locate the newly created shipment object (consignment), and parse the availableShippingOptions array to find the correct optionId.5 Assuming this data is safely extracted, the selectShippingOption method is called using the calculated shipment.id and the chosen optionId.5

TypeScript

// 1\. Define the address  
const newAddress \= {   
  address1: '123 Commerce St',   
  city: 'Austin',   
  postalCode: '78701',   
  countryCode: 'US',   
  stateOrProvinceCode: 'TX'   
};  
const stateWithAddress \= await service.updateShippingAddress(newAddress);

// 2\. Safely extract Shipment ID and Option ID from the new state  
const shipment \= stateWithAddress.data.getConsignments();

// The system requires a shipping option ID and a shipment ID  
if (\!shipment |

| shipment.availableShippingOptions.length \=== 0) {  
    throw new Error('No shipments or shipping options available.');  
}  
const firstAvailableOptionId \= shipment.availableShippingOptions.id;

// 3\. Select the option  
const stateWithShipping \= await service.selectShippingOption(  
  shipment.id,  
  firstAvailableOptionId  
);  
console.log('Shipping cost applied. New total:', stateWithShipping.data.getCheckout().grandTotal);

### **C. 03\_Discounts\_And\_Promotions.md: Applying and Removing**

The SDK provides direct methods for managing discounts and promotions. The application of coupons and gift certificates immediately recalculates the checkout totals.

Applying a promotion is straightforward 1:

TypeScript

// Apply a coupon  
const state \= await service.applyCoupon('coupon-code');

// or

// Apply a gift certificate  
const state \= await service.applyGiftCertificate('certificate-code');

Equally important for a complete UI experience are the methods to remove applied promotions. While examples for removal were noted as missing from some documentation sources, the functionality is essential and follows a predictable SDK pattern.

TypeScript

// Removing an applied coupon  
const state \= await service.removeCoupon('coupon-code');

// Removing an applied gift certificate  
const state \= await service.removeGiftCertificate('certificate-code');  
console.log('Discounts removed. Grand total reverted.');

### **D. 04\_Payment\_And\_Order.md: Submission and Finalization**

The final stages of checkout involve loading available payment methods, initializing the chosen method, and submitting the order.

1. **Load Payment Methods (loadPaymentMethods)**: This retrieves all payment methods configured on the merchant's store, including standard gateways and offline methods such as Cash on Delivery (COD).6  
2. **Initialize Payment (initializePayment)**: This method prepares the environment for payment. For complex integrated gateways, this may involve rendering secure input fields or widgets. For offline methods, it simply confirms the method selection.6  
   TypeScript  
   // Example: Initializing an offline payment method (COD)  
   await service.initializePayment({ methodId: 'cod' });

3. **Submit Order (submitOrder)**: This function executes the transaction, finalizing the order object in the BigCommerce system and sending the necessary payment details (or simply the confirmation of an offline method) to the processor.6  
4. **Handling External Payment Redirection (finalizeOrderIfNeeded)**: This is a critical flow for hosted payment methods or those requiring external validation (like 3D Secure).8 When a customer is redirected off-site to complete payment and then returns to the checkout page, this method must be called immediately upon page load, *before* rendering the UI. Its purpose is to complete the final confirmation handshake and transition the pending order status to confirmed.

The finalizeOrderIfNeeded method uses a specialized error pattern: if finalization is not required (i.e., the payment was handled internally or the customer is on the first load of the page), the function throws a specific error type (order\_finalization\_not\_required). Developers must explicitly check for and ignore this non-fatal error to proceed with rendering the standard checkout UI.8

TypeScript

// Execute immediately upon page load, before UI rendering:  
await service.loadCheckout();   
try {  
  await service.finalizeOrderIfNeeded();  
  // If successful, the order is complete. Redirect to confirmation page.  
  window.location.assign('/order-confirmation');  
} catch (error) {  
  if (error.type \=== 'order\_finalization\_not\_required') {  
    // This is the expected non-fatal response. Continue to render the checkout UI.  
    console.log('Order finalization not required. Rendering checkout.');  
  } else {  
    // A genuine error occurred during finalization (e.g., payment failure on redirect)  
    console.error('Critical finalization error:', error);  
    throw error;  
  }  
}

## **VII. Content Specification: docs/04\_API\_Reference/CheckoutService.md**

This document provides the formal interface reference for all public methods exposed by the CheckoutService. Each entry details the purpose, TypeScript signature, parameters, and expected return type, which, for all transactional methods, is a Promise resolving to the current CheckoutState.

Table: CheckoutService Core Public Methods

| Method Name | TypeScript Signature | Description |
| :---- | :---- | :---- |
| loadCheckout | (id: string) \=\> Promise\<CheckoutState\> | Retrieves and initializes the checkout state using the provided cart/checkout ID. |
| getState | () \=\> CheckoutState | Returns the current immutable state object synchronously for immediate data access. |
| subscribe | (listener: (state: CheckoutState) \=\> void) \=\> () \=\> void | Registers a listener function to be called on any state change. Returns a function to unsubscribe the listener. |
| updateShippingAddress | (address: Address) \=\> Promise\<CheckoutState\> | Sets the shipping address, triggering recalculation of available shipping options. |
| selectShippingOption | (shipmentId: string, optionId: string) \=\> Promise\<CheckoutState\> | Applies the chosen shipping method, updating total price and consignment details. |
| applyCoupon | (code: string) \=\> Promise\<CheckoutState\> | Attempts to apply a coupon code to the checkout totals. |
| removeCoupon | (code: string) \=\> Promise\<CheckoutState\> | Removes a previously applied coupon code from the checkout. |
| continueAsGuest | (credentials: GuestCredentials) \=\> Promise\<CheckoutState\> | Sets the customer as a guest using an email address, bypassing the sign-in requirement. |
| initializePayment | (options: PaymentInitializeOptions) \=\> Promise\<CheckoutState\> | Initializes a specific payment method, preparing it for transaction submission. |
| submitOrder | (options?: OrderSubmitOptions) \=\> Promise\<CheckoutState\> | Submits the order and processes the payment using the initialized method. |
| finalizeOrderIfNeeded | () \=\> Promise\<CheckoutState\> | Completes order finalization after a customer returns from an external payment redirect. Throws a specific non-fatal error if not required.8 |
| cancelRequests | () \=\> void | Aborts any outstanding API or external SDK requests initiated by the service, useful for cleanup or navigation between views. |

## **VIII. Content Specification: docs/05\_Typescript\_Definitions.md**

Providing comprehensive TypeScript interface definitions is vital for developers leveraging the SDK within statically typed environments. This reference file must define the expected shapes of the core data objects, ensuring robust type checking and improved developer experience through autocompletion.

Key interfaces to be defined include:

1. **CheckoutState**: The root interface encompassing the entire state tree. It must detail the three primary branches: data (the current checkout entity and related items), statuses (loading and transaction status flags), and errors (persistent error states, including specific getters like getLoadCheckoutError).  
2. **Checkout**: The core entity object residing within state.data. This interface defines critical properties such as grandTotal, subtotal, items, customer, and consignments.  
3. **Address**: Defines the required payload structure for geographic and contact information used in updateShippingAddress and setting the billing address (e.g., address1, city, postalCode, countryCode, stateOrProvinceCode).  
4. **GuestCredentials**: Defines the minimal input required for continueAsGuest, specifically the email string and the requiresSignIn boolean flag.  
5. **PaymentMethod**: Defines the structure of objects returned by loadPaymentMethods, detailing the methodId, display name, and required configuration for initialization.

## **IX. Ancillary Documentation**

### **A. CONTRIBUTING.md (Contribution Guidelines)**

For external collaborators wishing to contribute, the project's contribution guidelines must outline the internal development environment. The direct link to the contribution file is /bigcommerce/checkout-sdk-js/blob/master/CONTRIBUTING.md.1 This file must confirm that the repository utilizes Node v18 3, leverages Nx commands for monorepo maintenance (linting, building, and testing specific packages) 1, and likely adheres to a conventional commit message standard, which is supported by the commit-validation.json file found in the repository.3 The document should also provide a brief overview of the required Pull Request process.

### **B. FAQS.md (Frequently Asked Questions)**

The FAQ documentation should address common developer challenges encountered when implementing custom checkouts using the SDK:

* **Q: Why are my shipping options not updating after setting the address?** A: Ensure that the updateShippingAddress action successfully resolved and that the application code is correctly parsing the resultant state object to retrieve the availableShippingOptions from the updated consignment object before calling selectShippingOption.  
* **Q: I am using a hosted payment provider. How do I complete the order when the customer redirects back to my checkout page?** A: The critical solution is to use await service.finalizeOrderIfNeeded() immediately upon page load.8 This function handles the final confirmation with the external provider. Remember to specifically catch and ignore the order\_finalization\_not\_required error type.  
* **Q: We use offline payment methods, such as Cash on Delivery. How do I process these?** A: Offline methods are retrieved via loadPaymentMethods.6 They require initialization via initializePayment({ methodId: 'cod' }) and subsequent submission using submitOrder({ methodId: 'cod' }).6  
* **Q: My updates are not appearing on the store. Why is the checkout still showing old code?** A: This is typically a client-side caching issue. When configuring the Custom Checkout Script URL in the Control Panel, ensure a unique version identifier is appended to the filename (e.g., checkout-loader-1.0.1.js) to force the user's browser to download the latest file.3

## **X. Conclusion**

The BigCommerce Checkout JavaScript SDK is an essential tool for creating high-performance, custom storefront checkout experiences. The comprehensive documentation suite outlined here addresses the structural complexity of the underlying monorepo architecture and provides explicit guidance for developers navigating the asynchronous, state-driven nature of the service. By clearly detailing the principles of state immutability, providing complete TypeScript examples for common flows (such as guest authentication and shipping option selection), and clarifying the critical necessity of finalizeOrderIfNeeded for hosted payments, this documentation ensures that developers can build robust, reliable, and unique checkout pages while benefiting from the operational stability provided by the SDK's abstraction of multi-vendor payment integrations.

#### **Источники**

1. bigcommerce/checkout-sdk-js \- GitHub, дата последнего обращения: октября 22, 2025, [https://github.com/bigcommerce/checkout-sdk-js](https://github.com/bigcommerce/checkout-sdk-js)  
2. Getting Started with Checkout SDK \- BigCommerce Dev Center, дата последнего обращения: октября 22, 2025, [https://developer.bigcommerce.com/docs/storefront/cart-checkout/checkout-sdk](https://developer.bigcommerce.com/docs/storefront/cart-checkout/checkout-sdk)  
3. Checkout SDK Tutorial | BigCommerce Dev Center, дата последнего обращения: октября 22, 2025, [https://developer.bigcommerce.com/docs/storefront/cart-checkout/checkout-sdk/tutorial](https://developer.bigcommerce.com/docs/storefront/cart-checkout/checkout-sdk/tutorial)  
4. @bigcommerce/checkout-sdk-example \- Codesandbox, дата последнего обращения: октября 22, 2025, [https://codesandbox.io/s/bigcommerce-checkout-sdk-example-9l1y17nj1o](https://codesandbox.io/s/bigcommerce-checkout-sdk-example-9l1y17nj1o)  
5. bigcommerce single page checkout not working in checkout sdk \- Stack Overflow, дата последнего обращения: октября 22, 2025, [https://stackoverflow.com/questions/53095468/bigcommerce-single-page-checkout-not-working-in-checkout-sdk](https://stackoverflow.com/questions/53095468/bigcommerce-single-page-checkout-not-working-in-checkout-sdk)  
6. Checkout SDK Cash on Delivery Method \- BigCommerce Support, дата последнего обращения: октября 22, 2025, [https://support.bigcommerce.com/s/question/0D54O00007J2TDWSA3/checkout-sdk-cash-on-delivery-method?language=en\_US](https://support.bigcommerce.com/s/question/0D54O00007J2TDWSA3/checkout-sdk-cash-on-delivery-method?language=en_US)  
7. Initialize the Hosted Checkout SDK \- CommerceHub \- Fiserv Developer Studio, дата последнего обращения: октября 22, 2025, [https://developer.fiserv.com/product/CommerceHub/docs/?path=docs/Online-Mobile-Digital/Hosted-Checkout/SDK-Components/Checkout-Component-Initialize.md](https://developer.fiserv.com/product/CommerceHub/docs/?path=docs/Online-Mobile-Digital/Hosted-Checkout/SDK-Components/Checkout-Component-Initialize.md)  
8. Custom Checkout SDK Hosted Payment not working \- BigCommerce Help Center, дата последнего обращения: октября 22, 2025, [https://support.bigcommerce.com/s/question/0D51B000056SecsSAC/custom-checkout-sdk-hosted-payment-not-working?language=en\_US](https://support.bigcommerce.com/s/question/0D51B000056SecsSAC/custom-checkout-sdk-hosted-payment-not-working?language=en_US)