# AGENTS.md

# xxksu_susfs_patch — Agent Instructions

This repository is maintained with AI coding agents.

The primary goals of these instructions are:

* preserve established project semantics
* minimize unnecessary token/context usage
* avoid repeated repository-wide analysis
* make small, auditable changes
* stop when the requested task is complete

---

## 1. Default Operating Mode

Work narrowly.

Do not perform a full repository audit unless explicitly requested.

For normal tasks:

1. inspect `git status`
2. inspect the relevant files only
3. inspect nearby tests only
4. make the smallest correct change
5. run focused validation
6. inspect the final diff
7. stop

Do not rediscover the entire project architecture on every task.

---

## 2. Token / Context Budget Rules

Minimize context consumption aggressively.

### Prefer

* `git status --short`
* `git diff --stat`
* `git diff -- <specific-files>`
* `git log --oneline -10`
* targeted `grep` / `rg`
* reading specific line ranges
* focused tests
* existing project documentation

### Avoid unless required

* dumping entire large files
* `git log --all` with large history
* full-tree recursive inspection
* reading every test file
* repeatedly reopening files already inspected
* repeating repository architecture summaries
* broad speculative investigation
* running every test after a small isolated change

When a command produces large output, narrow the query before continuing.

Do not spend tokens proving facts already established by Git, tests, or current project documentation.

---

## 3. Start-of-Task Procedure

For a normal development task, begin with only:

```sh
git status --short
git branch --show-current
git log --oneline -8
```

Then inspect files directly relevant to the task.

If a `HANDOVER.md` exists and the task is explicitly a resumed/provider-handoff session, read it once before changing code.

Do not repeatedly read `HANDOVER.md` during the same session.

If no handover is needed, do not consume context reading historical reports.

---

## 4. Repository Purpose

`xxksu_susfs_patch` exists to generate and validate deterministic
xxKSU/SUSFS kernel patch outputs from known semantic inputs.

The project is NOT primarily responsible for compiling complete kernels.

Full kernel compilation/runtime verification may be handled by external
CI/workflows.

Do not introduce full-kernel downloads or builds into normal patch
generation unless explicitly requested.

---

## 5. Semantic Safety

Correct semantics are more important than making a patch apply.

Never treat:

```text
git apply succeeds
```

as proof that a generated patch is semantically correct.

Preserve established distinctions between supported patch profiles,
including profile 11 and profile 51.

Do not merge profile behavior merely to reduce implementation complexity.

Do not replace semantic matching with fragile line-number or textual
shortcuts when the existing project has structured matching logic.

Unknown or ambiguous semantic state must remain explicit.

Do not silently guess.

---

## 6. Existing Architecture First

Before introducing a new abstraction:

1. search for the existing implementation
2. check whether the repository already has a model/schema/helper for it
3. extend the existing mechanism when practical

Avoid parallel implementations of the same concept.

Do not refactor unrelated code while fixing a focused issue.

Do not redesign completed project phases unless a concrete defect requires
it.

---

## 7. File Reading Discipline

Read only what is needed.

Recommended sequence:

```text
target file
→ referenced helper
→ relevant test
→ relevant schema/model
```

Stop once enough evidence exists to make the change safely.

Do not recursively inspect every caller/callee unless behavior is genuinely
unclear.

For large files, locate symbols first with `rg`, then read the surrounding
region.

Example:

```sh
rg -n "symbol_name|related_name" path/
```

Then inspect only matching regions.

---

## 8. Git History Discipline

Use Git history as evidence, not as a default reading exercise.

Prefer:

```sh
git log --oneline -10
git log -p -1 -- path/to/file
git blame -L start,end path/to/file
```

Only investigate deeper history when needed to understand intent or a
regression.

Do not dump dozens or hundreds of historical commits into context.

---

## 9. Modification Rules

Make minimal changes.

Do not:

* rewrite unrelated files
* reformat whole files unnecessarily
* rename APIs without need
* remove compatibility paths without proving they are obsolete
* introduce speculative future architecture
* modify generated baselines casually
* hide UNKNOWN states
* weaken validation to make tests pass

