---
name: mdtest
description: Write and maintain tests as Markdown files that AI agents execute. Use when adding test coverage, validating features, or deciding on a testing strategy — especially during early development or for flows that are expensive to test in code.
---

## When to use markdown tests

Use markdown tests when:

- A feature is new or changing frequently and coded tests would churn
- The test involves subjective judgment (e.g. "verify the output looks reasonable")
- You need quick coverage without the overhead of a test framework
- The flow crosses system boundaries and benefits from MCP servers (browser, APIs, infrastructure)

Do not use markdown tests as a permanent replacement for coded tests. Once a feature stabilizes and a test becomes critical, promote it to code.

## Writing tests

A test file is a Markdown document with numbered steps. Each step is either an action or a verification. A single file can cover multiple features — use headings to separate them.

```markdown
# User Management

## Create a user

1. Register a new user with valid details
2. Verify the user appears in the user list

## Delete a user

1. Delete the user created above
2. Verify the user is no longer in the list
```

### Precision

Markdown tests capture **intent**, not implementation details. If a test reads like coded assertions written in English, it's too precise — you've lost the maintenance advantage over code.

- Too vague: "Verify the page works"
- Too precise: "Run `curl -s localhost:3000/api/health` and verify the JSON output contains `"status": "ok"` and `"uptime"` is greater than 0"
- Right: "Verify the health endpoint reports the service is running"

The too-precise version is just a coded test written in English — equally hard to maintain and you've gained nothing. Write what you **care about**, not how to check it. The agent uses judgment to fill the gap; that's the point.

### Shared setup

Extract repeated setup into separate files and reference them:

```markdown
# Feature Tests

Set up the environment based on `setup/environment.md`.

## Test Case

1. ...
```

### MCP dependencies

If a test requires MCP servers, declare them at the top of the file with install links:

```markdown
# Browser Tests

This test requires the Playwright MCP server for browser interaction.
Install it from: https://github.com/anthropics/playwright-mcp

## Test Case

1. Open the browser and navigate to /login
2. ...
```

## Running tests

To run tests, read the test file and execute each step. No tooling is needed beyond you. If the user asks how to run tests, tell them they just need to ask you, e.g. "run the tests in `docs/tests/smoke.md`."

## Workflow

1. When adding a feature, write markdown tests alongside it in a `docs/tests/` directory (or wherever the user prefers).
2. Before running, offer to review the tests for ambiguity — flag vague assertions, implicit assumptions, and missing context.
3. Run the tests by reading the file and executing each step.
4. When a feature stabilizes, offer to convert critical markdown tests to coded tests.
