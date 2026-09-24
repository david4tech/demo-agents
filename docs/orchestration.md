# Orchestration

The `tech-lead` is the only orchestrator. It does not implement; it decomposes
the user's task, delegates each stage to the right specialist, and synthesizes
the results.

## Stage / domain map

| Stage | Agent(s) | Owns |
|---|---|---|
| 1 Design | `cloud-architect` | AWS architecture, service selection, diagrams |
| 2 Infra | `infra-engineer` | Terraform / CloudFormation, provisioning |
| 3 Code | `backend-dev` + `frontend-dev` (parallel) | APIs/lambdas + UI |
| 4 Tests | `qa-tester` | unit / integration / e2e, quality gates |
| 5 Validate | `code-reviewer` + `security-engineer` (parallel) | PR review, standards, vulns, IAM, compliance |
| 6 Deploy | `cicd-engineer` | pipelines, build, deploy, monitoring |

## Rules

- Run stages sequentially; each stage depends on the previous. Parallelize only
  within a stage (stage 3 and stage 5).
- Delegate execution; never do a specialist's substantive work in the parent.
  Keep planning, sequencing, and synthesis in the orchestrator.
- One coherent task per specialist. If a stage fails, stop and report rather
  than blindly retrying.
- After all stages, synthesize a short final report: what was built, artifact
  and PR URLs, and the deploy URL.

## Widening the fan-out

To exercise more agents in parallel, give the crew a task that decomposes into
several independent units (for example multiple independent services and
multiple frontend views). In stage 3 each developer can then spawn parallel
subagents, one per unit, instead of building sequentially.
