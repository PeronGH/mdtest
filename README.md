# mdtest

Markdown is a testing language. AI agents are the interpreter.

Write your tests in plain text. Describe what to do and what to expect. Tell your coding agent to run them. That's it.

## Why

Tests written as code are precise but expensive to write and maintain, especially during early development when everything is changing. Tests written as Markdown are cheap to write, easy to change, and readable by anyone.

An AI coding agent (Claude Code, Codex CLI, Gemini CLI) can read a Markdown file, execute the described steps, and tell you whether things worked. For the agent, it's manual testing. For you, it's automatic.

## Use as a Skill

If you use the [`skills`](https://www.npmjs.com/package/skills) CLI:

```bash
npx skills add PeronGH/mdtest
```

## Getting Started

1. Write a text file describing your tests.
2. Tell your agent to run them.

That's the whole workflow. All you need is an agent CLI.

### Example

```markdown
## New Project Setup

1. Run `mytool init myproject`
2. Verify a `myproject` directory was created
3. Verify the directory contains a config file with sensible defaults
4. `cd` into the directory and run `mytool status`
5. Verify the output shows the project is initialized and has no errors
```

Then tell your agent: "run the tests in `tests/smoke.md`."

The agent reads the file, does what it says, and reports back.

### Tips

**Be as precise as you need to be.** Vague assertions like "verify the output looks correct" are fine — that's the point, the agent uses judgment. But don't be so vague that a human tester couldn't follow the steps either. "Verify the page works" is too loose. "Verify the page title is 'Dashboard' and the welcome banner is visible" is specific when you need it to be. Match the precision to how much you care.

**Ask the agent to lint your tests.** Before running, ask: "review these test steps for ambiguity." The agent will flag vague assertions, implicit assumptions, and missing context.

**Promote to code when ready.** Once a feature stabilizes and a test becomes critical, convert it to a coded test. The agent can help with the conversion — it already understands what the test does.

## Advanced

### MCP Dependencies

With MCP, your agent can interact with real services and tools. If a test requires a specific MCP server, say so at the top of the file:

```markdown
# Checkout Flow Tests

This test requires the Playwright MCP server for browser interaction.
Install it from: https://github.com/anthropics/playwright-mcp

This test requires the Stripe MCP server for payment verification.
Install it from: https://github.com/stripe/agent-toolkit

## Purchase a Product

1. Open the browser and navigate to /products/widget-a
2. Click "Add to Cart"
3. Proceed to checkout
4. Enter test card number 4242 4242 4242 4242
5. Complete the purchase
6. Verify the Stripe dashboard shows a new payment for $29.99
```

If the agent doesn't have the required MCP server configured, it will ask you to install it before proceeding.

Some useful MCP servers for testing:

- [Playwright MCP](https://github.com/anthropics/playwright-mcp) — browser interaction
- [Stripe Agent Toolkit](https://github.com/stripe/agent-toolkit) — payment flows
- [Cloudflare MCP](https://github.com/cloudflare/mcp-server-cloudflare) — DNS and infrastructure

### Imports

Tests often share setup steps. Instead of repeating them, write them once and reference them:

```markdown
# Login Tests

Set up the environment based on `setup/environment.md`.
Set up a test user based on `setup/test-user.md`.

## Verify Login

1. Navigate to /login
2. Enter the test user credentials
3. Click "Sign In"
4. Verify redirect to /dashboard

## Verify Failed Login

1. Navigate to /login
2. Enter email "nobody@example.com" and password "wrong"
3. Click "Sign In"
4. Verify error message "Invalid credentials" is displayed
```

Where `setup/environment.md` might be:

```markdown
# Environment Setup

1. Start the development server with `npm run dev`
2. Verify it's running at http://localhost:3000
3. Reset the test database with `npm run db:reset`
```

The agent reads the referenced file and follows its steps before continuing. It's just natural language — you're telling the agent to go read another document, the same way you'd tell a colleague.

### CI

You can run Markdown tests in CI too. Use your agent's batch mode and have it signal pass/fail however suits your pipeline. For example:

```bash
result=$(claude --dangerously-skip-permissions \
  -p "$(cat tests/smoke.md)

## Output
Respond with only 'pass' or 'fail'.")

if [ "$result" != "pass" ]; then
  exit 1
fi
```

There's no prescribed way to do this. Use whatever fits your setup.

## FAQ

**What agent should I use?**

Any coding agent that can read files and follow instructions. Claude Code, Codex CLI, and Gemini CLI all work. If you have MCP servers configured, the agent will use them.

**Doesn't this replace real tests?**

No. It complements them. Markdown tests are a fast, cheap way to get coverage early. Coded tests are where stable, critical paths end up. Use both.
