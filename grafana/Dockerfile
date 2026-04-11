FROM grafana/grafana

USER root

COPY caddy-root.crt /usr/local/share/ca-certificates/caddy-root.crt

RUN update-ca-certificates || true

USER grafana