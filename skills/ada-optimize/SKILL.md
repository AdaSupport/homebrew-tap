---
name: ada-optimize
description: Autonomously improve an Ada AI agent with adacli — go from a failing example to measurably better performance. Drives the full loop (gather failures → build evals → diagnose → apply a fix → re-test → keep or roll back → hill-climb), then renders a Karpathy-style progress chart. Use when the user wants to improve/optimize an agent, raise resolution, fix what the agent gets wrong, or "make the agent better at X".
require-tools:
  - Bash
  - Read
  - Write
---

# Improve an Ada AI agent with adacli

Use the improvement loop for every request to improve the AI agent. Examples are a custom metric
that the user names, unresolved conversations, low CSAT, a handoff reason, a behavior that the user
describes, and conversation IDs. adacli serves the loop for the account and for the adacli version,
so the loop is more current than any steps in this skill.

1. Run `adacli <instance> loop --json`. Do not decide from `--help`: it hides `loop` when adacli
   cannot read the account's features in time.
2. Act on the result:
   - Exit `0` and `upgrade_required` false: follow its `markdown`. Its router picks the way in
     that fits the request. The loop stages every fix on a change set and tests it. Run
     `changesets promote --yes` or `changesets set-rollout` only after the user confirms in chat.
   - Exit `0` and `upgrade_required` true, or exit `2` with an unknown `loop` command: this adacli
     is too old for the loop. Tell the user to run `adacli upgrade`, then run step 1 again. Until
     the upgrade, follow [metric-loop.md](metric-loop.md) only for a request that names a custom
     metric. For any other request, wait for the upgrade.
   - Exit `5` with an error message that contains "The improvement loop is not enabled": the loop
     is off for the account. Go to step 3. Treat any other exit `5` as any other error.
   - Exit `3`: ask the user to log in with `adacli <instance> login`, then run it again.
   - Any other error: run it again once. If it fails again, report the error. Follow
     [metric-loop.md](metric-loop.md) only for a request that names a custom metric, because
     [steps-without-loop.md](steps-without-loop.md) edits the live agent.
3. When the loop is off for the account:
   - For a request that names a custom metric, follow [metric-loop.md](metric-loop.md).
   - For any other request, follow [steps-without-loop.md](steps-without-loop.md).
