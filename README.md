# Enterprise Playwright + Cucumber BDD Test Automation Framework

Production-grade test automation framework supporting UI, API, and E2E testing with BDD, multi-environment execution, parallel runs, and comprehensive reporting.

## Technology Stack

| Component | Technology |
|-----------|------------|
| Language | TypeScript |
| Automation | Playwright |
| BDD | Cucumber (Gherkin) |
| Reports | Allure, Cucumber HTML |
| Logging | Winston |
| Test Data | JSON, CSV, Faker |
| API | Playwright APIRequestContext |

## Project Structure

```
src/
├── config/           # Environment & configuration
├── features/         # Gherkin features & step definitions
├── pages/            # Page Object Model
├── api/              # API automation layer
├── hooks/            # Cucumber lifecycle hooks
├── support/          # World, Browser/Context/Page managers
├── utils/            # Logger, data generator, utilities
└── test-data/        # Static test data (JSON, CSV)
```

## Prerequisites

- Node.js 18+ (LTS recommended)
- npm 9+
- Java 8+ (required for Allure reports)

## Quick Start

```bash
# Install dependencies
npm install

# Install Playwright browsers
npx playwright install --with-deps

# Run smoke tests (DEV environment)
npm run test:smoke

# Run all tests
npm run test:dev
```

## Execution Commands

### Environment Selection

```bash
npm run test:dev      # Development (orangedev)
npm run test:test     # Test (orangetest)
```

### Browser Selection

```bash
npm run chrome        # Chromium
npm run firefox       # Firefox
npm run webkit        # WebKit (Safari)
```

### Test Suite Tags

```bash
npm run test:smoke        # @smoke tagged scenarios
npm run test:regression   # @regression tagged scenarios
npm run test:sanity       # @sanity tagged scenarios
```

### Parallel Execution

```bash
# 4 parallel workers
npm run test:parallel

# Custom worker count
cross-env WORKERS=5 npm run test:dev
```

### Reporting

```bash
# Generate and open Allure report
npm run report:allure

# Generate Allure report only
npm run report:allure:generate

# Generate Cucumber HTML report
npm run report:cucumber
```

Reports are stored under:
- `reports/allure/` — Allure HTML report
- `reports/cucumber/` — Cucumber HTML report
- `reports/allure-results/` — Raw Allure results
- `screenshots/` — Failure screenshots
- `logs/` — Execution logs

## Environment Configuration

Environment files are located in `src/config/environments/`:

| File | Environment | URL |
|------|-------------|-----|
| `dev.env` | Development | 
| `test.env` | Test | |

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `ENV` | Target environment | `dev` |
| `BASE_URL` | Application URL |  (dev) |
| `LOGIN_PATH` | Login route | `/login?callbackUrl=%2F` |
| `API_URL` | API base URL | Environment-specific host |
| `KEYCLOAK_HOST` | Keycloak identity host | `` |
| `USERNAME` | Test username | `` |
| `PASSWORD` | Test password | `` |
| `BROWSER` | Browser type | `chromium` |
| `HEADLESS` | Headless mode | `true` |
| `WORKERS` | Parallel workers | `4` |
| `TAGS` | Cucumber tag filter | — |

## Writing Tests

### Feature File (Gherkin)

```gherkin
@login @smoke
Feature: Login
  Scenario: Successful Login
    Given user navigates to login page
    When user enters valid credentials
    And clicks login button
    Then user should be redirected to dashboard
```

### Step Definitions

Step definitions live in `src/features/<domain>/<domain>.steps.ts`. Assertions belong in steps, not page objects.

### Page Objects

Extend `BasePage` and contain only page interaction logic:

```typescript
export class LoginPage extends BasePage {
  async login(username: string, password: string): Promise<void> {
    await this.fill(this.usernameInput, username);
    await this.fill(this.passwordInput, password);
    await this.click(this.loginButton);
  }
}
```

### API Tests

Use the API layer in step definitions:

```typescript
const response = await this.customerApi.createCustomer(customerData);
expect(response.status()).toBe(201);
```

## CI/CD Integration

Ready-to-use pipeline configurations:

| Platform | File |
|----------|------|
| GitHub Actions | `.github/workflows/playwright.yml` |
| GitLab CI | `.gitlab-ci.yml` |
| Jenkins | `Jenkinsfile` |

Pipeline stages: Install → Lint → Build → Test → Report Publish

## GitHub Agents

This repository uses **multiple specialized agents** with small context windows. Start with the orchestrator — it routes to planner, generator, or healer sub-agents.

| Agent | Purpose |
|-------|---------|
| `playwright-bdd-orchestrator` | Capture requirements, route sub-agents, confirmation gates |
| `playwright-test-planner` | Explore app → write plans to `specs/plans/` |
| `playwright-test-generator` | Compare specs → generate features, steps, page objects |
| `playwright-test-healer` | Fix failing tests |

**Specs folder:** New requirements go to `specs/requirements/`, test plans to `specs/plans/`. The generator compares new requirements against existing plans before generating code. See [specs/README.md](specs/README.md).

**Workflow:** Requirement → Plan → Compare → Generate → Run → Heal (user confirms each phase).

**Agent usage guides:**
- [Multi-agent workflow](.github/agents/WORKFLOW.md)
- [English — How to Use the Agents](.github/agents/README.md)
- [Türkçe — Agent Kullanım Kılavuzu](.github/agents/README.tr.md)

## Coding Standards

See [docs/CODING_STANDARDS.md](docs/CODING_STANDARDS.md) for detailed guidelines.

## License

MIT
