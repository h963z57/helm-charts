# Universal chart

Render a minimal Deployment or the expanded Deployment with a sidecar, Service,
PriorityClass, ConfigMap, and Amazon EFS storage.

```sh
helm template example ./universal --namespace example-ns -f universal/examples.minimal.values.yaml
helm template example ./universal --namespace example-ns -f universal/examples.values.yaml
```

For installation, create the target namespace. The expanded example references an
existing Secret named `example-token-secret` with a `token` key. Replace the
placeholder EFS IDs and region and make the EFS CSI driver available before installing.

Generate the schema from the expanded example with helm-schema 0.24.0:

```sh
helm schema --chart-search-root universal --dependencies-filter UNIVERSAL \
  --no-dependencies --value-files examples.values.yaml --dont-add-global \
  --skip-auto-generation required,default,additionalProperties,title,description
```

## Values

| Field | Accepted input and default |
| --- | --- |
| `NAME`, `CONTAINERS`, `IMAGES` | Supply a workload name, a container list with a leading preset, and an image map keyed by uppercase preset name. |
| `REPLICAS`, `ENVIRONMENT` | `REPLICAS` is a string or template string and defaults to `"1"`. `ENVIRONMENT` defaults to `prod`. |
| `RESOURCE_<NAME>_<PRESET>` | Supply a string for each active container. Use uppercase names and replace hyphens with underscores. The result must contain CPU limit, memory limit, CPU request, and memory request, separated by slashes. Whitespace anywhere in the resolved tuple causes a render error. |
| `PORTS`, `COMMANDS`, `ARGS`, `PROBES` | Use maps keyed by uppercase preset or `default_<PRESET>`. A Service port defaults its `targetPort` to `port`. Set `hostport` on a port entry to reuse that port as the host port. |
| `ENVIRONMENTS` | Use lists under `default`, `default_<PRESET>`, or the uppercase preset. `text` takes `value`, `secret` takes `key`, and `fieldRef` takes a field path in `value`. |
| `VOLUME_MOUNTS`, `DCM`, `DCM_SELECTOR` | Use preset-keyed lists for mounts and dynamic ConfigMaps. The optional selector lists ConfigMap names. Omit it to leave volume selection unfiltered. |
| `PRIORITY` | Optional map with numeric `VALUE`, boolean `STRICT`, and a `NODE_SELECTOR` list. |
| `RECREATE`, `DAEMONSET`, `DIRECT_CONNECTION`, `SERVERTOKEN` | Optional boolean switches. Direct connections can supply an `EXTERNAL_IP` list. |
| `SERVICEACCOUNT`, `USER` | Optional service account name and container user ID. |
| `HPA`, `GRACEFULL_SHUTDOWN` | Optional autoscaling and preset lifecycle settings. See the chart templates for branch-specific fields. |

Helm checks the original values against the schema. During rendering, the chart
checks the result of `tpl` for `REPLICAS` and each active container's resource
tuple. Zero replicas and empty environment text are supported. Dynamic
ConfigMaps require a `subPath` and a string in the first `content` entry's
`data` field. Later fragments can use `data` or `data_nl` with strings or
environment-keyed maps. Helm reports template syntax and evaluation errors
during `tpl`; the chart checks the result after evaluation succeeds.
