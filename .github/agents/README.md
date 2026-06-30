# GitHub Agents — Usage Guide

This project uses **four specialized agents** plus a lightweight orchestrator. Each agent loads only the context it needs.

## Agents

| Agent | Invoke when… |
|-------|--------------|
| **`playwright-bdd-orchestrator`** | Starting any new QA task (always start here) |
| `playwright-test-planner` | You have a requirement and need a test plan |
| `playwright-test-generator` | Plan is approved — compare specs and generate tests |
| `playwright-test-healer` | Tests are failing |

## Recommended Flow

```
1. @playwright-bdd-orchestrator  →  capture requirement
2. @playwright-test-planner      →  explore + write plan to specs/plans/
3. Review plan manually
4. @playwright-test-generator    →  compare vs existing plans, generate code
5. Run: npm run test:dev
6. @playwright-test-healer       →  fix failures (if any)
```

## Specs Folder

Before generating tests, requirements and plans are stored under `specs/`:

- `specs/requirements/` — what to test (from stakeholders)
- `specs/plans/` — how to test (Gherkin + framework mapping)

The generator **compares new requirements against existing plans** before writing code. See [specs/README.md](../../specs/README.md).

## Example Prompts

### New feature (via orchestrator)

```
@playwright-bdd-orchestrator

New requirement: verify stock order creation form on dev.
Steps:
1. Login as admin
2. Navigate to Create Resources
3. Fill required fields and submit
```

### Generate from existing plan

```
@playwright-test-generator

Compare and implement tests from specs/plans/login-dashboard.plan.md
```

### Fix failures

```
@playwright-test-healer

Login scenario failed with Keycloak redirect timeout. Fix and re-run @smoke.
```

## Test Commands

```bash
npm run test:dev          # All tests, dev environment
npm run test:test         # Test environment
npm run test:smoke        # @smoke tag only
cross-env TAGS=@login npm run test:dev
cross-env WORKERS=1 npm run test:dev   # Debug mode
```

## Documentation

- [Multi-agent workflow](WORKFLOW.md)
- [Specs folder guide](../../specs/README.md)
- [Coding standards](../../docs/CODING_STANDARDS.md)
- [Türkçe kılavuz](README.tr.md)
