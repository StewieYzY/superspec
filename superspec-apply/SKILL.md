---
name: superspec-apply
description: Phase 3 of SuperSpec workflow — execute implementation via subagent-driven development with delta spec injection. Uses plan.md (recommended/max gear) or tasks.md degradation (fastest gear). Use after plan (or propose if skipping plan phase).
---

# SuperSpec Apply (Phase 3)

Fusion: `subagent-driven-development` (discipline skeleton) + delta spec injection + DDD execution mode

## Trigger

User invokes `/superspec:apply`. Requires Phase 1 artifacts at minimum. Phase 2 plan.md is optional — its presence determines execution mode.

---

## Prerequisite (recommended/max gear)

```bash
git worktree add ../worktrees/<name> -b <branch-name>
```

Fastest gear: work directly on current branch, no worktree.

---

## Execution Baseline Judgment (FIRST STEP, must execute explicitly)

```
Check: does openspec/changes/<name>/plan.md exist?

  YES → plan.md mode (recommended/max gear)
      → use subagent review flow

  NO  → tasks.md degradation mode (fastest gear)
      → single agent, no subagent review
```

---

## plan.md Mode (Recommended / Max Gear)

### Controller Agent (per task)

```
1. Determine active change name
2. Read delta spec:
   find openspec/changes/<name>/specs -name "*.md" -exec cat {} \;
3. Read plan.md current task's full content
4. Construct implementer prompt (delta spec + task content)
5. Dispatch implementer agent
```

### Implementer Agent Prompt

```
<delta-spec>
{{DELTA_SPEC_CONTENT}}
</delta-spec>

<task>
{{CURRENT_TASK_FROM_PLAN_MD}}
</task>

Execution mode: Document-Driven Development (DDD)
1. Read <task> "Context Quick-Reference" to understand intent and constraints
2. Execute "Implementation Steps" sequentially, mark [x] after each step
   - Impl Hint is a navigation suggestion, not a mandate
   - If deviating from Impl Hint, explain reason in completion report
3. Verify against "Acceptance Criteria" for each Scenario:
   - [AUTO] → execute operation command directly, compare against expected
   - [SEMI] → execute precondition command first, then operation command
   - [MANUAL] → skip, note in concerns: operation steps and expected result
4. Encounter situation not covered by plan → report NEEDS_CONTEXT, do not self-extend

Discipline:
- verification-before-completion: must complete all AUTO/SEMI acceptance before declaring done
- Frequent commits: commit after each implementation step
- Do not do anything beyond the plan

Completion states (pick exactly one):
- DONE: all steps complete, all AUTO/SEMI scenarios verified
- DONE_WITH_CONCERNS: complete, has MANUAL items or other notes
- NEEDS_CONTEXT: encountered plan-uncovered situation, needs more info
- BLOCKED: blocked, cannot continue
```

### Review Flow

```
implementer agent (DDD + verification)
         ↓ completion report
spec compliance reviewer
  Baseline: delta spec Scenario THEN (NOT Impl Hint)
  Checks:
  - Every ADDED Requirement: is there corresponding implementation?
  - Every Scenario THEN: is it observably satisfied?
  - [MANUAL] Scenario: does concerns include manual verification note? (if yes, pass)
  - Any over-implementation / under-implementation / spec deviation?
         ↓ ✅
code quality reviewer
  Checks: code quality / architecture / edge cases / security / performance
         ↓ ✅
plan.md task status → DONE
```

### Spec Reviewer Prompt

```
<delta-spec>
{{DELTA_SPEC_CONTENT}}
</delta-spec>

<implementer-report>
{{IMPLEMENTER_COMPLETION_REPORT}}
</implementer-report>

Verification baseline: Requirements and Scenario THENs in <delta-spec>.

Check rules:
- ADDED Requirement: is there corresponding implementation? (do NOT check if implementation entry matches Impl Hint)
- MODIFIED Requirement: does implementation match updated behavior description?
- REMOVED Requirement: is old code cleaned up?
- Every Scenario THEN: is it observably satisfied after implementation?
- [MANUAL] Scenario: concerns include manual verification note → sufficient, do not mark as failed

Judgment basis: Scenario THEN observable results, NOT implementation path.
```

---

## tasks.md Degradation Mode (Fastest Gear)

```
Single agent, no subagent review
Execution baseline: tasks.md coarse-grained tasks

Acceptance strategy (runtime trial, no pre-classification):
1. Execute tasks.md tasks sequentially
2. After each task completes, scan specs/ for matching Scenarios:
   - Match by Requirement name keyword against task description
   - Found → attempt to construct verification command and execute:
     - Success → treat as AUTO, record pass
     - Failure or cannot construct command → mark as needing manual verification,
       do not block current task, aggregate for verify phase
   - Not found → skip Scenario-level acceptance, rely on verify phase fallback
3. After all tasks complete, proceed to verify

Note: fastest gear has no AUTO/SEMI/MANUAL pre-classification.
      Uses runtime trial: automate what can be automated, defer what cannot to verify.
      Completeness guaranteed by verify's 3-dimension verification as quality floor.
```

---

## Model Selection

| Task type | Model |
|-----------|-------|
| Mechanical implementation (simple CRUD, config) | cheap model |
| Integration, requires judgment | standard model |
| Architecture decisions, spec/quality review | capable model |

---

## Seam to Phase 4

```
plan.md tasks marked DONE (or tasks.md checkboxes [x])
→ Phase 4 verify reads plan.md + specs + design + main spec
```

---

## Discipline Skills Active

| Skill | When |
|-------|------|
| `verification-before-completion` | Before any "done" declaration |
| `subagent-driven-development` | Core execution discipline (plan.md mode) |
| `using-git-worktrees` | Prerequisite for recommended/max gear |
| `systematic-debugging` | On bug or test failure |
| `receiving-code-review` | On review feedback from spec/quality reviewer |
| `dispatching-parallel-agents` | When 2+ independent tasks available |
