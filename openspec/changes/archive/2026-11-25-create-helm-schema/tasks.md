# Tasks

## 1. Examples and schemas

- [x] 1.1 Create the minimal and annotated expanded examples for `universal`, generate its schema, and document prerequisite and generation commands. Verify both examples with `helm template`. Confirm the schema accepts arbitrary supported preset names and rejects a wrong literal type.
- [x] 1.2 Create the minimal and annotated expanded examples for `traefik-network`, generate its schema, and update the chart README to use the current TLS and middleware fields. Render HTTP, TCP, and TraefikService examples and verify rejection of an unsupported route type, an out-of-range port, and a missing HTTP backend port.
- [x] 1.3 Record supported types, defaults, conditional fields, and dynamic keys in the relevant schema or usage documentation. Verify that documented field shapes match chart defaults and representative current values.

## 2. Render-time validation

- [x] 2.1 Validate the resolved `REPLICAS` string before emitting the workload. Verify that literal zero and templated zero pass and that empty, negative, and non-integer results fail with an actionable `REPLICAS` message.
- [x] 2.2 Validate each active container's computed `RESOURCE_*` input before splitting and indexing the resource tuple. Verify a valid tuple and a chart-supported template-derived case. Reject missing keys, incomplete tuples, invalid quantities, and resolved tuples with whitespace around a slash. Verify each error identifies the computed key.
- [x] 2.3 Review the existing environment text and DCM `tpl` callers for chart-owned requirements. Add field-specific checks wherever a required chart input or output contract exists, document when native Helm errors apply, and verify allowed empty text and representative dynamic ConfigMap content still render.

## 3. Integration and compatibility

- [x] 3.1 Compare manifests for the 14 workload and two network values files identified in the consumer directory using identical Helm inputs before and after the change. Verify semantic equivalence for supported valid files and record any baseline failures without publishing consumer values or rendered credentials.
- [x] 3.2 Run `helm lint` and `helm template` against both charts with the documented examples, record any pre-existing lint issues separately, and exercise install and upgrade dry runs where their required APIs are available. Verify that invalid inputs fail before resource submission.
