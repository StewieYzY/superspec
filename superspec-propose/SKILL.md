---
name: superspec-propose
description: Phase 1 of SuperSpec workflow — generate a complete change proposal with proposal.md, delta specs, design.md, and tasks.md. Includes spec self-review and user review gate. Use after explore or directly when the change is clear enough to propose.
---

# SuperSpec Propose (Phase 1)

Fusion: `opsx:propose` + `brainstorming` (spec self-review + user review gate)

## Trigger

User invokes `/superspec:propose` with a change description. May also be invoked after Phase 0 (explore) — in which case change directory and investigation.md may already exist.

---

## Step 1a: Read Inputs

Read in priority order:
1. `investigation.md` — if exists (from Phase 0), use as constraint
2. Phase 0b conversation confirmations — if coming from explore
3. User's direct description — if starting fresh

---

## Step 1b: Create Change Directory (Idempotent)

```
Determine change name:
  - If openspec/changes/<name>/ already exists (from Phase 0) → skip creation
  - If not:
    - Derive kebab-case name from user description
      (e.g. "add CSV export" → "add-csv-export")
    - Check directory does NOT exist (openspec new change throws on existing dir)
    - Execute: openspec new change "<name>"
```

---

## Step 1c: Generate Artifacts (DAG Dependency Order)

```
proposal.md          (no dependency, generate first)
     ↓
specs/<cap>/spec.md  (depends on proposal) ← pure WHAT: Requirement + Scenario
design.md            (depends on proposal)
     ↓
tasks.md             (depends on specs + design) ← coarse-grained skeleton
```

### OpenSpec CLI Interaction

For each artifact in DAG order:
1. `openspec status --change "<name>" --json` → get build order and ready artifacts
2. `openspec instructions <artifact-id> --change "<name>" --json` → get generation instructions
3. Generate artifact following instructions, using context/rules as constraints
4. Do NOT write context/rules content into artifact files

### Delta Spec Format (STRICT)

Delta specs contain **only behavior (WHAT)**, never implementation (HOW).

```markdown
# Delta for <capability>

## ADDED Requirements
### Requirement: <name>
The system SHALL/MUST <behavior>.

#### Scenario: <name>
- WHEN <action> (preconditions merged into WHEN, e.g. "WHEN authenticated user clicks Export")
- THEN <outcome> (observable, verifiable result)

## MODIFIED Requirements
### Requirement: <name>
<updated behavior>
(Previously: <old behavior>)

#### Scenario: <name>
- WHEN <action>
- THEN <outcome>

## REMOVED Requirements
### Requirement: <name> (Deprecated)
<reason for removal>

## RENAMED Requirements
### <old name> → <new name>
```

**Forbidden in delta spec:**
- File paths, function names, class names
- Implementation hints, implementation suggestions
- GIVEN keyword (merge preconditions into WHEN)
- TBD, TODO, placeholder markers

---

## Step 1d: Spec Self-Review

After all artifacts generated, execute checklist:

- [ ] **Placeholder scan** — no TBD/TODO/[placeholder] in any artifact
- [ ] **Internal consistency** — proposal/specs/design are mutually consistent
- [ ] **Scope check** — change scope is reasonable, no scope creep
- [ ] **Scenario observability** — every THEN is a verifiable outcome, not vague description
- [ ] **WHAT/HOW separation** — delta spec contains no file paths, function names, or implementation suggestions

Issues found → fix inline → no re-review needed

---

## Step 1e: User Review Gate (CANNOT SKIP)

Present all generated artifacts to user. Explicitly ask:

> "The above proposal, specs, and design — do they match your expectations? Confirm to proceed to planning phase."

- User requests modification → modify → re-run self-review → re-present
- User confirms → proceed to Phase 2

---

## Seam to Phase 2

```
proposal.md, specs/, design.md, tasks.md all exist in change directory
→ Phase 2 reads all artifacts in one pass
```

---

## Discipline Skills Active

| Skill | When |
|-------|------|
| `verification-before-completion` | Before declaring propose "done" |
| `brainstorming` | When comparing approaches for design decisions |
