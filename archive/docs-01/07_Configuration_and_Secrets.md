# Configuration and Secrets

## Overview
This document provides a comprehensive guide to all configuration variables, environment settings, and secrets used in the BigCommerce POA React checkout project. Proper configuration is critical for security, functionality, and deployment success.

## Environment Variables

### Core Application Configuration

#### Node.js Environment
```bash
# Application Environment
NODE_ENV=development|production|test
```
- **Purpose**: Determines application behavior and optimization level
- **Values**: 
  - `development`: Enables development features, debugging, hot reload
  - `production`: Optimized for performance, minimal logging
  - `test`: Testing environment with mocked services
- **Usage**: Used throughout the application for conditional behavior

#### Development Settings
```bash
# Development Configuration
MEASURE_SPEED=false|true
WEBPACK_DONE=command_to_execute_after_build
PORT=8080
MODE=replay|record
```
- **MEASURE_SPEED**: Enables webpack build performance measurement
- **WEBPACK_DONE**: Command to execute after webpack build completes
- **PORT**: Development server port (default: 8080)
- **MODE**: Test recording mode for Polly.js

### BigCommerce Configuration

#### Store Configuration
```bash
# BigCommerce Store Settings
BIGCOMMERCE_STORE_HASH=your_store_hash
BIGCOMMERCE_ACCESS_TOKEN=your_access_token
BIGCOMMERCE_CLIENT_ID=your_client_id
BIGCOMMERCE_CLIENT_SECRET=your_client_secret
STORE_URL=https://your-store.mybigcommerce.com
STOREURL=https://your-store.mybigcommerce.com
```
- **STORE_HASH**: Unique identifier for your BigCommerce store
- **ACCESS_TOKEN**: API access token for BigCommerce API calls
- **CLIENT_ID**: OAuth client identifier
- **CLIENT_SECRET**: OAuth client secret
- **STORE_URL**: Your BigCommerce store URL

### Payment Gateway Configuration

#### PayPal Configuration
```bash
# PayPal Commerce
PAYPAL_CLIENT_ID=your_paypal_client_id
PAYPAL_CLIENT_SECRET=your_paypal_client_secret
PAYPAL_MERCHANT_ID=your_paypal_merchant_id
PAYPAL_ENVIRONMENT=sandbox|live
```

#### Stripe Configuration
```bash
# Stripe
STRIPE_PUBLISHABLE_KEY=pk_test_...|pk_live_...
STRIPE_SECRET_KEY=sk_test_...|sk_live_...
STRIPE_WEBHOOK_SECRET=whsec_...
```

#### Adyen Configuration
```bash
# Adyen
ADYEN_CLIENT_KEY=your_adyen_client_key
ADYEN_MERCHANT_ACCOUNT=your_merchant_account
ADYEN_ENVIRONMENT=test|live
```

#### Other Payment Methods
```bash
# Braintree
BRAINTREE_CLIENT_TOKEN=your_braintree_client_token
BRAINTREE_MERCHANT_ID=your_braintree_merchant_id

# Square
SQUARE_APPLICATION_ID=your_square_app_id
SQUARE_LOCATION_ID=your_square_location_id

# Klarna
KLARNA_CLIENT_ID=your_klarna_client_id
KLARNA_CLIENT_SECRET=your_klarna_client_secret
```

### Analytics and Monitoring

#### Sentry Configuration
```bash
# Sentry Error Tracking
SENTRY_DSN=https://your-sentry-dsn@sentry.io/project-id
SENTRY_ENVIRONMENT=development|staging|production
SENTRY_RELEASE=1.0.0
```

#### Google Analytics
```bash
# Google Analytics
GOOGLE_ANALYTICS_ID=GA_MEASUREMENT_ID
GOOGLE_TAG_MANAGER_ID=GTM-XXXXXXX
```

