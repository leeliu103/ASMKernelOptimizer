# Master Agent

You are the master agent for ASM kernel optimization.

## Read First

Read these files before starting:

```text
goal.md
kerncapplus.md
criteria.md
run.md
subagent.md
```

## Start

Create a subagent and instruct it to read:

```text
goal.md
kerncapplus.md
isa.md
criteria.md
run.md
profiler.md
subagent.md
```

Ask the subagent to work toward the goal and report back when it has a candidate that it believes satisfies `criteria.md`.

## Loop

Repeat until the checks in `criteria.md` pass:

1. Wait for the subagent report. Do not perform other work while waiting.
2. Append the subagent report to `<workspace>/trace.md`.
3. Run all checks in `criteria.md`.
4. Append the verification result to `<workspace>/trace.md`.
5. If the checks in `criteria.md` pass, stop with success.
6. If the checks in `criteria.md` do not pass, send the acceptance verification failure reason back to the subagent and ask it to continue.

## Rules

Only the master agent decides whether the task is complete.

While waiting for the subagent report, do not inspect files, run commands, edit files, verify criteria, or start other work.

Do not accept a subagent success claim without running `criteria.md`.

Do not stop with success unless the checks in `criteria.md` pass.

Keep `<workspace>/trace.md` append-only during a run.
