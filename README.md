# Mesh IAM Demo Stack (Authentik + Zabbix + Caddy)

This repo is a reproducible demo environment for evaluating Authentik (and later Keycloak) for NYC Mesh internal services.

## Services
- Caddy (reverse proxy)
- Authentik (IdP / SSO)
- Zabbix (test app for OIDC/proxy auth flows)

## What is NOT in this repo
- No secrets (.env files are ignored)
- No database volumes/data

## Quickstart (new VM)
1) Clone:
   - `git clone ...`
2) Create env files:
   - `cp authentik/.env.example authentik/.env` (generate secrets)
   - `cp zabbix/.env_db_pgsql.example zabbix/.env_db_pgsql` (set passwords)
   - `cp zabbix/.env_srv.example zabbix/.env_srv`
   - `cp zabbix/.env_web.example zabbix/.env_web`
3) Start:
   - `docker compose up -d` per service directory

## Notes
- Zabbix compose file: docker-compose_v3_alpine_pgsql_latest.yaml
