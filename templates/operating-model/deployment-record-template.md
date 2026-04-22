# Deployment-Record Template — Template for `Deployment-Record` Artifacts

> **Template ID:** TPL-OM-DEPL-001
> **Artifact Class:** Deployment-Record (GOV-OM-001 §4.1, §5.4, custody.md §4)
> **Produced In:** Deploy phase
> **Exit Gate:** Non-Prod-Deploy-Gate or Prod-Deploy-Gate (§6.4)

---

## Purpose

Immutable record of every deployment event. One record per deploy action (not per deploy target — a multi-env rollout produces multiple records). The record is the anchor for forward-link traceability: Deployment → Image → Build-Artifact → Code-Version → Spec-Version.

---

## Instantiation

Created by the `deploy-*.yml` workflows at the ledger location decided in L1 Spec 6 (placeholder: `io.openleap.k8s/deployments/ledger/YYYY/MM/<deployment-id>.yaml`).

Records are **immutable** — never updated in place. A rollback produces a new Deployment-Record pointing at the older image (not an edit of the failed record).

---

## Schema (YAML)

```yaml
# Deployment-Record
deploymentId: "DEPL-<monotonic>"          # unique, immutable
timestamp: "YYYY-MM-DDThh:mm:ssZ"
environment: "dev" | "test" | "staging" | "prod" | "prod-<tenant>"

# Forward-links (mandatory per GOV-OM-001 §5.4)
image:
  registry: "<registry-host>"
  repository: "io.openleap/<artifact-id>"
  tag: "<semver>"                          # e.g., "1.4.2"
  digest: "sha256:<64hex>"
buildArtifact:
  coordinates: "io.openleap:<artifact-id>:<semver>"  # Nexus GAV
  commitSha: "<40hex>"
codeVersion: "<semver>"
specVersion: "<spec-id>@<semver>"          # from CHANGELOG speckref

# UVL binding (product-derivation context)
uvlBinding:
  productId: "<product-id>"                # null for platform-track deploys
  bindingId: "<uvl-binding-id>"            # null for platform-track
  activeFeatures: [ "F-...", ... ]         # empty for platform-track

# Approvals (all gates for this environment)
approvals:
  - role: "Release Authority"
    approver: "<name-or-self@audit>"
    timestamp: "YYYY-MM-DDThh:mm:ssZ"
    mode: "team" | "solo"
  - role: "Security Authority"             # required for prod
    approver: "<name-or-self@audit>"
    timestamp: "YYYY-MM-DDThh:mm:ssZ"
    mode: "team" | "solo"
  - role: "Quality Steward"                # records veto-not-exercised
    approver: "<name-or-self@audit>"
    vetoExercised: false
    timestamp: "YYYY-MM-DDThh:mm:ssZ"
    mode: "team" | "solo"

# Pre-gate auto-checks
autoRejectChecks:
  traceability: "pass"
  custody: "pass"
  ontology: "pass"
  template: "pass"
  semver: "pass"
  tests: "pass"
  complianceEvidence: "pass"                # required for prod (GOV-DORA-001)

# Rollback linkage (only present if this is a rollback deploy)
rollback:
  replaces: "DEPL-<earlier>"
  reason: "cve-fast-path-auto-rollback" | "manual-rollback" | "failed-health-check"
  triggeringIncident: "<incident-id>"       # or null

# Post-deploy
postDeploy:
  healthCheck: "pass" | "pending" | "failed"
  firstObservedError: null                  # or "YYYY-MM-DDThh:mm:ssZ"
```

---

## Verification Rules (enforced by `traceability-check.yml`)

1. `deploymentId` matches `DEPL-[0-9]+` and is strictly monotonically greater than any prior record in the ledger.
2. `image.digest` exists in the registry at deploy time.
3. `buildArtifact.coordinates` resolves to a non-snapshot artifact in Nexus when `environment = prod` or `prod-*`.
4. `codeVersion` equals the `CHANGELOG` entry version at `image.tag`.
5. `specVersion` is resolvable to a Spec-ID at `dev.spec` with that semver tag.
6. `approvals[]` contains exactly the roles required for the environment:
   - Non-prod: `Release Authority` only
   - Prod: `Release Authority`, `Security Authority`, `Quality Steward`
7. For Team-Mode Prod: all three `approver` values are distinct.
8. All `autoRejectChecks` values are `"pass"`; any other value blocks deploy.
```
