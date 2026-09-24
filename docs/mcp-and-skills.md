# MCP and skills per agent

Which MCP servers and KiroCrew skills each agent uses. This reflects the crew's
design. The exact low-level tool allowlists live in the Kiro CLI agent
definitions and are not included in this repo.

| Agent | MCP servers | Skills |
|---|---|---|
| `tech-lead` | kirocrew-core | (orchestration via select_crew + spawn_run) |
| `cloud-architect` | awsdac, aws-diagram, aws-pricing-calculator | - |
| `infra-engineer` | github, aws-pricing-calculator | prepare-pr |
| `backend-dev` | github | prepare-pr |
| `frontend-dev` | github | frontend-design-workflow, web-verify, prepare-pr |
| `qa-tester` | - | pod-e2e, web-verify |
| `code-reviewer` | github | sage-review |
| `security-engineer` | aws-pricing-calculator | - |
| `cicd-engineer` | github | prepare-pr |

## MCP pool

- **kirocrew-core**: orchestration (`select_crew`, `spawn_run`) - `tech-lead` only.
- **github**: PR workflow wrapper (open/update/review PRs) - agents that produce or review PRs.
- **awsdac** + **aws-diagram**: architecture diagrams (grouped-box YAML + mingrammer) - `cloud-architect`.
- **aws-pricing-calculator**: cost estimates - `cloud-architect`, `infra-engineer`, `security-engineer`.

No Slack MCP and no `revstar-standards` skill are attached to this crew.

## Note on fidelity

MCP and skill assignments here are the crew's intended wiring, recorded when the
crew was built. To reproduce the agents exactly (including raw tool allowlists),
export each Kiro CLI agent definition and add its `tools` and `mcpServers`
blocks to the matching file under `agents/`.
