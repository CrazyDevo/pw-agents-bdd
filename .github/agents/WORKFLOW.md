# Multi-Agent BDD Workflow

Specialized agents with small context windows. Start with the **orchestrator** — it routes to the right sub-agent.

```
User requirement
       │
       ▼
┌──────────────────────┐
│ playwright-bdd-      │  Captures REQ-XXX, manages gates
│ orchestrator         │
└──────────┬───────────┘
           │
     ┌─────┼─────┐
     ▼     ▼     ▼
 Planner  Generator  Healer
     │       │        │
     ▼       ▼        ▼
specs/   src/      src/
plans/   features/  (fixes)
```

## Agents

| Agent | Role | Context loaded |
|-------|------|------------------|
| `playwright-bdd-orchestrator` | Route, gates, requirement capture | specs/ only |
| `playwright-test-planner` | Browser exploration → test plan | requirement + app |
| `playwright-test-generator` | Compare specs → generate code | plan + src/ patterns |
| `playwright-test-healer` | Fix failing tests | failure log + relevant src/ |

## Specs Folder

All requirements and plans live under `specs/`:

| Path | Purpose |
|------|---------|
| `specs/requirements/REQ-XXX-*.md` | New requirements |
| `specs/plans/*.plan.md` | Approved Gherkin plans |
| `specs/archive/` | Superseded documents |

See [specs/README.md](../../specs/README.md) for naming and comparison workflow.

## End-to-End Flow

1. **Start orchestrator** — provide manual steps or reference a requirement
2. Orchestrator saves `specs/requirements/REQ-XXX.md` → asks to plan
3. **Planner** explores app → saves `specs/plans/<id>.plan.md`
4. User reviews plan → asks to generate
5. **Generator** compares requirement vs existing plans → generates tests
6. User asks to run → `npm run test:dev`
7. On failure → **Healer** fixes and re-runs

## Confirmation Gates

Every phase requires explicit user approval. Agents never auto-chain planner → generator → healer.

## Quick Start

```
@playwright-bdd-orchestrator

Test login and sidebar on dev:
1. Go to stock order management UI
2. Login with admin/admin via Keycloak
3. Click each sidebar link
```

Orchestrator will create the requirement file and guide you through planner → generator → run → heal.
