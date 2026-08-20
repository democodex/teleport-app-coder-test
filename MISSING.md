# MISSING.md

Date: 2026-08-20

## Purpose

This document catalogs **observation gaps** in the
`teleport-app-coder-test` project — areas where the project currently lacks the
instrumentation, coverage, or safeguards needed to observe, verify, and diagnose
its own behavior.

Because this repository is a test and demonstration sandbox for the Teleport AI
coding agent (see [`README.md`](README.md)), it is intentionally minimal. The
gaps below are therefore not "bugs" to fix urgently, but a checklist of what the
project *would* need before it could be trusted as a place where agent behavior
is meaningfully observed, validated, and audited.

---

## 1. Test Coverage

**Current state:** There are no tests, no test runner, and no test directory in
the repository.

Gaps to address:

- [ ] No automated tests of any kind (unit, integration, or end-to-end).
- [ ] No test framework or runner configured (e.g. no `package.json`,
      `pytest`, `go test`, or CI test step).
- [ ] No verification that agent-applied changes preserve existing file
      structure or content invariants.
- [ ] No regression tests capturing known-good states of `README.md`,
      `AI_CHANGES.md`, and `NOTES.md`.
- [ ] No smoke test that confirms the repository is in a valid, readable state
      after an agent run.
- [ ] No coverage measurement or reporting, so "how much is exercised" is
      unknown.
- [ ] No fixtures or golden files to diff agent output against expected output.

## 2. Observability

**Current state:** The only record of activity is the manually/agent-appended
[`AI_CHANGES.md`](AI_CHANGES.md) log. There is no structured, queryable view of
what happened during a run.

Gaps to address:

- [ ] No structured event trail (the change log is free-form prose, not
      machine-parseable).
- [ ] No correlation/trace ID linking a prompt to the resulting file changes,
      commits, and outcome.
- [ ] No timeline or ordering guarantees beyond human-written timestamps.
- [ ] No way to reconstruct *why* a change was made from observability data
      alone.
- [ ] No distinction between "agent intended to do X" and "agent actually did
      X" — intent vs. effect is not captured.
- [ ] No health/liveness signal indicating whether an agent run completed,
      partially completed, or failed.

## 3. Metrics

**Current state:** No metrics are collected or emitted.

Gaps to address:

- [ ] No count of agent tasks run, succeeded, or failed.
- [ ] No measurement of task duration / latency.
- [ ] No count of files created, modified, or deleted per task.
- [ ] No measurement of change size (lines/bytes added or removed).
- [ ] No success-rate or error-rate metric over time.
- [ ] No metric emission format or backend (e.g. Prometheus, StatsD, OpenTelemetry).
- [ ] No baseline or SLO to compare metrics against.

## 4. Logging

**Current state:** [`AI_CHANGES.md`](AI_CHANGES.md) is the closest thing to a
log, but it is a curated changelog, not operational logging.

Gaps to address:

- [ ] No runtime/operational logs (only after-the-fact change summaries).
- [ ] No consistent log format or severity levels (debug/info/warn/error).
- [ ] No timestamps at a consistent precision or timezone convention across
      entries.
- [ ] No capture of the raw prompt, agent reasoning, or the exact commands/edits
      executed.
- [ ] No log retention, rotation, or archival policy.
- [ ] No separation between human-facing summaries and machine-facing logs.
- [ ] No log line linking back to the commit(s) it produced.

## 5. Error Handling

**Current state:** There is no code, and therefore no defined behavior for
failure conditions.

Gaps to address:

- [ ] No documented expectations for what happens when an agent task fails
      partway through.
- [ ] No record of failed or aborted tasks (the change log only shows
      applied changes).
- [ ] No rollback, revert, or recovery procedure defined for a bad change.
- [ ] No validation that requested changes are safe or well-formed before
      applying them.
- [ ] No guardrails against destructive edits (e.g. truncating or deleting
      key files).
- [ ] No surfacing of errors — failures may be silent and unobservable.
- [ ] No retry or idempotency guarantees for re-running the same task.

## 6. Monitoring & Alerting

**Current state:** Nothing monitors the repository or the agent's activity.

Gaps to address:

- [ ] No continuous integration to detect breakage on each change.
- [ ] No alerting when an agent run fails, stalls, or produces anomalous output.
- [ ] No dashboards summarizing agent activity or repository health.
- [ ] No drift detection comparing actual repository state to an expected
      baseline.
- [ ] No anomaly detection for unusually large or unexpected changes.
- [ ] No on-call/notification path — no one is told when something goes wrong.
- [ ] No uptime/availability tracking for whatever service exercises the agent.

---

## Suggested First Steps

If this project graduates from a bare sandbox toward a validated test harness,
a reasonable order to close these gaps is:

1. **Establish a baseline** — add golden/fixture files and a smoke test that
   verifies the repository is in a valid state.
2. **Add a test runner + CI** — so every change is automatically checked.
3. **Introduce structured logging** — machine-parseable events for each task,
   including the prompt, actions, and outcome.
4. **Emit basic metrics** — task counts, success/failure, duration, and change
   size, derived from the structured logs.
5. **Define error handling** — failure records, rollback procedure, and
   pre-apply validation guardrails.
6. **Wire up monitoring & alerting** — dashboards and notifications built on the
   metrics and logs above.

Closing these gaps in this order lets later layers (metrics, monitoring) build
on the structured foundation established by earlier ones (logging, tests).
