---
name: superspec-plan
description: Phase 2 of SuperSpec workflow — refine coarse tasks.md into bite-size plan.md with implementation steps, impl hints, and acceptance criteria. Reads all artifacts + main spec, maps delta spec to DDD execution, classifies acceptance as AUTO/SEMI/MANUAL. Use after propose, before apply.
---

# SuperSpec Plan (Phase 2)

Fusion: `writing-plans` (bite-size + anti-placeholder discipline) + Delta Spec → DDD mapping

## Trigger

User invokes `/superspec:plan`. Requires Phase 1 artifacts (proposal.md, specs/, design.md, tasks.md) to exist.

---

## Input (read all in one pass)

```
proposal.md     → intent and success criteria
specs/          → delta spec (pure WHAT: Requirement + Scenario)
design.md       → technical decisions and constraints
tasks.md        → coarse-grained skeleton reference
main spec       → openspec/specs/ relevant capabilities (understand existing behavior, prevent delta duplication)
```

---

## Step 2a: Read Main Spec

```
Main spec exists (existing system) → read, ensure delta is true diff, no conflicts
Main spec does not exist (new project) → skip, continue normally
Main spec read fails → warn, continue (do not block)
```

---

## Step 2b: Delta Spec → DDD Mapping

DDD execution logic: read spec to understand behavior → decide implementation path (Impl Hint) → generate implementation steps → verify against Scenario for acceptance criteria.

### ADDED Requirement
```
Read Scenario WHEN/THEN → understand expected behavior
→ Based on design.md + existing code structure, infer Impl Hint (suggested implementation entry point)
→ Generate implementation steps
→ For each Scenario, generate acceptance criteria (classify AUTO/SEMI/MANUAL)
```

### MODIFIED Requirement
```
Compare old vs new behavior → understand change scope and impact
→ Generate Impl Hint (which files to modify, note impact scope)
→ Generate modification steps
→ Generate acceptance criteria (old vs new behavior comparison)
```

### REMOVED Requirement
```
Identify code implementing the Requirement
→ Generate cleanup steps (delete source + delete call sites)
→ Generate acceptance criteria (confirm entry point unreachable)
```

### RENAMED Requirement
```
Generate rename steps (global reference replacement)
→ Generate acceptance criteria (confirm no dangling references)
```

---

## Step 2c: Acceptance Classification

For each Scenario's acceptance criteria, classify:

| Category | Definition | Who executes |
|----------|-----------|--------------|
| **AUTO** | curl command / CLI command / test script | implementer executes directly |
| **SEMI** | requires service startup before command execution | implementer starts service + executes |
| **MANUAL** | requires human interaction (UI operation, third-party callback, external system) | implementer skips, reports DONE_WITH_CONCERNS |

### MANUAL Verification Chain (complete, no missing links)

```
implementer skips MANUAL Scenario
  → reports DONE_WITH_CONCERNS
  → concerns note: "Scenario X requires manual verification: <steps> / Expected: <result>"
      ↓
spec reviewer confirms Scenario is indeed manual-only
  → does NOT mark as failed
      ↓
verify phase aggregates all MANUAL items, outputs checklist
  → user confirms each item
  → after confirmation, writes openspec/changes/<name>/.manual-verified
      ↓
archive 5a reads .manual-verified to confirm persistent record exists
```

---

## Step 2d: Error Handling Rules

```
Impl Hint inference fails (cannot find suitable entry point)
  → Mark as "Impl Hint: TBD — implementer judges implementation entry point"
  → Acceptance criteria MUST still be generated (Scenario → acceptance is never optional)

plan.md generation partially fails (insufficient info for a task)
  → Write completed tasks
  → Mark failed task status as BLOCKED, note reason
  → Do NOT abort entire plan.md generation for a single task failure
```

---

## Step 2e: Generate plan.md (Core Output)

### plan.md Schema

```markdown
# Plan: <change-name>
> Recommended/max gear sole execution baseline.
> When uncertain, read referenced section by anchor — do not read entire files.
> Task status: TODO / IN_PROGRESS / DONE / BLOCKED

---

## Task 1: <task name>
Status: TODO
Maps to: tasks.md §1.1

### Context Quick-Reference
> Expand only when implementation is uncertain. Normally skip to steps and acceptance.

- Intent: <one sentence> → see proposal.md §<Section>
- Tech constraint: <one sentence> → see design.md §<Section>
- Requirement baseline: specs/<cap>/spec.md §ADDED: <Requirement Name>
- Existing behavior: openspec/specs/<cap>/spec.md §<Section> (if context needed)

### Implementation Steps
> Impl Hint: suggested entry <source_file_path>:<function_name>()
> Impl Hint is a navigation suggestion, not a mandate; if a better design choice exists, deviate and explain in completion report

- [ ] Step 1: <specific operation description>
      File: `<specific path>`
      Note: <unambiguous implementation key points, no TBD allowed>

- [ ] Step 2: <specific operation description>
      File: `<specific path>`
      Note: <unambiguous implementation key points>

- [ ] Step N: Commit
      Command: `git add <specific paths> && git commit -m "<message>"`

### Acceptance Criteria
> Derived from delta spec Scenario THEN, verify against observable results

- [ ] Scenario: <name> [AUTO]
      Operation: `<curl command / CLI command, concrete and executable>`
      Expected: <observable output matching THEN>

- [ ] Scenario: <name> [SEMI]
      Precondition: `<command to start service>`
      Operation: `<command to execute>`
      Expected: <observable output>

- [ ] Scenario: <name> [MANUAL]
      Operation: <human operation steps>
      Expected: <expected result>
      Note: requires manual verification, implementer reports DONE_WITH_CONCERNS

### Completion Condition
All implementation steps [x] + all AUTO/SEMI acceptance criteria [x]
+ MANUAL items noted in concerns → change status to DONE

---

## Task 2: <task name>
Status: TODO
...
```

### Forbidden in plan.md

- TBD in step content (Impl Hint TBD is the only exception)
- Steps without specific file paths
- Vague expected results
- Tasks without acceptance criteria

---

## Step 2f: Plan Self-Review

- [ ] **Spec coverage** — every delta spec Requirement has a corresponding task?
- [ ] **Placeholder scan** — step content has no TBD/vague descriptions (Impl Hint TBD excluded)?
- [ ] **Step completeness** — every step has file path + specific note?
- [ ] **Acceptance classified** — every Scenario has AUTO/SEMI/MANUAL label?
- [ ] **Acceptance executable** — AUTO/SEMI operation commands are concrete and executable, expected results are specific?
- [ ] **Anchor accuracy** — context quick-reference anchors point to correct sections?

**Output**: `openspec/changes/<name>/plan.md`

---

## Seam to Phase 3

```
plan.md exists in change directory
→ Phase 3 apply detects plan.md and uses subagent review mode
```

---

## Discipline Skills Active

| Skill | When |
|-------|------|
| `verification-before-completion` | Before declaring plan "done" |
| `writing-plans` | Core discipline for plan structure and anti-placeholder |