#### Other Analytics
```bash
# Facebook Pixel
FACEBOOK_PIXEL_ID=your_facebook_pixel_id

# Hotjar
HOTJAR_ID=your_hotjar_id
```

### Testing Configuration

#### Test Environment
```bash
# Testing Settings
IS_CI=true|false
NODE_TLS_REJECT_UNAUTHORIZED=0
```
- **IS_CI**: Indicates if running in CI environment
- **NODE_TLS_REJECT_UNAUTHORIZED**: Disables SSL verification for testing

#### E2E Testing
```bash
# E2E Test Configuration
PLAYWRIGHT_BASE_URL=http://localhost:8080
PLAYWRIGHT_TIMEOUT=30000
```

### Build and Deployment

#### Build Configuration
```bash
# Build Settings
PRERELEASE=prerelease|false
WEBPACK_ANALYZE=true|false
BUNDLE_ANALYZER=true|false
```

#### Performance Monitoring
```bash
# Performance Settings
LIGHTHOUSE_ENABLED=true|false
LIGHTHOUSE_PORT=9222
```

## Configuration Files

### Webpack Configuration
```javascript
// webpack.config.js
module.exports = {
    mode: process.env.NODE_ENV || 'development',
    devServer: {
        port: process.env.PORT || 8080,
    },
    plugins: [
        new webpack.DefinePlugin({
            'process.env.NODE_ENV': JSON.stringify(process.env.NODE_ENV),
            'process.env.MEASURE_SPEED': JSON.stringify(process.env.MEASURE_SPEED),
        })
    ]
};
```

### Jest Configuration
```javascript
// jest.config.js
module.exports = {
    setupFilesAfterEnv: ['<rootDir>/jest-setup.ts'],
    testEnvironment: 'jsdom',
    collectCoverageFrom: [
        'src/**/*.{ts,tsx}',
        '!src/**/*.d.ts',
        '!src/**/*.test.{ts,tsx}'
    ]
};
```

### TypeScript Configuration
```json
// tsconfig.json
{
    "compilerOptions": {
        "target": "ES2020",
        "module": "ESNext",
        "moduleResolution": "node",
        "strict": true,
        "esModuleInterop": true,
        "skipLibCheck": true,
        "forceConsistentCasingInFileNames": true
    }
}
```

## Security Best Practices

### Environment Variable Security

#### 1. Never Commit Secrets
```bash
# .gitignore
.env
.env.local
.env.production
.env.staging
*.pem
*.key
```

#### 2. Use Environment-Specific Files
```bash
# Development
.env.development

# Staging
.env.staging

# Production
.env.production
```

#### 3. Validate Required Variables
```typescript
// config/validate.ts
const requiredEnvVars = [
    'BIGCOMMERCE_STORE_HASH',
    'BIGCOMMERCE_ACCESS_TOKEN',
    'NODE_ENV'
];

export function validateEnvironment(): void {
    const missing = requiredEnvVars.filter(varName => !process.env[varName]);
    
    if (missing.length > 0) {
        throw new Error(`Missing required environment variables: ${missing.join(', ')}`);
    }
}
```

### Secret Management

#### 1. Use Secret Management Services
```bash
# AWS Secrets Manager
aws secretsmanager get-secret-value --secret-id checkout-secrets

# Azure Key Vault
az keyvault secret show --vault-name checkout-vault --name bigcommerce-token

# Google Secret Manager
gcloud secrets versions access latest --secret="checkout-secrets"
```

#### 2. Rotate Secrets Regularly
```bash
# Example secret rotation script
#!/bin/bash
# rotate-secrets.sh

# Generate new access token
NEW_TOKEN=$(curl -X POST "https://api.bigcommerce.com/stores/$STORE_HASH/v3/storefront/api-token" \
  -H "X-Auth-Token: $BIGCOMMERCE_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"type": "storefront"}')

# Update secret in management service
aws secretsmanager update-secret \
  --secret-id checkout-secrets \
  --secret-string "{\"BIGCOMMERCE_ACCESS_TOKEN\":\"$NEW_TOKEN\"}"
```

