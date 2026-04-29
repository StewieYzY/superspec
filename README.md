# SuperSpec

> Spec-Driven Development with Iron-Law Discipline

SuperSpec fuses two frameworks:

- **OpenSpec** — Delta Spec artifact system (persistent, versionable requirements)
- **Superpowers** — Iron Law execution discipline (no skipping verification, no rationalizing shortcuts)

The result: a structured workflow where specs drive implementation, implementation is verified against specs, and specs evolve cleanly over time.

---

## Core Philosophy

```
Spec (WHAT)  →  Plan (HOW)  →  Implement  →  Verify  →  Archive
     ↑                                                      ↓
     └──────────────── Spec Evolution Cycle ────────────────┘
```

**WHAT and HOW are strictly separated.**

- Delta specs describe behavior only (Requirement + Scenario WHEN/THEN) — no file paths, no function names, no implementation hints
- `plan.md` carries the HOW — impl hints, step-by-step instructions, acceptance criteria
- Archive merges delta specs into main spec cleanly, with no HOW pollution

**Verify is the quality floor. No gear may skip it.**

---

## Skills Overview

| Skill | Phase | Purpose |
|-------|-------|---------|
| `superspec-explore` | 0 | Divergent thinking → convergent design |
| `superspec-propose` | 1 | Generate proposal, delta specs, design, tasks |
| `superspec-plan` | 2 | Refine tasks into bite-size plan with acceptance criteria |
| `superspec-apply` | 3 | Execute implementation via DDD + subagent review |
| `superspec-verify` | 4a | 3-dimension verification (Completeness / Correctness / Coherence) |
| `superspec-finish` | 4b | Git branch disposition (merge / PR / keep / discard) |
| `superspec-archive` | 5 | Merge delta spec into main spec, move to archive |

---

## Installation

Copy the skill folders into your project's `.claude/skills/` directory:

```
your-project/
└── .claude/
    └── skills/
        ├── superspec-explore/
        │   └── SKILL.md
        ├── superspec-propose/
        │   └── SKILL.md
        ├── superspec-plan/
        │   └── SKILL.md
        ├── superspec-apply/
        │   └── SKILL.md
        ├── superspec-verify/
        │   └── SKILL.md
        ├── superspec-finish/
        │   └── SKILL.md
        └── superspec-archive/
            └── SKILL.md
```

