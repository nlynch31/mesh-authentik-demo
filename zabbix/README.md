# Zabbix demo config

This directory contains the minimal files needed to run the Zabbix demo service
(PostgreSQL + Caddy frontend) without committing secrets or the upstream repo.

Run from this directory:
docker compose -f compose.yaml up -d

Create runtime env files (not tracked):
cp .env_db_pgsql.example .env_db_pgsql
cp .env_srv.example .env_srv
cp .env_web.example .env_web
