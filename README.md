# demo-agents

A 9-agent "technology department" crew for KiroCrew, used live in the talk
**"Stop Babysitting Your AI. Build a Crew Instead."** A single orchestrator
(`tech-lead`) decomposes a task and delegates each stage to a specialist; work
runs in parallel where the stages allow it.

Real example built by this crew end to end: **LinkSnip**, a serverless URL
shortener on AWS (design -> Terraform -> backend + frontend -> tests -> review +
security -> CI/CD).

## Roster

| Agent | Stage | Owns | Model |
|---|---|---|---|
| `tech-lead` | 0 Orchestrate | Decompose, delegate, synthesize (does not implement) | claude-opus-4.8 |
| `cloud-architect` | 1 Design | AWS architecture, service selection, diagrams | claude-opus-4.8 |
| `infra-engineer` | 2 Infra | Terraform / CloudFormation, provisioning | claude-sonnet-5 |
| `backend-dev` | 3 Code (parallel) | APIs, services, lambdas | claude-sonnet-5 |
| `frontend-dev` | 3 Code (parallel) | UI | claude-sonnet-5 |
| `qa-tester` | 4 Tests | unit / integration / e2e, quality gates | claude-sonnet-4.6 |
| `code-reviewer` | 5 Validate (parallel) | PR review, standards | claude-sonnet-5 |
| `security-engineer` | 5 Validate (parallel) | vulns, IAM, compliance, secrets scan | claude-sonnet-5 |
| `cicd-engineer` | 6 Deploy | pipelines, build, deploy, monitoring | claude-sonnet-4.6 |

## Orchestration

Stages run sequentially; each depends on the previous. Parallelism happens
**within** a stage: stage 3 (`backend-dev` + `frontend-dev`) and stage 5
(`code-reviewer` + `security-engineer`). Each specialist may itself fan out into
parallel subagents when its stage decomposes into independent units.

```
tech-lead
  1  cloud-architect
  2  infra-engineer
  3  backend-dev  ||  frontend-dev
  4  qa-tester
  5  code-reviewer  ||  security-engineer
  6  cicd-engineer
```

See `docs/orchestration.md` for the full flow and rules.

## Layout

```
agents/    <- per-agent registration (model, description, triggers, workspace, memory store)
prompts/   <- each agent's system/steering prompt
crew.json  <- machine-readable roster
docs/      <- orchestration notes
```

## Notes

- Each agent runs in its own isolated workspace + memory store.
- Model tiers are chosen per role: heavy reasoning on opus, code/review/security
  on sonnet-5, QA and CI/CD on sonnet-4.6.
- These are definitions only. No credentials or secrets are included.
