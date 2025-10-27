# Developer Quick Start

This guide provides the essential steps to get the BigCommerce React Checkout running locally.

## 1. Prerequisites
- **Node.js**: `v16.x`
- **npm**: `v8.x`
- **Git**

## 2. Initial Setup

### Clone and Install
```bash
# 1. Clone the repository
git clone https://github.com/bigcommerce/checkout-js.git
cd checkout-js

# 2. Install dependencies
npm install
```

### Environment Variables
Create a `.env` file in the project root. You will need to populate it with valid credentials from a BigCommerce store.

```
# Minimal required variables for local development
BIGCOMMERCE_STORE_HASH="your_store_hash"
BIGCOMMERCE_ACCESS_TOKEN="your_access_token"
```

## 3. Core Development Tasks

### Running the Development Server
This command starts a local server with hot-reloading enabled.

```bash
npm run dev
```
The application will be available at `http://localhost:8080`.

### Running Tests
This command runs the entire test suite (unit and integration).

```bash
npm run test
```

### Running Linter
This command checks the code for style and formatting issues.
```bash
npm run lint
```

## 4. Key Project Structure
- **`packages/core/src/app/`**: Main application components, organized by feature (checkout, billing, shipping, etc.). This is where most UI development happens.
- **`packages/ui/`**: The shared, reusable component library.
- **`packages/payment-integrations/`**: Individual packages for each payment provider.
- **`packages/locale/`**: Contains all translation files (`en.json`, etc.).

## 5. First Steps for a New Developer
1.  **Run the App:** Follow the setup steps above to get the application running on your local machine.
2.  **Explore a Golden Path:** Open the `01-submit-guest-order-spec.md` and trace its verification criteria through the codebase. Start with the main `packages/core/src/app/checkout/Checkout.tsx` component.
3.  **Run the Tests:** Execute `npm run test` to see how the application's behavior is verified. Try making a small change in a component and see the relevant test fail.
4.  **Review the UI Kit:** Browse the shared component library in `packages/ui/` to understand the available building blocks.
