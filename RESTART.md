# Restart Durability

Date: 2026-08-20

## Note on Process Restart Durability

Process restart durability describes whether work survives when the running
process stops and starts again — whether from a crash, a deployment, a scaling
event, or an intentional restart. A durable process resumes from a known, valid
state instead of losing work or leaving the system inconsistent.

### Why it matters

Any long-running process will eventually be restarted. If in-progress work lives
only in memory, a restart drops it. Durability is what lets a process pick up
where it left off rather than starting over or, worse, corrupting shared state.

### Principles

- **Persist before acknowledging.** Write state to durable storage (disk, a
  database, or a queue) before reporting a step as complete, so a restart cannot
  lose an acknowledged result.
- **Make operations idempotent.** A restart may replay the last in-flight
  operation. Design steps so that repeating them produces the same result and
  causes no duplicate side effects.
- **Checkpoint long work.** Record progress at safe boundaries so a restarted
  process can resume from the last checkpoint instead of the beginning.
- **Recover to a valid state on startup.** On boot, load persisted state,
  reconcile any partially completed work, and refuse to proceed from a state
  that cannot be trusted.
- **Handle shutdown gracefully.** Where possible, catch termination signals,
  flush in-flight state, and stop accepting new work so restarts start from a
  clean point.

### For this repository

This is a test and demonstration sandbox and does not run a long-lived process,
so there is no durable state to protect here today. This note records the
expectations to apply if any process or persistent workflow is added later.
