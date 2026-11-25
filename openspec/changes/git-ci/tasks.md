# Tasks

## 1. Tag-triggered workflow

- [x] 1.1 Add a workflow under `.github/workflows/` for numeric version tag pushes. Exclude tag deletions, check out the event revision, and grant repository read access. Verify the trigger and checkout configuration with a workflow syntax check and review.

## 2. Helm checks

- [x] 2.1 Provision Helm 3.19.0 in the workflow and render both values examples for each chart with schema validation enabled. Disable matrix fail-fast and name each job by chart and example. Verify that the four equivalent local commands succeed and that a failing render makes its job fail.
- [x] 2.2 Run lint for each matrix entry. Require `traefik-network` lint success and keep only the `universal` lint step advisory. Verify that the existing `Deployment-config` error remains visible and that a failed universal render still fails CI.
- [x] 2.3 Add the negative check using the minimal network example and `--set 'INBOUND_CONNECTION[0].type=unsupported'`. Verify that it accepts only a nonzero Helm exit with the expected schema diagnostic for `/INBOUND_CONNECTION/0/type`. Confirm that successful rendering or an unrelated error fails the check.
- [x] 2.4 Document the tag format, four chart/example combinations, local Helm commands, and the lint exception in the repository README. Verify that the documented positive and negative commands produce the expected results with Helm 3.19.0.

## 3. Integration verification

- [x] 3.1 Verify the first approved version tag pushed on a commit containing the workflow. Confirm the run checks out the tagged commit and reports four successful renders and a successful negative check. Record the run URL, or document that remote verification is pending an approved tag push.

Remote verification is pending the first approved version tag pushed on a commit containing the workflow.