## Environment-Specific Configurations

### Development Environment
```bash
# .env.development
NODE_ENV=development
MEASURE_SPEED=true
PORT=8080
BIGCOMMERCE_STORE_HASH=dev_store_hash
BIGCOMMERCE_ACCESS_TOKEN=dev_access_token
PAYPAL_ENVIRONMENT=sandbox
STRIPE_PUBLISHABLE_KEY=pk_test_...
SENTRY_ENVIRONMENT=development
```

### Staging Environment
```bash
# .env.staging
NODE_ENV=production
PORT=8080
BIGCOMMERCE_STORE_HASH=staging_store_hash
BIGCOMMERCE_ACCESS_TOKEN=staging_access_token
PAYPAL_ENVIRONMENT=sandbox
STRIPE_PUBLISHABLE_KEY=pk_test_...
SENTRY_ENVIRONMENT=staging
```

### Production Environment
```bash
# .env.production
NODE_ENV=production
BIGCOMMERCE_STORE_HASH=prod_store_hash
BIGCOMMERCE_ACCESS_TOKEN=prod_access_token
PAYPAL_ENVIRONMENT=live
STRIPE_PUBLISHABLE_KEY=pk_live_...
SENTRY_ENVIRONMENT=production
```

## Configuration Validation

### Runtime Validation
```typescript
// config/validator.ts
import Joi from 'joi';

const configSchema = Joi.object({
    NODE_ENV: Joi.string().valid('development', 'production', 'test').required(),
    BIGCOMMERCE_STORE_HASH: Joi.string().required(),
    BIGCOMMERCE_ACCESS_TOKEN: Joi.string().required(),
    PORT: Joi.number().port().default(8080),
    PAYPAL_CLIENT_ID: Joi.string().when('PAYPAL_ENVIRONMENT', {
        is: 'live',
        then: Joi.required(),
        otherwise: Joi.optional()
    })
});

export function validateConfig(): void {
    const { error } = configSchema.validate(process.env);
    
    if (error) {
        throw new Error(`Configuration validation failed: ${error.message}`);
    }
}
```

### Build-Time Validation
```json
// package.json
{
    "scripts": {
        "validate:config": "ts-node scripts/validate-config.ts",
        "prebuild": "npm run validate:config"
    }
}
```

## Troubleshooting

### Common Issues

#### 1. Missing Environment Variables
```bash
# Error: Missing required environment variables
# Solution: Check .env file and ensure all required variables are set
```

#### 2. Invalid BigCommerce Credentials
```bash
# Error: 401 Unauthorized
# Solution: Verify BIGCOMMERCE_ACCESS_TOKEN and BIGCOMMERCE_STORE_HASH
```

#### 3. Payment Gateway Configuration
```bash
# Error: Payment method not available
# Solution: Verify payment gateway credentials and environment settings
```

### Debug Configuration
```typescript
// config/debug.ts
export function debugConfiguration(): void {
    if (process.env.NODE_ENV === 'development') {
        console.log('Environment Variables:');
        console.log('- NODE_ENV:', process.env.NODE_ENV);
        console.log('- BIGCOMMERCE_STORE_HASH:', process.env.BIGCOMMERCE_STORE_HASH ? '***' : 'NOT SET');
        console.log('- PAYPAL_ENVIRONMENT:', process.env.PAYPAL_ENVIRONMENT);
        console.log('- SENTRY_ENVIRONMENT:', process.env.SENTRY_ENVIRONMENT);
    }
}
```

## Conclusion

Proper configuration management is essential for the security, functionality, and maintainability of the BigCommerce POA React checkout project. Follow these guidelines to ensure your application is properly configured and secure.

Remember to:
- Never commit secrets to version control
- Use environment-specific configuration files
- Validate configuration at runtime
- Rotate secrets regularly
- Monitor for configuration-related errors
