# EVIDENCE_BEFORE_DONE.md

This file defines the evidence requirement for agent work.

It does not replace `PURIA.md`.

If this file conflicts with `PURIA.md`, stop, follow `PURIA.md`, and surface the conflict.

---

## Rule

Agents MUST NOT claim that work is done, fixed, tested, ready, validated, or safe to merge without evidence.

A claim without evidence is a failure.

A commit before evidence is a failure.

A push before evidence is a failure.

A ready-for-review PR without evidence is a failure.

---

## What counts as evidence

Evidence must be reproducible and relevant to the change.

Acceptable evidence includes:

- exact command output
- passing test output
- build output
- lint output
- HTTP status checks
- server log excerpts showing no errors
- Playwright traces, screenshots, or reports
- CI links
- generated artifacts
- manual verification checklist with exact observed results

Do not summarize evidence as “tested”.

Show the proof.

---

## Minimum evidence for code changes

For code changes, agents must show:

```sh
go build ./...
go test -race -count=1 ./...
go vet ./...
task lint
```

If the repository defines stronger validation, use the stronger validation.

If a command cannot run, say why and do not claim the work is validated.

---

## Minimum evidence for UI or route changes

For UI, template, route, navigation, handler, middleware, or layout changes, agents must additionally verify real page loads.

Evidence must include:

- affected page or route
- at least three adjacent pages or routes
- expected HTTP status
- actual HTTP status
- body or screenshot when relevant
- server log check for panic, internal error, template error, runtime error, and unexpected 404

Authenticated pages must be tested with a real authenticated session, a documented test session, Playwright login, or an explicit statement that authenticated smoke testing is not automated yet.

Agents MUST NOT claim authenticated pages were tested if only public pages were checked.

---

## Minimum evidence for integration changes

For external integrations, agents must show either:

- a real test against the configured service, or
- a local/mock integration test, or
- a clear statement that live validation was not performed

Secrets must never be printed in logs, prompts, commits, or PR bodies.

---

## PR evidence section

Every substantial PR must include:

```md
## Evidence

### Commands run

### Results

### Page or action checks

| Check | Expected | Actual | Result |
|---|---:|---:|---|

### Logs checked

- [ ] no panic
- [ ] no internal error
- [ ] no template error
- [ ] no unexpected 404

### What was not verified

### Human verification steps
```

---

## Failure behavior

If evidence is missing or incomplete, the agent must:

1. stop
2. report what is missing
3. avoid committing, pushing, merging, or recommending merge
4. create or update validation tasks if needed

---

## Final reminder

The agent's confidence is not evidence.

Only reproducible proof is evidence.
