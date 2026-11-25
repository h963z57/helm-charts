# Helm Values Validation

## Purpose

Define installable configuration examples and predictable validation for Helm chart values so operators can fix input errors before submitting resources to Kubernetes.

## ADDED Requirements

### Requirement: Runnable chart values examples

Each chart SHALL publish minimal and expanded values examples with synthetic data and instructions for running them with Helm.

#### Scenario: Render both profiles

- **WHEN** an operator renders either chart with its minimal or expanded example
- **THEN** Helm renders the resources named in that example's documentation
- **AND** the example documents external prerequisites for installing those resources

### Requirement: Validate chart values through Helm

Each chart SHALL provide a schema that validates supported input types, allowed values, required fields, and conditional field requirements where these can be determined from the merged values.

#### Scenario: Reject malformed literal input

- **WHEN** Helm validates a route with an unsupported type, a port outside the valid range, or an HTTP route missing its required backend port
- **THEN** chart validation fails and identifies the offending values path

#### Scenario: Accept supported dynamic keys and template strings

- **WHEN** a workload supplies valid preset-specific keys and a supported template string for replicas
- **THEN** schema validation accepts the input types and Helm evaluates the template string during rendering

### Requirement: Validate values resolved during rendering

The chart SHALL stop rendering when a chart-consumed required value is missing or its resolved form is invalid. The diagnostic SHALL identify the chart input path or computed key and state the expected form.

#### Scenario: Report an invalid resolved replica count

- **WHEN** a supported replica template resolves to an empty string, a negative number, or a non-integer
- **THEN** Helm exits with an error that identifies `REPLICAS` and the expected non-negative integer

#### Scenario: Report invalid resources for an active container

- **WHEN** a rendered container's computed `RESOURCE_*` key is missing or resolves to an invalid limit and request tuple
- **THEN** Helm exits with an error that names the key and describes its four expected components

#### Scenario: Reject whitespace in a resolved resource tuple

- **WHEN** the resolved value of an active container's computed `RESOURCE_*` key contains whitespace, including before or after a slash
- **THEN** Helm exits with an error that names the computed key and states the expected tuple format

#### Scenario: Preserve valid zero and optional values

- **WHEN** a supported replica value resolves to zero or a templated text field allows an empty result
- **THEN** that result passes its field-specific validation

### Requirement: Preserve existing valid values

Installing, upgrading, or rendering with supported valid values SHALL retain the existing chart behavior and resource output.

#### Scenario: Render existing deployments with validation enabled

- **WHEN** representative current consumer values for both charts are rendered under identical release and namespace inputs before and after the change
- **THEN** the rendered Kubernetes resources are semantically equivalent
- **AND** deployment-specific credentials and infrastructure identifiers are excluded from committed examples and verification output