Preserve deterministic output.

A baseline change must invalidate any verification tied to the old baseline
where applicable.

---

## 10. Validation Strategy

Use the smallest validation set that can prove the requested change.

### Small isolated change

Run:

* directly relevant unit test(s)
* syntax/type check if applicable
* focused diff review

### Cross-cutting semantic change

Run:

* directly affected unit tests
* profile-specific tests
* semantic/invariant checks
* broader test suite only if justified

### Release/checkpoint task

Run the project's established full validation suite.

Do not run expensive validation repeatedly when no relevant code changed
since the previous successful run.

Record the final validation command and result.

---

## 11. Failure Handling

Do not bypass failures merely to complete the task.

If a test fails:

1. determine whether the failure is caused by the current change
2. inspect the smallest relevant area
3. fix the root cause if within task scope

Do not:

* disable tests
* weaken assertions
* convert failures to warnings
* add fake fallback data
* silently skip unsupported cases
* replace UNKNOWN with a guessed value

If a real blocker exists, report it precisely and stop.

---

## 12. Commit Discipline

Do not commit unless the task explicitly requests commits or the active
workflow requires a checkpoint.

When committing:

* keep commits logically scoped
* avoid mixing implementation and unrelated cleanup
* use the repository's established signing policy
* inspect the staged diff before commit

Recommended:

```sh
git diff --cached --stat
git diff --cached
```

After commit:

```sh
git status --short
git log -1 --oneline
```

Do not push unless explicitly requested.

---

## 13. Documentation Discipline

Markdown reports, audits, and handover files may be local-only depending on
`.gitignore`.

Do not automatically create large reports after ordinary tasks.

Create documentation only when:

* explicitly requested
* required for a provider/session handoff
* required to explain a durable project invariant
* required by the current project phase

Keep temporary AI reasoning out of tracked documentation.

`AGENTS.md` is the persistent agent operating contract and should remain
concise.

---

## 14. Handover Sessions

When explicitly preparing a provider/session handoff:

1. freeze development
2. inspect current Git state
3. validate completed work
4. commit completed implementation if requested
5. document unfinished work accurately
6. create/update `HANDOVER.md`
7. stop

Do not start the next development phase during handover preparation.

A handover must distinguish:

* COMPLETE
* WIP
* BLOCKED
* UNKNOWN

Never convert WIP into COMPLETE merely to obtain a clean tree.

---

## 15. Resume Sessions

When resuming from a handover:

1. read `AGENTS.md`
2. read `HANDOVER.md` once
3. run `git status --short`
4. verify current HEAD
5. run the handover's stated minimum validation
6. continue only the stated next step

Do not re-plan the entire project unless the repository evidence contradicts
the handover.

---

## 16. Communication Style

Keep progress reports short.

Do not narrate every shell command.

Report only:

* meaningful finding
* actual blocker
* completed checkpoint
* validation result

Avoid repeating information already stated earlier in the session.

For routine work, the final response should normally contain:

```text
Status
Changes
Validation
Git state
Next step
```

Keep it concise.

---

## 17. Stop Conditions

Stop immediately when the requested scope is complete.

Do not continue into:

* the next phase
* optional refactors
* cleanup unrelated to the request
* speculative enhancements
* extra documentation
* additional commits

unless explicitly requested.

If all acceptance criteria pass, report success and stop.

---

## 18. High-Cost Operations

The following require explicit justification before execution:

* full repository architecture audit
* full Git history analysis
* downloading complete kernel source trees
* full kernel compilation
* broad upstream comparison
* regeneration of all baselines
* regeneration of all profiles
* full test matrix
* large automated refactor

Prefer a targeted alternative whenever one can answer the current question.

---

## 19. Core Principle

Use the minimum amount of repository context required to make a correct,
reviewable change.

Correctness first.

Then minimize:

* files read
* history read
* commands executed
* tests executed
* generated output
* conversation tokens

Do not trade semantic correctness for token savings.
