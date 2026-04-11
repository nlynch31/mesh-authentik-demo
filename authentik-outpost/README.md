# Authentik Outpost

This directory contains a Docker Compose configuration for an Authentik outpost proxy, used for protecting applications with Authentik authentication.

## Service

- **authentik-proxy**: Authentik's proxy service for authentication and authorization
  - Exposed ports: 9000 (HTTP), 9443 (HTTPS)
  - Connects to your main Authentik server for authentication

## Setup

1. **Configure Environment Variables**
   - Copy `.env` and update with your Authentik server details
   - Generate an outpost token from your Authentik Admin Interface:
     - Go to **Admin Interface** > **Directory** > **Tokens** > **Create Token**
     - Set token type to "Outpost"
     - Copy the token and paste it in `.env`

2. **Update Caddy Configuration**
   Add a route in your Caddyfile to route through the outpost:

   ```
   protected-app.test.internal {
       tls internal
       reverse_proxy authentik-outpost-proxy:9000
   }
   ```

## Quick Start

```bash
# Update .env with your configuration
cp .env .env.local
# Edit .env.local with your values

# Start the outpost
docker compose --env-file .env.local up -d
```

## Integration

The outpost proxy will:
- Handle authentication flows
- Inject user information headers
- Protect your applications
- Work seamlessly with your existing Authentik setup

## Notes

- Requires connection to your main Authentik server
- Uses the same `local-services-network` as other services
- Can be scaled horizontally if needed
- Supports both HTTP and HTTPS connections