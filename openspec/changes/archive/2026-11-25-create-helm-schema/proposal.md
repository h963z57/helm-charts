# Proposal

## Why

Operators currently infer the values contract from chart templates and deployment-specific files. Invalid input can reach template evaluation or Kubernetes and produce errors that do not identify the value to fix.

## What Changes

- Provide runnable minimal and expanded values examples for `universal` and `traefik-network`, derived from existing usage and populated with synthetic data.
- Add a Helm-discoverable `values.schema.json` to each chart. Document accepted types, allowed values, defaults, required fields, and conditional fields supported by the current templates.
- Validate chart-consumed results of `tpl` during rendering where the resolved value has a known contract. Report the relevant input path and expected form using `required` or `fail`.
- Preserve representative valid configurations from `../cloud-ias/helm/vars/`. Reject invalid input earlier with a message the operator can act on. **BREAKING:** Resource tuples containing whitespace fail validation. Remove spaces from existing tuples before upgrading.

## Capabilities

### New Capabilities

- `helm-values-validation` defines examples, schema validation, and render-time diagnostics for the two application charts.

### Modified Capabilities

## Impact

The implementation will affect values examples, schema files, usage documentation, and validation in the application chart templates and existing `Deployment-config` helpers. The existing Helm installation flow and chart dependencies will be used. Validation targets chart inputs and rendered fields. Actual cluster prerequisites such as installed CRDs, referenced Secrets, and storage drivers still need to be satisfied.
