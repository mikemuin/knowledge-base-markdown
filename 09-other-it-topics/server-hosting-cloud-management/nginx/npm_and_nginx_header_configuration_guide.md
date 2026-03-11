# NPM and NGINX Header Configuration Guide
## Preventing Header Conflicts in Dual-Proxy Architecture

---

## Table of Contents
1. [Understanding the Problem](#understanding-the-problem)
2. [Header Responsibility Matrix](#header-responsibility-matrix)
3. [NPM Configuration (UI Steps)](#npm-configuration-ui-steps)
4. [Application NGINX Configuration](#application-nginx-configuration)
5. [Laravel Backend Configuration](#laravel-backend-configuration)
6. [Testing and Verification](#testing-and-verification)
7. [Common Issues and Solutions](#common-issues-and-solutions)

---

## Understanding the Problem

When requests flow through two proxies, headers can be:
- **Overwritten**: Second proxy replaces values set by first proxy
- **Lost**: Headers not passed through properly
- **Duplicated**: Both proxies add the same header differently

**Request Flow:**
```
Client (203.0.113.45)
    ↓
DigitalOcean Load Balancer
    ↓
NPM Container (Sets client headers)
    ↓
Application NGINX Container (Adds application headers)
    ↓
Laravel Backend (Reads final headers)
```

**Goal:** Each layer sets specific headers once, and all subsequent layers preserve them.

---

## Header Responsibility Matrix

| Header | NPM Sets | App NGINX | Laravel Reads | Purpose |
|--------|----------|-----------|---------------|---------|
| `X-Real-IP` | ✅ YES | ⚠️ PRESERVE | ✅ YES | Original client IP |
| `X-Forwarded-For` | ✅ YES | ⚠️ PRESERVE | ✅ YES | Proxy chain IPs |
| `X-Forwarded-Proto` | ✅ YES | ⚠️ PRESERVE | ✅ YES | Original protocol (https/http) |
| `X-Forwarded-Host` | ✅ YES | ⚠️ PRESERVE | ✅ YES | Original hostname |
| `Host` | ✅ YES | ⚠️ PRESERVE | ✅ YES | Target hostname |
| `X-Request-ID` | ✅ YES | ⚠️ PRESERVE | ✅ YES | Request tracing ID |
| `X-Tenant-ID` | ❌ NO | ✅ YES | ✅ YES | Extracted subdomain |
| `X-Application-Context` | ❌ NO | ✅ YES | ✅ YES | Module identifier |

**Legend:**
- YES: This layer sets the header
- PRESERVE: This layer passes it through unchanged
- NO: This layer doesn't handle this header

---

## NPM Configuration (UI Steps)

### Step 1: Access NPM Admin Interface

1. Open your browser and navigate to: `http://your-server-ip:81`
2. Log in with your NPM admin credentials
3. You should see the NPM dashboard

### Step 2: Configure a Proxy Host

#### For New Proxy Host:

1. Click **"Hosts"** in the left sidebar
2. Click **"Proxy Hosts"** tab
3. Click **"Add Proxy Host"** button

#### For Existing Proxy Host:

1. Click **"Hosts"** in the left sidebar
2. Click **"Proxy Hosts"** tab
3. Find your proxy host (e.g., `*.hspc.app`)
4. Click the **three dots (⋮)** on the right
5. Click **"Edit"**

### Step 3: Configure Basic Proxy Settings

**Details Tab:**

1. **Domain Names:** Enter your wildcard domain
   ```
   *.hspc.app
   hspc.app
   ```

2. **Scheme:** Select `http` (internal communication)

3. **Forward Hostname/IP:** Enter your frontend NGINX container name
   ```
   frontend-nginx
   ```
(NOT `localhost` or IP address - use Docker service name)

4. **Forward Port:** Enter `80`

5. **Cache Assets:** Check this box (optional, for static assets)

6. **Block Common Exploits:** Check this box (recommended)

7. **Websockets Support:** Check this box (if you use WebSockets)

### Step 4: Configure SSL

**SSL Tab:**

1. **SSL Certificate:** Select your wildcard certificate
   - If not created yet, click **"Request a new SSL Certificate"**
   - Use **Let's Encrypt** with DNS challenge for wildcard

2. **Force SSL:** Check this box (redirect HTTP to HTTPS)

3. **HTTP/2 Support:** Check this box (recommended)

4. **HSTS Enabled:** Check this box (recommended for security)

5. **HSTS Subdomains:** Check this box (applies to all subdomains)

### Step 5: Configure Custom Headers (CRITICAL)

**Custom Locations Tab:**

Click **"Add Location"** and configure as follows:

**Location 1: Default pass-through**

```nginx
location / {
    # Preserve original client IP
    proxy_set_header X-Real-IP $remote_addr;

    # Build forwarded-for chain
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

    # Preserve original protocol (https)
    proxy_set_header X-Forwarded-Proto $scheme;

    # Preserve original hostname
    proxy_set_header X-Forwarded-Host $host;

    # Pass original host header
    proxy_set_header Host $host;

    # Generate and pass request ID for tracing
    proxy_set_header X-Request-ID $request_id;

    # HTTP version for WebSocket support
    proxy_http_version 1.1;

    # WebSocket headers
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection $connection_upgrade;

    # Timeouts
    proxy_connect_timeout 10s;
    proxy_send_timeout 30s;
    proxy_read_timeout 30s;

    # Buffering
    proxy_buffering on;
    proxy_buffer_size 4k;
    proxy_buffers 8 4k;

    # Forward to application NGINX
    proxy_pass http://frontend-nginx:80;
}
```

### Step 6: Advanced Configuration (Optional but Recommended)

**Advanced Tab:**

Add this configuration:

```nginx
# Connection upgrade mapping for WebSockets
map $http_upgrade $connection_upgrade {
    default upgrade;
    '' close;
}

# Client body size (adjust for your needs)
client_max_body_size 20M;

# Security headers
add_header X-Frame-Options "SAMEORIGIN" always;
add_header X-Content-Type-Options "nosniff" always;
add_header X-XSS-Protection "1; mode=block" always;
add_header Referrer-Policy "strict-origin-when-cross-origin" always;

# Remove nginx version from headers
server_tokens off;

# Rate limiting (adjust as needed)
limit_req_zone $binary_remote_addr zone=global_limit:10m rate=1000r/m;
limit_req zone=global_limit burst=100 nodelay;
```

### Step 7: Save Configuration

1. Click **"Save"** button at the bottom
2. NPM will automatically reload the configuration
3. Wait for the green "Successfully saved" message

### Step 8: Verify NPM Configuration

**Check if proxy host is working:**

1. Go to **"Hosts" → "Proxy Hosts"**
2. Look for green **"Online"** status next to your host
3. If red **"Offline"**, check:
   - Frontend NGINX container is running: `docker ps | grep frontend-nginx`
   - Container name matches what you entered
   - Port 80 is exposed in the container

---

## Application NGINX Configuration

### Location 1: Configuration File Location

Your application NGINX configuration is typically in one of these locations:

**For containerized NGINX:**
```bash
# Inside the container
/etc/nginx/nginx.conf          # Main config
/etc/nginx/conf.d/*.conf        # Site configs
/etc/nginx/sites-enabled/*      # Alternative location

# Mounted from host (check your docker-compose.yml)
./nginx/nginx.conf              # Custom mount
./nginx/conf.d/default.conf     # Custom mount
```

**To find your config:**
```bash
# Check docker-compose.yml for volume mounts
cat docker-compose.yml | grep -A5 "frontend-nginx"

# Or enter the container and check
docker exec -it frontend-nginx bash
ls -la /etc/nginx/
cat /etc/nginx/nginx.conf
```

### Location 2: Main Configuration Structure

Create or edit: `/etc/nginx/nginx.conf`

```nginx
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    # IMPORTANT: Custom log format with request ID and tenant
    log_format main '$remote_addr - $remote_user [$time_local] '
                    '"$request" $status $body_bytes_sent '
                    '"$http_referer" "$http_user_agent" '
                    'req_id=$http_x_request_id tenant=$tenant_id '
                    'upstream=$upstream_addr time=$request_time';

    access_log /var/log/nginx/access.log main;

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    server_tokens off;

    # Client body size (must match or exceed NPM setting)
    client_max_body_size 20M;

    # Include site configurations
    include /etc/nginx/conf.d/*.conf;
}
```

### Location 3: Site Configuration File

Create or edit: `/etc/nginx/conf.d/default.conf`

```nginx
# Upstream backend API
upstream backend_api {
    server appointment-api:9000;  # Adjust to your backend container name
    keepalive 32;
}

# Main server block
server {
    listen 80;
    server_name _;  # Accept all hostnames (we're behind NPM)

    root /usr/share/nginx/html;
    index index.html;

    # Extract tenant ID from hostname
    # Format: clinic1.hspc.app → tenant_id = clinic1
    set $tenant_id "";
    if ($http_host ~* "^([^.]+)\.hspc\.app$") {
        set $tenant_id $1;
    }

    # If accessing base domain (hspc.app), set tenant to "main"
    if ($http_host = "hspc.app") {
        set $tenant_id "main";
    }

    # === HEADER PRESERVATION (CRITICAL) ===
    # DO NOT set these headers - they come from NPM
    # Just ensure they're passed to backend

    # These are ALREADY set by NPM, we just preserve them:
    # - X-Real-IP
    # - X-Forwarded-For
    # - X-Forwarded-Proto
    # - X-Forwarded-Host
    # - Host
    # - X-Request-ID

    # === STATIC ASSETS (Angular Frontend) ===
    location / {
        try_files $uri $uri/ /index.html;

        # Cache static assets
        location ~* \.(jpg|jpeg|png|gif|ico|css|js|svg|woff|woff2|ttf|eot)$ {
            expires 1y;
            add_header Cache-Control "public, immutable";
            access_log off;
        }
    }

    # === API ENDPOINTS ===
    location /api/ {
        # PRESERVE headers from NPM (DO NOT OVERWRITE)
        proxy_set_header X-Real-IP $http_x_real_ip;
        proxy_set_header X-Forwarded-For $http_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $http_x_forwarded_proto;
        proxy_set_header X-Forwarded-Host $http_x_forwarded_host;
        proxy_set_header Host $http_host;
        proxy_set_header X-Request-ID $http_x_request_id;

        # ADD application-specific headers
        proxy_set_header X-Tenant-ID $tenant_id;
        proxy_set_header X-Application-Context "appointment-system";

        # WebSocket support (preserve from NPM)
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $http_connection;

        # Timeouts (shorter than NPM)
        proxy_connect_timeout 5s;
        proxy_send_timeout 25s;
        proxy_read_timeout 25s;

        # Buffering
        proxy_buffering on;
        proxy_buffer_size 4k;
        proxy_buffers 8 4k;

        # Forward to backend
        proxy_pass http://backend_api;
    }

    # === HEALTH CHECK ENDPOINT ===
    location /health {
        access_log off;
        add_header Content-Type text/plain;
        return 200 "healthy\n";
    }

    # === ERROR PAGES ===
    error_page 404 /404.html;
    location = /404.html {
        internal;
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        internal;
    }
}
```

### Location 4: Multi-Module Configuration (If Needed)

If you have multiple modules (appointment, portal, directory, admin), create separate config files:

**File: `/etc/nginx/conf.d/appointment.conf`**

```nginx
server {
    listen 80;
    server_name appointment.hspc.app;

    root /usr/share/nginx/html/appointment;

    # Extract tenant if using subdomain: clinic1.appointment.hspc.app
    set $tenant_id "";
    if ($http_host ~* "^([^.]+)\.appointment\.hspc\.app$") {
        set $tenant_id $1;
    }

    location / {
        try_files $uri $uri/ /index.html;
    }

    location /api/ {
        # Preserve NPM headers
        proxy_set_header X-Real-IP $http_x_real_ip;
        proxy_set_header X-Forwarded-For $http_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $http_x_forwarded_proto;
        proxy_set_header X-Forwarded-Host $http_x_forwarded_host;
        proxy_set_header Host $http_host;
        proxy_set_header X-Request-ID $http_x_request_id;

        # Add application headers
        proxy_set_header X-Tenant-ID $tenant_id;
        proxy_set_header X-Application-Context "appointment";

        proxy_pass http://appointment-api:8080;
    }
}
```

**File: `/etc/nginx/conf.d/portal.conf`**

```nginx
server {
    listen 80;
    server_name portal.hspc.app;

    root /usr/share/nginx/html/portal;

    location / {
        try_files $uri $uri/ /index.html;
    }

    location /api/ {
        # Preserve NPM headers
        proxy_set_header X-Real-IP $http_x_real_ip;
        proxy_set_header X-Forwarded-For $http_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $http_x_forwarded_proto;
        proxy_set_header X-Forwarded-Host $http_x_forwarded_host;
        proxy_set_header Host $http_host;
        proxy_set_header X-Request-ID $http_x_request_id;

        # Add application headers
        proxy_set_header X-Application-Context "patient-portal";

        proxy_pass http://portal-api:8080;
    }
}
```

### Applying Configuration Changes

**Method 1: If Config is Mounted from Host**

```bash
# Edit your local file
nano ./nginx/conf.d/default.conf

# Test configuration
docker exec frontend-nginx nginx -t

# If successful, reload
docker exec frontend-nginx nginx -s reload

# Or restart container
docker restart frontend-nginx
```

**Method 2: If Config is Inside Container**

```bash
# Copy current config out (backup)
docker cp frontend-nginx:/etc/nginx/conf.d/default.conf ./backup-default.conf

# Edit locally
nano default.conf

# Copy back into container
docker cp default.conf frontend-nginx:/etc/nginx/conf.d/default.conf

# Test configuration
docker exec frontend-nginx nginx -t

# Reload
docker exec frontend-nginx nginx -s reload
```

**Method 3: Rebuild Container with New Config (Recommended for Production)**

```dockerfile
# Dockerfile for frontend-nginx
FROM nginx:alpine

# Copy custom nginx config
COPY nginx.conf /etc/nginx/nginx.conf
COPY conf.d/ /etc/nginx/conf.d/

# Copy frontend assets
COPY dist/ /usr/share/nginx/html/

EXPOSE 80
```

```bash
# Build new image
docker build -t frontend-nginx:latest .

# Stop old container
docker stop frontend-nginx

# Remove old container
docker rm frontend-nginx

# Run new container
docker run -d --name frontend-nginx \
  --network frontend \
  frontend-nginx:latest
```

---

## Laravel Backend Configuration

### Location 1: Trust Proxy Middleware

Laravel needs to trust the proxy chain to correctly read forwarded headers.

**File: `app/Http/Middleware/TrustProxies.php`**

```php
<?php

namespace App\Http\Middleware;

use Illuminate\Http\Middleware\TrustProxies as Middleware;
use Illuminate\Http\Request;

class TrustProxies extends Middleware
{
    /**
     * The trusted proxies for this application.
     *
     * Trust all proxies (we're in controlled private network)
     */
    protected $proxies = '*';

    /**
     * Alternative: Trust specific Docker networks
     * protected $proxies = ['172.16.0.0/12', '10.0.0.0/8'];
     */

    /**
     * The headers that should be used to detect proxies.
     */
    protected $headers =
        Request::HEADER_X_FORWARDED_FOR |
        Request::HEADER_X_FORWARDED_HOST |
        Request::HEADER_X_FORWARDED_PORT |
        Request::HEADER_X_FORWARDED_PROTO |
        Request::HEADER_X_FORWARDED_AWS_ELB;
}
```

### Location 2: Middleware to Extract Tenant

**File: `app/Http/Middleware/IdentifyTenant.php`**

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use App\Models\Tenant;
use Illuminate\Support\Facades\Log;

class IdentifyTenant
{
    public function handle(Request $request, Closure $next)
    {
        // Get tenant ID from header (set by NGINX)
        $tenantId = $request->header('X-Tenant-ID');

        // Log for debugging
        Log::info('Tenant request', [
            'tenant_id' => $tenantId,
            'request_id' => $request->header('X-Request-ID'),
            'real_ip' => $request->header('X-Real-IP'),
            'forwarded_for' => $request->header('X-Forwarded-For'),
            'host' => $request->header('Host'),
        ]);

        if (!$tenantId) {
            return response()->json([
                'error' => 'Tenant not identified'
            ], 400);
        }

        // Look up tenant
        $tenant = Tenant::where('subdomain', $tenantId)->first();

        if (!$tenant) {
            return response()->json([
                'error' => 'Tenant not found'
            ], 404);
        }

        // Store tenant in request for later use
        $request->merge(['tenant' => $tenant]);

        // Set tenant context globally
        app()->instance('tenant', $tenant);

        return $next($request);
    }
}
```

### Location 3: Register Middleware

**File: `app/Http/Kernel.php`**

```php
protected $middlewareGroups = [
    'api' => [
        \App\Http\Middleware\TrustProxies::class,
        \App\Http\Middleware\IdentifyTenant::class,
        // ... other middleware
    ],
];
```

### Location 4: Access Client IP in Controllers

**File: `app/Http/Controllers/ExampleController.php`**

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class ExampleController extends Controller
{
    public function index(Request $request)
    {
        // Get real client IP (from X-Real-IP header)
        $clientIp = $request->ip();

        // Get tenant
        $tenant = $request->tenant;

        // Get request ID for logging/tracing
        $requestId = $request->header('X-Request-ID');

        return response()->json([
            'client_ip' => $clientIp,
            'tenant' => $tenant->name,
            'request_id' => $requestId,
        ]);
    }
}
```

### Location 5: Logging Configuration

**File: `config/logging.php`**

```php
'channels' => [
    'stack' => [
        'driver' => 'stack',
        'channels' => ['single'],
        'ignore_exceptions' => false,
        'formatter' => env('LOG_FORMATTER', \Monolog\Formatter\JsonFormatter::class),
    ],

    'single' => [
        'driver' => 'single',
        'path' => storage_path('logs/laravel.log'),
        'level' => env('LOG_LEVEL', 'debug'),
        'formatter' => \Monolog\Formatter\JsonFormatter::class,
    ],
],
```

**File: `app/Providers/AppServiceProvider.php`**

```php
use Illuminate\Support\Facades\Log;

public function boot()
{
    // Add request context to all logs
    if (request()->hasHeader('X-Request-ID')) {
        Log::shareContext([
            'request_id' => request()->header('X-Request-ID'),
            'tenant_id' => request()->header('X-Tenant-ID'),
            'client_ip' => request()->ip(),
        ]);
    }
}
```

---

## Testing and Verification

### Test 1: Check Headers Reaching Laravel

Create a test endpoint to see all headers:

**File: `routes/api.php`**

```php
Route::get('/debug/headers', function (Request $request) {
    return response()->json([
        'all_headers' => $request->headers->all(),
        'client_ip' => $request->ip(),
        'tenant_id' => $request->header('X-Tenant-ID'),
        'request_id' => $request->header('X-Request-ID'),
        'real_ip' => $request->header('X-Real-IP'),
        'forwarded_for' => $request->header('X-Forwarded-For'),
        'forwarded_proto' => $request->header('X-Forwarded-Proto'),
        'host' => $request->header('Host'),
    ]);
});
```

**Test from your browser:**
```
https://clinic1.hspc.app/api/debug/headers
```

**Expected output:**
```json
{
  "all_headers": { ... },
  "client_ip": "203.0.113.45",
  "tenant_id": "clinic1",
  "request_id": "abc123xyz",
  "real_ip": "203.0.113.45",
  "forwarded_for": "203.0.113.45, 165.227.1.1",
  "forwarded_proto": "https",
  "host": "clinic1.hspc.app"
}
```

### Test 2: Check NGINX Headers

```bash
# Check NPM logs
docker logs npm | tail -50

# Check frontend NGINX logs
docker logs frontend-nginx | tail -50

# Look for request ID and tenant ID in logs
docker exec frontend-nginx tail -f /var/log/nginx/access.log
```

### Test 3: End-to-End Header Flow Test

```bash
# Make request with curl and see headers
curl -v https://clinic1.hspc.app/api/debug/headers

# Expected in response headers:
# < X-Request-ID: abc123
# And in response body:
# {"tenant_id": "clinic1", ...}
```

### Test 4: IP Address Test

From an external machine (not your server):

```bash
# Visit your site
curl https://clinic1.hspc.app/api/debug/headers

# Check if client_ip matches YOUR actual IP
# NOT 172.x.x.x (Docker internal)
# NOT 10.x.x.x (DO private network)
```

### Test 5: Multi-Tenant Test

```bash
# Test different tenants
curl https://clinic1.hspc.app/api/debug/headers | grep tenant_id
curl https://clinic2.hspc.app/api/debug/headers | grep tenant_id
curl https://clinic3.hspc.app/api/debug/headers | grep tenant_id

# Each should show different tenant_id
```

---

## Common Issues and Solutions

### Issue 1: Laravel sees 172.x.x.x as client IP

**Cause:** Headers not being passed through or TrustProxies not configured

**Solution:**
```php
// In TrustProxies.php
protected $proxies = '*';  // Trust all proxies

// In NGINX
proxy_set_header X-Real-IP $http_x_real_ip;  // Not $remote_addr
```

### Issue 2: Tenant ID is empty

**Cause:** NGINX not extracting subdomain correctly

**Solution:**
```nginx
# Check your regex pattern
set $tenant_id "";
if ($http_host ~* "^([^.]+)\.hspc\.app$") {
    set $tenant_id $1;
}

# Test regex
docker exec frontend-nginx nginx -T | grep tenant_id
```

### Issue 3: Request ID is lost

**Cause:** NPM not generating it or NGINX not passing it

**Solution:**
```nginx
# In NPM Advanced config
proxy_set_header X-Request-ID $request_id;

# In App NGINX
proxy_set_header X-Request-ID $http_x_request_id;
```

### Issue 4: WebSocket connections fail

**Cause:** Upgrade headers not configured properly

**Solution:**
```nginx
# In both NPM and App NGINX
proxy_http_version 1.1;
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;

# In NPM Advanced (global)
map $http_upgrade $connection_upgrade {
    default upgrade;
    '' close;
}
```

### Issue 5: 502 Bad Gateway

**Cause:** NGINX can't reach upstream

**Solution:**
```bash
# Check if containers are on same network
docker network inspect frontend

# Check if container name is correct
docker ps | grep frontend-nginx

# Test connectivity
docker exec npm ping frontend-nginx
docker exec npm curl http://frontend-nginx:80/health

# Check NGINX config syntax
docker exec frontend-nginx nginx -t
```

### Issue 6: SSL redirect loop

**Cause:** X-Forwarded-Proto not set or Laravel forcing HTTPS incorrectly

**Solution:**
```nginx
# NPM must set
proxy_set_header X-Forwarded-Proto $scheme;

# App NGINX must preserve
proxy_set_header X-Forwarded-Proto $http_x_forwarded_proto;
```

```php
// In Laravel app/Providers/AppServiceProvider.php
public function boot()
{
    // Force HTTPS in production
    if (config('app.env') === 'production') {
        \URL::forceScheme('https');
    }
}
```

---

## Quick Reference Card

### NPM Settings Checklist
- Forward to Docker service name (not IP)
- Forward port matches container expose
- WebSocket support enabled
- Custom location with proxy headers configured
- X-Request-ID generation added
- SSL certificate configured for wildcard
- Force SSL enabled

### App NGINX Settings Checklist
- Tenant extraction from hostname configured
- Headers preserved (not overwritten)
- Application headers added (X-Tenant-ID, X-Application-Context)
- Client body size matches NPM
- Timeouts shorter than NPM
- Health check endpoint configured
- Logging with request ID and tenant ID

### Laravel Settings Checklist
- TrustProxies middleware configured
- IdentifyTenant middleware registered
- Request context added to logs
- Test endpoint for header verification

---

## Final Notes

1. **Always test after changes**: Use the `/api/debug/headers` endpoint
2. **Check logs at each layer**: NPM → App NGINX → Laravel
3. **Use request IDs**: Makes debugging multi-hop requests possible
4. **Document your changes**: Keep this guide updated with your specific setup
5. **Monitor in production**: Watch for header-related issues after deployment

This configuration ensures clean header handling throughout your proxy chain while maintaining tenant isolation and proper client IP tracking for your healthcare SaaS platform.
