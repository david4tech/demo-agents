# demo-agents

A 9-agent "technology department" crew for KiroCrew, used live in the talk
**"Stop Babysitting Your AI. Build a Crew Instead."** A single orchestrator
(`tech-lead`) decomposes a task and delegates each stage to a specialist; work
runs in parallel where the stages allow it.

Real example built by this crew end to end: **LinkSnip**, a serverless URL
shortener on AWS (design -> Terraform -> backend + frontend -> tests -> review +
security -> CI/CD).

## Roster

| Agent | Stage | Owns | Model | MCP | Skills |
|---|---|---|---|---|---|
| `tech-lead` | 0 Orchestrate | Decompose, delegate, synthesize (does not implement) | claude-opus-4.8 | kirocrew-core | - |
| `cloud-architect` | 1 Design | AWS architecture, service selection, diagrams | claude-opus-4.8 | awsdac, aws-diagram, aws-pricing-calculator | - |
| `infra-engineer` | 2 Infra | Terraform / CloudFormation, provisioning | claude-sonnet-5 | github, aws-pricing-calculator | prepare-pr |
| `backend-dev` | 3 Code (parallel) | APIs, services, lambdas | claude-sonnet-5 | github | prepare-pr |
| `frontend-dev` | 3 Code (parallel) | UI | claude-sonnet-5 | github | frontend-design-workflow, web-verify, prepare-pr |
| `qa-tester` | 4 Tests | unit / integration / e2e, quality gates | claude-sonnet-4.6 | - | pod-e2e, web-verify |
| `code-reviewer` | 5 Validate (parallel) | PR review, standards | claude-sonnet-5 | github | sage-review |
| `security-engineer` | 5 Validate (parallel) | vulns, IAM, compliance, secrets scan | claude-sonnet-5 | aws-pricing-calculator | - |
| `cicd-engineer` | 6 Deploy | pipelines, build, deploy, monitoring | claude-sonnet-4.6 | github | prepare-pr |

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

See `docs/orchestration.md` for the full flow and `docs/mcp-and-skills.md` for
the per-agent MCP and skill wiring.

## Layout

```
agents/    <- per-agent registration (model, description, triggers, mcp, skills, workspace, memory store)
prompts/   <- each agent's system/steering prompt
crew.json  <- machine-readable roster
docs/      <- orchestration + MCP/skills notes
```

## Notes

- Each agent runs in its own isolated workspace + memory store.
- Model tiers are chosen per role: heavy reasoning on opus, code/review/security
  on sonnet-5, QA and CI/CD on sonnet-4.6.
- `mcp` and `skills` reflect the crew's intended wiring. Exact low-level tool
  allowlists live in the Kiro CLI agent definitions and are not included here.
- These are definitions only. No credentials or secrets are included.
