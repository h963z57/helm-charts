# Spec Delta

## Purpose

Give maintainers an automated result for the documented Helm chart examples at each newly pushed version tag.

## ADDED Requirements

### Requirement: Check the tagged revision

The repository SHALL run chart CI in GitHub Actions when a new Git tag in the numeric MAJOR.MINOR.PATCH form is pushed. The workflow SHALL use the chart and values files from the commit referenced by that tag.

#### Scenario: Push a version tag

- **WHEN** a maintainer pushes a new tag such as `6.0.1` on a commit containing the workflow
- **THEN** GitHub Actions runs the checks against that tagged commit

#### Scenario: Push a branch or an unrelated tag

- **WHEN** a maintainer pushes a branch update or a tag outside the supported format
- **THEN** the tag-triggered chart checks do not run

### Requirement: Render all documented chart examples

The workflow SHALL require successful rendering with Helm values schema validation enabled for both the minimal and expanded example of each chart.

#### Scenario: All chart examples render

- **WHEN** the tagged versions of `universal` and `traefik-network` render with `examples.minimal.values.yaml` and `examples.values.yaml`
- **THEN** GitHub Actions reports all four render checks as passed

#### Scenario: A required render fails

- **WHEN** Helm rejects an example or fails to render it
- **THEN** the workflow fails and identifies the chart and example file

### Requirement: Verify schema rejection

The workflow SHALL require Helm to reject a synthetic invalid values case for an expected schema error.

#### Scenario: Invalid route type is rejected

- **WHEN** the workflow renders the otherwise valid minimal `traefik-network` example with an unsupported route type
- **THEN** Helm fails with a schema diagnostic identifying the invalid route type field and the negative check passes

#### Scenario: Expected schema rejection is missing

- **WHEN** the invalid example renders successfully or fails without the expected schema diagnostic
- **THEN** the workflow fails the negative check
