# Zabbix demo config

This directory contains the minimal files needed to run the Zabbix demo service
(PostgreSQL + nginx frontend) without committing secrets or the upstream repo.

Upstream repo clone lives at ../zabbix-docker (not tracked in git).

Run from this directory:
docker compose -f docker-compose_v3_alpine_pgsql_latest.yaml up -d

Create runtime env files (not tracked):
cp .env_db_pgsql.example .env_db_pgsql
cp .env_srv.example .env_srv
cp .env_web.example .env_web
