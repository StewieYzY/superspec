---
name: superspec-explore
description: Phase 0 of SuperSpec workflow — explore problem space with divergent thinking (0a) then converge on design direction (0b). Use when ideas are vague, need to think through a change before proposing, or want to compare approaches. Combines opsx:explore stance with brainstorming discipline.
---

# SuperSpec Explore (Phase 0)

Fusion: `opsx:explore` (stance) + `brainstorming` (Socratic + HARD-GATE)

## Trigger

User invokes `/superspec:explore` with an idea, problem, or change name to think through. May also be invoked with no argument — enter explore mode open-ended.

## HARD-GATE

**No implementation.** No code, no file creation (except OpenSpec artifacts if user asks to capture thinking). This is thinking time.

---

## Phase 0a — Divergent Exploration

### Autonomous Recon (always first)

```
1. Read openspec/specs/ to understand current system
2. Check openspec/changes/ for active changes
3. Report findings in 2-3 sentences, then begin questioning
```

### Behavior Norms

- Ask **one question at a time** (Socratic) — never stack questions
- Allow free divergence — follow interesting threads, pivot when new info emerges
- Use ASCII diagrams liberally when they clarify thinking
- Do NOT create any directories or files
- Do NOT write code

### Exploration Dimensions (ask in priority order, one at a time)

1. **Motivation** — What real problem does this solve?
2. **Scope** — Who is affected? How does existing behavior change?
3. **YAGNI** — Is there a simpler solution?
4. **Risk** — What's the biggest uncertainty?

### Soft Checkpoint (0a end — CANNOT SKIP)

After sufficient exploration, explicitly ask:

> "Based on the above investigation, my judgment is [worth continuing / suggest abandoning], because [reason]. Continue or stop here?"

- User **abandons** → no directory created, end
- User **continues** → proceed to 0a→0b transition

---

## 0a → 0b Transition (Change Directory Creation Point)

When user chooses to continue:

```
1. Ask: "What is the change name? (kebab-case, used for directory, e.g. add-csv-export)"

2. After getting name, check if directory already exists:
   - If openspec/changes/<name>/ exists → skip creation, proceed to 0b
   - If not → execute: openspec new change "<name>"
     (IMPORTANT: openspec new change throws error on existing directory,
      so MUST check before calling)

3. If exploration produced persistent insights, write:
   openspec/changes/<name>/investigation.md

4. Enter Phase 0b
```

---

## Phase 0b — Convergent Design

### HARD-GATE Activated

**No implementation allowed.** This is design convergence, not execution.

### Behavior Norms

- First, **declare dimensions confirmed in 0a** — do NOT re-ask:
  > "Based on Phase 0a exploration, we confirmed:
  > - Motivation: <one sentence>
  > - Scope: <one sentence>
  > - YAGNI check: <conclusion>
  > Now entering design convergence, focusing on approach selection and implementation direction."

- If investigation.md exists, read it as constraint
- Ask one question at a time to confirm remaining unclear requirements
- Provide **2-3 approach comparisons with explicit recommendation**
- Present design in **3 blocks** (each ~200-300 words), user confirms each before continuing:

### Three Presentation Blocks

| Block | Content | Focus |
|-------|---------|-------|
| **Block 1: Why & What** | Reference 0a motivation conclusion, add success criteria | Intent |
| **Block 2: Scope & Impact** | Which spec domains affected, how existing behavior changes | Boundaries |
| **Block 3: Approach** | Technical direction, approach comparison & tradeoffs | **Core contribution of 0b** |

After each block: "Is this direction right? Or needs adjustment?"

### HARD-GATE

All 3 blocks confirmed before proceeding. Unconfirmed = no forward movement.

### Output

Complete design understanding for Phase 1 (proposal draft direction). No artifacts written — understanding lives in conversation context and investigation.md (if created).

---

## Seam to Phase 1

```
Change directory created at 0a→0b transition (or already existed)
investigation.md written (if exploration produced insights)
→ Phase 1 Step 1b checks directory existence, skips creation if present
```

---

## Discipline Skills Active

| Skill | When |
|-------|------|
| `verification-before-completion` | Before declaring exploration "done" |
| `systematic-debugging` | If exploration surfaces a bug or contradiction |

---

## What You Don't Do

- Implement anything
- Create code files
- Force a conclusion (thinking IS the value)
- Auto-capture insights (offer to save, don't just do it)
- Follow a script (adapt to what emerges)
