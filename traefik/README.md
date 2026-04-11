# Whoami Service for Authentik Outpost Testing

This directory contains a Docker Compose configuration for the Traefik whoami test application, designed to work with Caddy as the reverse proxy.

## Service

- **whoami**: Simple test application that echoes back request information
  - Image: `traefik/whoami:latest`
  - Listens on port 80 internally
  - Useful for testing Authentik outpost configurations

## Quick Start

```bash
docker compose up -d
```

## Accessing the Service

The whoami service will be accessible through your Caddy configuration. Add a route in Caddy's Caddyfile to point to this service:

```
whoami.localhost {
    reverse_proxy whoami:80
}
```

Or if running on the same network, the service is accessible at `http://whoami:80` from other containers.

## Integration with Authentik

This service can be used to test Authentik outpost:

1. Configure Caddy to route requests through Authentik middleware
2. Forward traffic to the whoami service to verify headers and authentication
3. Test that Authentik headers are properly injected

## Stopping Services

```bash
docker compose down
```

## Notes

- Service uses internal expose (port 80 not published to host)
- Requires being on the same Docker network or network configuration in Caddy
- Returns detailed information about requests (headers, method, IP, etc.)

