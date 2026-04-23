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


### Create .env file:

AUTHENTIK_SECRET_KEY=#########################
PG_PASS=#########
### Add DNS

*Could use your insight here - trying to update for deployment*
Important: establish a stable service hostname.
- If using the in-mesh zone, add a record to `mesh.zone` in the `nycmesh-dns` repository.
- If using a second-level domain like `nycmesh.net`, add a Terraform `namedotcom_record` resource under `sld/`.
- Submit the change as a pull request.
- Point the record at the stable ingress or service endpoint for the IdP.
Proposed hostnames:
- `auth.mesh.nycmesh.net`
- `auth.nycmesh.net`
### Reverse Proxy

*Same situation - trying to update for deployment*

The test repro contains a caddy reverse proxy - for production, instruct the load balancer in the destination supernode via a k8s-infra PR (I think!). 
For requests going to Authentik itself, the proxy/LB needs to pass:

- X-Forwarded-Proto
- X-Forwarded-For
- Host

And for WebSocket-capable paths, also:

- Connection: Upgrade
- Upgrade: WebSocket
### Start Authentik

Username: akadmin
Password: review environmental variable AUTHENTIK_BOOTSTRAP_PASSWORD if initial login flow fails
### Authentik Configuration

Add user to admin group so you can disable the “akadmin” user.

### Add an application

When adding an application, choose “configure application with provider.”

### Generic SAML Configuration

User → Application → redirect → Authentik (SAML IdP) → login → return → Zabbix

Configure `baseurl` to be the **full public RP UI URL**, often with the path and trailing slash if applicable, not just the bare domain.

Set ACS URL to: [https://{ui-base}/index_sso.php ](https://{ui-base}/index_sso.php?acs)(review application to be sure)

SLS URL: SLS URL: https://{ui-base}/index_sso.php?sls

**SP entity ID / Audience URI:** > SP entity ID: must **exactly match** the value configured in Authentik (case-sensitive). This can be a URL or any unique string, but must be identical on both sides.
Slug: same

Binding: POST (basically redirect only supports smaller messages)

NameID mapping:
	- **Preferred:** persistent, non-changing identifier for NameID

If you want RBAC, make sure Authentik sends a group attribute using a SAML property mapping.

Then configure RP **Group name attribute** and **User group mapping** so incoming SAML groups map to RP groups/roles.

- Enable SAML authentication: enabled
- IdP entity ID: paste the Authentik IdP Entity ID
- SSO service URL: paste the Authentik SSO URL
- Username attribute: start with NameID
- SP entity ID: application or hostname
- Certificate: paste the Authentik signing certificate
- ==You will also want to enable JIT as well== -- note  if JIT provisioning is enabled, Zabbix requires a **deprovisioned users group** to be set in the Authentication tab

Authentik provides SAML metadata, endpoints, and certificates for signing/verification. On the Zabbix side, you provide the IdP certificate, and if you enable signing or encryption from the SP side, you also provide the SP certificate/private key.

Certificate handling:

- Zabbix can store the IdP certificate via the web UI (database-backed)
- In some container deployments, mounting the certificate as a file is more reliable or required depending on configuration

Example (container-based setup):

volumes:
  - ./certs:/etc/zabbix/web/certs:ro


From application, copy: 

 - SSO URL
- Entity ID
- Certificate

In application, enable these settings (if present)

Zabbix, for example, requires the following in $SSO[‘SETTINGS’] =

$SSO['SETTINGS'] = [
    'strict' => false,
    'baseurl' => 'https://domainname',
    'use_proxy_headers' => true
];


==Review VSCODE history to determine final step here==

### Generic OAUTH / OIDC Configuration

### Notes

