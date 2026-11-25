# Design

## Context

The repository has two application charts, each with minimal and expanded values examples and a checked-in schema. Local Helm 3.19.0 rendered all four combinations. Both `traefik-network` lint checks passed. Both `universal` lint checks reported the undeclared local `Deployment-config` dependency.

## Goals / Non-Goals

**Goals:** Reproduce the local example checks on the tagged commit and make each chart and example result identifiable.

**Non-Goals:** Kubernetes API validation, runtime readiness, chart packaging, and publication.

## Decisions

1. Add a repository workflow under `.github/workflows/` for tag pushes. Use the GitHub tag filter `[0-9]+.[0-9]+.[0-9]+` and exclude tag deletions. Check out the event revision. The numeric format follows existing tags such as `6.0.0`. A branch or pull request trigger could provide earlier feedback in a separate change.
2. Use a Linux runner and pin Helm to 3.19.0, the locally verified version. Use a four-entry chart/example matrix with fail-fast disabled. Run `helm template example ./<chart> --namespace example-ns -f <chart>/<example>` with schema validation enabled. The runner needs repository read access and no cluster credentials.
3. Require `helm lint` for both `traefik-network` examples and report it as a non-blocking diagnostic for `universal`. Keep the exception scoped to the lint step so any universal render failure still fails the run.
4. For the required negative check, override the minimal network example with an unsupported route type using a synthetic override. Assert both the nonzero exit and the schema diagnostic for `INBOUND_CONNECTION[0].type`, allowing for Helm's path formatting. Use the existing values schema and Helm CLI.

## Risks / Trade-offs

- Existing tags point to commits created before the workflow. For end-to-end verification, push a new approved version tag on a commit that already contains the workflow.
- Rendering checks the example values and template output locally. Verifying Kubernetes API acceptance would require a cluster, Traefik CRDs, and installation prerequisites for the expanded examples.
- The advisory `universal` lint step can report new lint errors without failing CI. Keep its errors visible and revisit the exception when the dependency declaration is addressed separately.