SuperSpec requires [OpenSpec](https://github.com/Fission-AI/OpenSpec) and [Superpowers](https://github.com/obra/superpowers) to be installed alongside.

---

## Three-Gear Speed Control

Choose the gear that fits the change size:

### Fastest — small changes, hotfixes

```
/superspec:propose → /superspec:apply → /superspec:verify → /superspec:archive
```

- Skips: explore, plan, subagent review, finish
- Does NOT skip: verify
- apply runs in single-agent mode using `tasks.md` as execution baseline
- Coherence check is minimal (test suite only, no main spec cross-check)

### Recommended — new features, meaningful changes

```
/superspec:explore → /superspec:propose → /superspec:plan
→ /superspec:apply → /superspec:verify → /superspec:finish → /superspec:archive
```

- Full workflow, all phases
- apply runs in subagent mode with spec + quality review
- Coherence check is pragmatic (`git diff --name-only` → affected Requirements → human judgment)

### Maximum — architecture changes, core features

```
Same as recommended + no steps skipped within each phase
+ every review loop runs to completion
```

- Coherence check is complete (full test suite + all main spec Requirements)
- Every subagent review cycle must pass before proceeding

> **Principle**: The path is flexible, quality is not. Verify is the floor at every gear.

---

## File Roles

| File | Created in | Role during execution | Role in archive |
|------|-----------|----------------------|-----------------|
| `investigation.md` | Phase 0 (optional) | Context input for Phase 1 | Reference record |
| `proposal.md` | Phase 1 | Referenced by plan.md (intent) | Audit record |
| `specs/<cap>/spec.md` | Phase 1 | Pure WHAT — injected into agents | Merges into main spec |
| `design.md` | Phase 1 | Referenced by plan.md (constraints) | Audit record |
| `tasks.md` | Phase 1 | Coarse skeleton; fastest-gear execution baseline | Completion state record |
| `plan.md` | Phase 2 | **Sole execution baseline** (recommended/max) | HOW record — stays in archive |
| `.manual-verified` | Phase 4a | Persistent MANUAL acceptance confirmation | Stays in archive |

---

## Key Concepts

### Delta Spec

A delta spec describes only **what changes** in system behavior — not how to implement it.

```markdown
## ADDED Requirements
### Requirement: User can export data
The system SHALL allow users to export current view as CSV.

#### Scenario: Successful export
- WHEN authenticated user clicks "Export CSV"
- THEN browser downloads a valid CSV file containing current view data
```

**Never include** in delta spec: file paths, function names, impl hints, `GIVEN` keyword, TBD markers.

### plan.md Structure

Each task in `plan.md` is self-contained with lazy context loading:

```markdown
## Task 1: Implement CSV export endpoint
Status: TODO

### Context Quick-Reference
> Expand only when uncertain. Otherwise go straight to steps.
- Intent: → see proposal.md §Why
- Tech constraint: → see design.md §API Design
- Requirement baseline: specs/data/spec.md §ADDED: User can export data

### Implementation Steps
> Impl Hint: src/export.ts:exportToCSV() (suggestion, not mandate)
- [ ] Step 1: Create export handler ...
- [ ] Step N: Commit

### Acceptance Criteria
- [ ] Scenario: Successful export [AUTO]
      Operation: `curl -H "Authorization: Bearer $TOKEN" /api/export`
      Expected: 200 response with Content-Type: text/csv
```

### WHAT / HOW Separation

| Location | Contains | Example |
|----------|----------|---------|
| `specs/<cap>/spec.md` | WHAT (behavior) | "System SHALL allow CSV export" |
| `plan.md` | HOW (implementation) | `src/export.ts:exportToCSV()` |
| `openspec/specs/` (main spec) | WHAT (current system truth) | All merged Requirements |

This separation ensures main spec stays clean after archive merge — no stale file paths or function names.

### Acceptance Classification

| Type | When | Who verifies |
|------|------|-------------|
| `[AUTO]` | curl / CLI / test script | Implementer agent |
| `[SEMI]` | Needs service running first | Implementer agent (after startup) |
| `[MANUAL]` | UI interaction, third-party callback | Human, confirmed in verify phase |

MANUAL items are tracked through a complete chain:
`DONE_WITH_CONCERNS` → spec reviewer confirms → verify aggregates → user confirms → `.manual-verified` written → archive checks file exists.

---

## Discipline Skills Referenced

SuperSpec relies on these Superpowers skills for its execution discipline:

| Skill | When active |
|-------|------------|
| `verification-before-completion` | Before any "done" declaration — every phase |
| `systematic-debugging` | On bug or test failure |
| `subagent-driven-development` | Core execution in apply phase (plan.md mode) |
| `using-git-worktrees` | Prerequisite for recommended/max gear apply |
| `receiving-code-review` | On review feedback from spec/quality reviewer |
| `dispatching-parallel-agents` | When 2+ independent tasks are available |

---

## Workflow Diagram

```
Phase 0: superspec:explore (optional)
  ┌─────────────────────────────────┐
  │ 0a: Divergent (stance)          │
  │   recon → Socratic questions    │
  │   soft checkpoint: continue?    │
  │                                 │
  │ 0a→0b: Create change directory  │
  │   ask name → openspec new change│
  │   write investigation.md        │
  │                                 │
  │ 0b: Convergent (HARD-GATE)      │
  │   declare confirmed dims        │
  │   3-block design → confirm each │
  └────────────────┬────────────────┘
                   │
Phase 1: superspec:propose
  ┌─────────────────────────────────┐
  │ read inputs (investigation.md   │
  │   / conversation / description) │
  │ idempotent dir creation         │
  │ generate DAG artifacts:         │
  │   proposal → specs + design     │
  │            → tasks              │
  │ self-review → user review gate  │
  └────────────────┬────────────────┘
                   │
Phase 2: superspec:plan
  ┌─────────────────────────────────┐
  │ one-pass read all artifacts     │
  │   + main spec (skip if absent)  │
  │ delta spec → DDD mapping        │
  │   ADDED/MODIFIED/REMOVED/RENAMED│
  │ classify acceptance: AUTO/SEMI/ │
  │   MANUAL                        │
  │ generate plan.md with anchors   │
  │ self-review                     │
  └────────────────┬────────────────┘
                   │
Phase 3: superspec:apply
  ┌─────────────────────────────────┐
  │ detect plan.md existence        │
  │                                 │
  │ plan.md mode:                   │
  │   worktree → per task:          │
  │   controller injects delta spec │
  │   implementer (DDD)             │
  │   → spec reviewer (THEN-based)  │
  │   → quality reviewer            │
  │   → task DONE                   │
  │                                 │
  │ tasks.md mode (fastest):        │
  │   single agent, runtime trial   │
  │   acceptance, verify fallback   │
  └────────────────┬────────────────┘
                   │
Phase 4a: superspec:verify
  ┌─────────────────────────────────┐
  │ Completeness: tasks + reqs +    │
  │   AUTO/SEMI + MANUAL checklist  │
  │ Correctness: spec match + THEN  │
  │ Coherence: gear-based strategy  │
  │   max: full / rec: git-diff /   │
  │   fast: test suite only         │
  │ MANUAL confirmed → .manual-verified│
  │ CRITICAL blocks 4b              │
  └────────────────┬────────────────┘
                   │ no CRITICAL
Phase 4b: superspec:finish
  ┌─────────────────────────────────┐
  │ run tests → determine base      │
  │ 4 options: merge/PR/keep/discard│
  │ cleanup worktree                │
  └────────────────┬────────────────┘
                   │
Phase 5: superspec:archive
  ┌─────────────────────────────────┐
  │ check: tasks done +             │
  │   .manual-verified present      │
  │ delta → main spec merge         │
  │   RENAMED→REMOVED→MODIFIED→ADDED│
  │ show diff → user confirms       │
  │ move to archive/YYYY-MM-DD-name │
  │ plan.md stays in archive (HOW)  │
  └─────────────────────────────────┘
```

