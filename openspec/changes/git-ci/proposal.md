# Proposal

## Why

Maintainers have no automated check that the charts at a pushed version tag still render with their documented examples. Checking the tagged commit catches schema and template regressions in the version consumers receive.

## What Changes

- Run GitHub Actions when a numeric version tag is pushed.
- Require the minimal and expanded examples of `universal` and `traefik-network` to render with schema validation enabled.
- Verify that Helm rejects synthetic invalid values and report lint results with the existing `universal` exception documented.

## Capabilities
### New Capabilities

- `helm-chart-tag-ci`: Validate the documented chart examples at a pushed version tag and report failures in GitHub Actions.

### Modified Capabilities

## Impact

Implementation adds a GitHub Actions workflow and documents its checks. Chart source, dependencies, example values, OpenSpec configuration, Kubernetes installation, and release publication are outside the scope.
