# CORS - Unformatted

CORS (Cross-Origin Resource Sharing) is a critical security mechanism that controls how web browsers handle requests between different origins (domains, protocols, or ports). Understanding CORS is essential when building SaaS architectures where multiple applications need to communicate with each other.

## What is CORS?

CORS is a browser security feature that blocks web pages from making requests to a different domain than the one serving the page, unless the target server explicitly allows it. An "origin" consists of the protocol, domain, and port - so `https://app.example.com:443` and `https://api.example.com:443` are different origins.

## CORS in SaaS Architecture Context

In a typical SaaS setup, you might have:

- **Frontend Application** (Angular): `https://app.yoursaas.com`
- **Backend API** (Laravel): `https://api.yoursaas.com`
- **Directus CMS**: `https://cms.yoursaas.com`
- **Admin Dashboard**: `https://admin.yoursaas.com`

Each of these represents a different origin, and CORS policies determine which can communicate with which.

## Directus and CORS Configuration

Directus has sophisticated CORS handling because it serves multiple purposes:

- **Admin Interface**: Web-based admin panel
- **REST API**: For application integrations
- **GraphQL API**: Alternative query interface
- **Asset Management**: File uploads and serving

### Key Directus CORS Settings

Directus uses environment variables for CORS configuration:

**CORS_ENABLED**: Enable/disable CORS entirely **CORS_ORIGIN**: Define allowed origins (can be array or wildcard) **CORS_METHODS**: Allowed HTTP methods **CORS_ALLOWED_HEADERS**: Headers that can be sent **CORS_EXPOSED_HEADERS**: Headers exposed to the client

### Strategic CORS Configuration Approaches

**Development Environment**: Use permissive settings with `CORS_ORIGIN=true` (allows all origins) for rapid development and testing.

**Production Environment**: Implement strict whitelist approach specifying exact domains that need access to your Directus instance.

**Staging Environment**: Mirror production settings to catch CORS issues before deployment.

## API Communication Patterns in Your SaaS Stack

### Frontend to Backend Communication

Your Angular application will make API calls to both your Laravel backend and Directus. Configure Laravel's CORS middleware to allow requests from your frontend domain, and similarly configure Directus to accept requests from the frontend.

### Backend to Backend Communication

Server-to-server communication (Laravel to Directus) doesn't involve browsers, so CORS doesn't apply. Use API tokens and direct HTTP client requests.

### Admin Interface Access

Directus admin interface needs to make API calls to itself, which is same-origin. However, if you're embedding Directus admin in an iframe or accessing it from a different subdomain, CORS policies apply.

## NGINX Proxy Manager Considerations

When using NGINX Proxy Manager for SSL termination and routing, ensure your CORS configuration accounts for the proxy setup:

- Configure upstream servers (Directus, Laravel) to trust the proxy
- Set appropriate headers for origin forwarding
- Handle preflight OPTIONS requests at the proxy level if needed

## Security Best Practices

**Principle of Least Privilege**: Only allow the minimum required origins. Avoid wildcards in production.

**Environment-Specific Configuration**: Use different CORS policies for development, staging, and production environments.

**Header Security**: Be selective about which headers you expose and allow. Include security headers like `X-Content-Type-Options` and `X-Frame-Options`.

**Token-Based Authentication**: Combine CORS with proper API authentication using Bearer tokens or API keys.

## Practical Implementation Strategy

**Phase 1**: Start with permissive CORS settings during initial development to avoid blocking legitimate requests while building integrations.

**Phase 2**: Gradually tighten CORS policies as your architecture solidifies and you identify exact communication patterns.

**Phase 3**: Implement monitoring to catch CORS violations in production, which might indicate either security issues or legitimate requests that need to be allowed.

**Documentation**: Maintain clear documentation of which applications need to communicate with which APIs, as this directly informs your CORS configuration strategy.

The key is balancing security with functionality - CORS should protect against malicious cross-origin requests while enabling your legitimate application integrations to function seamlessly.