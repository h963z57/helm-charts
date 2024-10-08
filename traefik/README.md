# TRAEFIK CHART

## Important params
```yaml
SSL:
  - {cert: "/run/secrets/example.com.crt", key: "/run/secrets/example.com.key"}

DASH_URL: traefik.example.com
```

## Example ```values.yaml```
```yaml
PROJECT_NAME: traefik

IMAGE:
  NAME: traefik
  SOURCE: traefik
  VER: "v3.0"

DIRECT_CONNECTION: true
PORTS:
  - {name: websecure,   port: 443,  protocol: TCP, proxyProtocol: true}
  - {name: dashboard,   port: 8080, protocol: TCP, proxyProtocol: true}
  - {name: traefik-exp, port: 8082, protocol: TCP}

VOLUME_MOUNTS:
  - {name: traefik-config,   mountPath: "/etc/traefik",     subPath: traefik.yml,   readOnly: true, configName: traefik.yml-cm}
  - {name: fullchain-secret, mountPath: "/run/secrets/example.com.crt", subPath: fullchain.pem, readOnly: true, secretName: fullchain.pem-secret, key: prod }
  - {name: privkey-secret,   mountPath: "/run/secrets/example.com.key", subPath: privkey.pem,   readOnly: true, secretName: privkey.pem-secret,   key: prod}

SSL:
  - {cert: "/run/secrets/example.com.crt", key: "/run/secrets/example.com.key"}

DASH_URL: traefik.example.com

HEALTHCHECK:
  TYPE: tcp
  port: 8082
  initialDelaySeconds: 60
  periodSeconds: 30
  timeoutSeconds: 10
  failureThreshold: 3
```