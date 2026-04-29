---
name: superspec-archive
description: Phase 5 of SuperSpec workflow — merge delta specs into main spec, move change to archive directory. Ensures main spec stays clean (pure WHAT) and plan.md stays as HOW record in archive. Use after finish completes.
---

# SuperSpec Archive (Phase 5)

Fusion: `opsx:archive` + delta spec merge logic

## Trigger

User invokes `/superspec:archive`. Requires Phase 4b finish to have completed.

---

## Step 5a: Completion Check

```
Check:
- All plan.md tasks DONE? (fastest gear: all tasks.md checkboxes [x]?)
- .manual-verified file exists? (required when MANUAL items were present)
  - Exists → read confirmed scenario list, reconcile against MANUAL Scenarios
  - Does NOT exist but MANUAL items present → WARN, require user to complete verify Phase 4a MANUAL confirmation first
- tasks.md synced with plan.md completion status?

Incomplete state:
  → WARN + ask user: "Completion check shows gaps. Force archive anyway?"
  → User confirms → proceed
  → User declines → abort
```

---

## Step 5b: Delta Spec → Main Spec Merge

### Operation Order (important, prevents conflicts)

```
1. RENAMED → rename requirement header in main spec
2. REMOVED → delete requirement block from main spec
3. MODIFIED → replace requirement block in main spec
4. ADDED   → append requirement block to main spec
```

### Merge Process

```
1. Read delta spec(s): openspec/changes/<name>/specs/**/*.md
2. Read main spec(s): openspec/specs/**/*.md
3. Compute merge operations in RENAMED → REMOVED → MODIFIED → ADDED order
4. Display diff of proposed changes to main spec
5. Ask user: "Apply these changes to main spec?"
6. User confirms → execute merge
7. User declines → abort
```

### Merge Safety Guarantee

Delta spec contains only pure WHAT (Requirement + Scenario WHEN/THEN). No Impl Hints, no file paths, no function names. Merged main spec stays clean — format fully compatible.

---

## Step 5c: Archive Directory Movement

```
Move: openspec/changes/<name>/
   → openspec/changes/archive/YYYY-MM-DD-<name>/

Retention rules:
- plan.md → stays in archive directory (HOW record, does NOT enter main spec)
- .manual-verified → stays in archive directory (persistent MANUAL confirmation record)
- proposal.md → stays in archive directory (audit record)
- design.md → stays in archive directory (audit record)
- specs/ → stays in archive directory (delta spec record)
- tasks.md → stays in archive directory (completion state record)
```

---

## Spec Evolution Cycle (completed)

```
main spec describes current behavior (pure WHAT, clean)
       ↓
change proposes delta (ADDED/MODIFIED/REMOVED, pure WHAT)
       ↓
plan.md generates HOW (Impl Hint + acceptance criteria, not in main spec)
       ↓
implementation lands (DDD: read spec → implement → verify Scenario)
       ↓
verify 3-dimension + MANUAL items user confirmed → .manual-verified
       ↓
archive merge delta → main spec (clean merge)
       ↓
main spec describes new behavior → baseline for next change
```

---

## Discipline Skills Active

| Skill | When |
|-------|------|
| `verification-before-completion` | Before declaring archive "done" |
