# HELM-CHARTS

## Usage

```bash
helm install <release name> <path/to/chart/dir> -f <path/to/values.yaml> 
```

### Create values 
See chart list of depends and use ```values.yaml``` in libraries

## Tag CI

GitHub Actions checks new numeric `MAJOR.MINOR.PATCH` tags, such as `6.0.1`.
Push the tag to GitHub after it points to a commit that contains the workflow.
The workflow checks out that commit and renders the minimal and expanded examples
for both charts with Helm 3.19.0 and schema validation enabled.

Run the four renders locally from the repository root:

```bash
for chart in universal traefik-network; do
  for example in examples.minimal.values.yaml examples.values.yaml; do
    helm template example "./$chart" --namespace example-ns -f "$chart/$example" || exit 1
  done
done
```

Run `helm lint` with the same chart path, namespace, and values file to reproduce
the lint checks. Lint is required for `traefik-network`. The `universal` lint
result is advisory because Helm reports that the bundled `Deployment-config`
chart is missing from the parent dependency declaration. Both charts must render.

The negative check expects a nonzero exit and a schema error at
`/INBOUND_CONNECTION/0/type`:

```bash
helm template example ./traefik-network --namespace example-ns \
  -f traefik-network/examples.minimal.values.yaml \
  --set 'INBOUND_CONNECTION[0].type=unsupported'
```

### Thanks
- Andrei Perepechaev