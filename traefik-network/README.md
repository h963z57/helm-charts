# TRAEFIK-NETWORK CHART

## Example ```values.yaml```
```yaml
INBOUND_CONNECTION:
  - {name: ,         # any
    type: ,          # http | tcp
    domain: ,        # my.example.com
    port: ,          # pods inbound
    entryPoints: [], # name of your entrypoint in Traefik
    ### optionals
    namespace: ,     # default is <name>-ns
    service: ,       # default is <name>-svc
    middlewaresPresents: [], # list of presets
    passthrough: ,           # passthrough|notPresent or <no param>
    proxyProtocol: ,         # 1|2 or <no param>
    }
```