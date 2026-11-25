# Traefik network chart

The minimal example renders one HTTP route. The expanded example renders HTTP,
TCP, and TraefikService routes plus a BasicAuth middleware.

```sh
helm template example ./traefik-network -f traefik-network/examples.minimal.values.yaml
helm template example ./traefik-network -f traefik-network/examples.values.yaml
```

Installing the examples requires the Traefik CRDs, the route namespaces and
entry points, and the referenced backend Services. The expanded HTTP route
requires the `example-basic-auth` Secret in `example-ns`. The TraefikService
example references `api@internal`.

Each route requires a name, type, and at least one entry point. HTTP requires
a domain and backend port. TCP requires a backend port, plus a domain for
`tls: passthrough`. `tls: notPresent` omits the TLS section. Omitting `tls`
renders an empty TLS section. `proxyProtocol` accepts version `1` or `2`.
The default namespace is `<name>-ns` and the default backend is `<name>-svc`.
Middleware presets use `middlewaresPresents`. The `basic-auth` preset needs
`secretName`, and `keycloak-auth` needs `address`.

Generate the schema from the expanded example with helm-schema 0.24.0:

```sh
helm schema --chart-search-root traefik-network --dependencies-filter 'Traefik network' \
  --no-dependencies --value-files examples.values.yaml --dont-add-global \
  --skip-auto-generation required,default,additionalProperties,title,description
```
