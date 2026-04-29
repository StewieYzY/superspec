---
name: superspec-verify
description: Phase 4a of SuperSpec workflow — 3-dimensional verification (Completeness, Correctness, Coherence) with MANUAL item aggregation and persistence. Quality floor that no gear may skip. Use after apply, before finish.
---

# SuperSpec Verify (Phase 4a)

## Trigger

User invokes `/superspec:verify`. Requires Phase 3 to have completed (plan.md tasks DONE or tasks.md checkboxes [x]).

## Input

```
plan.md (if exists) + specs/ + design.md + openspec/specs/ (main spec)
```

---

## 3-Dimension Verification

### 1. COMPLETENESS (完整性)

```
Check:
- All plan.md tasks are DONE? (fastest gear: all tasks.md checkboxes [x]?)
- Every delta spec Requirement has corresponding implementation?
- Every AUTO/SEMI Scenario acceptance criteria verified and passed?

Aggregate MANUAL items:
- Collect all MANUAL Scenarios across all tasks
- Output checklist for user confirmation:

  === 需人工确认的验收项 ===
  [ ] Scenario: <name> (Task X)
      操作: <具体步骤>
      期望: <期望结果>
  请逐项操作并确认。
```

### 2. CORRECTNESS (正确性)

```
Check:
- Implementation matches delta spec Requirement descriptions?
- Each Scenario THEN is observably satisfied?
- Edge cases and error states handled per spec?
```

### 3. COHERENCE (一致性) — by gear

| Gear | Strategy |
|------|----------|
| **Max** (complete) | Run full test suite. Check ALL main spec Requirements — confirm unchanged behavior not broken. |
| **Recommended** (pragmatic) | `git diff --name-only` → get modified file list. Agent assists to find Requirements these files touch. Human judges: is unchanged behavior preserved? Run affected test subset. |
| **Fastest** (minimal) | Run test suite (if available). No main spec cross-check. |

**Pragmatic Coherence note**: Requirement mapping is agent-assisted inference, NOT authoritative. Final judgment always by human.

---

## MANUAL Verification Persistence

After user confirms all MANUAL items:

```bash
cat > openspec/changes/<name>/.manual-verified << EOF
confirmed_at: <timestamp>
confirmed_scenarios:
  - <Scenario name 1> (Task X)
  - <Scenario name 2> (Task Y)
EOF
```

This file is the persistent record that archive phase 5a reads.

---

## Result Classification

| Level | Meaning | Action |
|-------|---------|--------|
| **CRITICAL** | Must fix | Blocks Phase 4b finish. Must resolve before proceeding. |
| **WARNING** | Should fix | User confirms to continue, or fixes first. |
| **SUGGESTION** | Optional improvement | Does not block flow. |

---

## tasks.md Sync

Map plan.md task completion status → tasks.md corresponding checkboxes:

```
plan.md Task 1: DONE  → tasks.md §1.1: [x]
plan.md Task 2: DONE  → tasks.md §1.2: [x]
...
```

---

## Output

Verification report with CRITICAL/WARNING/SUGGESTION classification.

```
CRITICAL  → must fix, do NOT enter Phase 4b
WARNING   → suggest fix, user confirms to continue
SUGGESTION → optional, does not block
```

---

## Seam to Phase 4b

```
No CRITICAL issues + MANUAL items confirmed → .manual-verified written
→ Phase 4b finish proceeds
```

---

## Discipline Skills Active

| Skill | When |
|-------|------|
| `verification-before-completion` | Before declaring verification "done" |
| `systematic-debugging` | If CRITICAL issue found, investigate root cause |
