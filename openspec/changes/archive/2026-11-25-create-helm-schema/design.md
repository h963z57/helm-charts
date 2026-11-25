# Design

## Context

The exploration identified 14 workload values files and two routing values files under `../cloud-ias/helm/vars/`. Three files had other input shapes. These counts establish a comparison set for the current chart interfaces. The existing charts also accept dynamic preset names and render some inputs through Helm `tpl`. `universal` uses the local `Deployment-config` library. `traefik-network` consumes `INBOUND_CONNECTION` and renders HTTP, TCP, and TraefikService routes.

## Goals / Non-Goals

**Goals:** Provide repeatable schema generation and surface actionable input errors at the earliest phase that has enough information.

**Non-Goals:** Cluster discovery, dependency changes, migration of other consumers, and validation of arbitrary application-specific ConfigMap payloads are outside the design scope.

## Decisions

1. **Ship one schema per application chart.** Place the generated Draft 7 schema beside each `Chart.yaml` so Helm validates merged values. Maintain a runnable `examples.minimal.values.yaml` and an annotated `examples.values.yaml` per chart. Use the installed `helm schema` plugin with explicit annotations and record its version and flags. Direct inference from a concrete application file would freeze preset names and infer requirements from incidental examples. Generate per application chart and use the exact chart filter or an isolated generation root, since recursive discovery also finds `Deployment-config`, which has no values file.
2. **Model the current input contract.** Use enums and conditionals for route type, backend port, TLS modes, and middleware fields. Use typed dynamic maps for presets and a pattern for computed `RESOURCE_*` keys. Include optional feature branches and describe their defaults and examples where the chart applies them. Account for chart defaults during Helm merging, including null placeholders. Keep extension keys open where valid consumers use them. Exact required keys derived from active container presets and the contents of templated strings are checked at render time. Align chart usage examples with actual template keys such as `tls: passthrough`.
3. **Validate resolved values at the consumer.** Locate each existing `tpl` call and document its output contract. Evaluate `REPLICAS` once per workload and check that it resolves to a non-negative integer before using it in metadata and the workload spec. Use `required` for values that must be present and non-empty, with a path-specific message. Use `fail` for malformed resolved content, preserving valid zero. In the resources helper, compute the `RESOURCE_*` key for each active container, check presence before indexing, and validate all four limit and request components before rendering. Preserve the existing evaluation order and quantity syntax. Reject whitespace in the resolved resource tuple, including around delimiters. For environment text and DCM content, preserve valid empty or arbitrary text where the chart permits it. Apply checks to chart-owned inputs and required fragments. Native Helm errors may report malformed template syntax before a custom post-evaluation check can run.
4. **Verify with the normal Helm entry points.** Use the same release name, namespace, values, and Helm flags when comparing baseline and changed renders. Keep deployment values and rendered sensitive fields local. Use synthetic fixtures for negative cases. Verify that schema failures identify the values path and render-time failures identify the computed key or input and expected form. Confirm install and upgrade behavior with dry runs where the required APIs are available.

## Risks / Trade-offs

- Strict inference can reject valid dynamic keys or merged defaults. Model those cases explicitly and compare existing valid manifests before releasing.
- Required values can change the outcome of rendering chart defaults alone. Document required input and use the examples as the supported smoke-test entry points.
- Baseline `helm lint` already reports an undeclared local `Deployment-config` dependency for `universal`. Record the baseline and check for new failures without changing dependency declarations.
- Local renders cannot establish that referenced Secrets, Traefik CRDs, and the EFS CSI driver exist. Document each prerequisite where the relevant example uses it.

## Migration Plan

Current consumers keep their values files if resolved resource tuples contain no whitespace. Remove any whitespace from resource tuples before upgrading. Operators with rejected inputs use the path and expected form in the error to correct their configuration before retrying. A rollback can use the previous chart version and its existing values.
