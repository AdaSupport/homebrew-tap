---
name: ada-test-agent
description: Author and run test cases against an Ada agent with adacli's testing commands, then read results and metrics. Use when the user wants to test the agent, create test cases/suites, run a regression, or check whether a change improved behavior.
require-tools:
  - Bash
  - Read
---

# Testing an Ada agent

`adacli <instance> testing ...` manages test cases and runs so you can validate behavior
and catch regressions after changes.

## Inspect

```bash
adacli <instance> testing cases list --json     # existing test cases
adacli <instance> testing runs list --json      # past runs
adacli <instance> testing quota --json          # remaining run quota (runs cost budget)
adacli <instance> testing metrics --json        # metrics for runs
```

Run `adacli <instance> testing cases --help` and `testing runs --help` for the exact
create/get/update/delete options.

## Author a test case

```bash
adacli <instance> testing cases create --help   # see required fields, then:
adacli <instance> testing cases create ...       # persona, objective, eval criteria, hard-fails

# Or draft one from a real (failing) conversation. Every personal value is faked:
adacli <instance> testing cases from-conversation <conversation-id> --json   # draft + instructions
```

The draft's `instructions` field holds the steps that write, save and run the test case.
[test-case-from-conversation.md](test-case-from-conversation.md) has the same steps. The
command saves nothing.

## Run and read results

```bash
# Check quota first — runs are metered.
adacli <instance> testing quota --json
adacli <instance> testing runs create --cases tc-1 tc-2 --repeat 3 --json   # --repeat for a stable rate
adacli <instance> testing runs get <run-id> --json
adacli <instance> testing metrics --json

# Did a change raise the bar? Compare two run sets:
adacli <instance> testing runs compare --baseline <run-ids...> --candidate <run-ids...> --json
# → { summary.delta, newly_passing, regressed }
```

## Improve the agent

To improve the agent, use the **ada-optimize** skill.

## Rules

- **Check `testing quota` before creating runs** — they consume budget; don't burn it in loops.
- Tie tests back to findings from **ada-insights** (test the exact failures you found).
- After the tests pass on a config that is already live, snapshot it via **ada-config-snapshot**
  so the known-good state is committed.
- When the tests check a staged change set, snapshot only after the promotion of the change set
  succeeds. A snapshot before that records the live agent without the change. While the change
  is still staged, report the change set ID and the test results.
