---
name: superspec-finish
description: Phase 4b of SuperSpec workflow — handle git completion after verification passes. Presents 4 options for branch disposition and cleans up worktree. Use after verify passes (no CRITICAL issues).
---

# SuperSpec Finish (Phase 4b)

Fusion: `finishing-a-development-branch` (adapted)

## Trigger

User invokes `/superspec:finish`. Requires Phase 4a verify to have completed with no CRITICAL issues.

## CRITICAL Gate

```
If CRITICAL issues exist from verify:
  → Do NOT proceed
  → Report: "CRITICAL issues must be resolved before finish. Run /superspec:verify after fixing."
```

---

## Steps

### 1. Run full test suite (if available)

```bash
# Adapt to project's test command
npm test / pnpm test / cargo test / go test ./... / etc.
```

### 2. Determine base branch

```
Current branch tracking remote → identify base (main/master/develop)
```

### 3. Present 4 options

```
All implementation complete. Choose branch disposition:

  1. Merge locally    — merge current branch back to base branch
  2. Push + PR        — push to remote and create Pull Request
  3. Keep as-is       — keep branch, no merge/push
  4. Discard          — discard work, clean up worktree and branch
```

### 4. Execute choice

| Choice | Action |
|--------|--------|
| Merge locally | `git checkout <base> && git merge <branch>` |
| Push + PR | `git push -u origin <branch>` → `gh pr create` |
| Keep as-is | No action, branch preserved |
| Discard | `git checkout <base>` → clean up worktree + branch |

### 5. Cleanup worktree (if applicable)

```bash
# If working in a worktree created by superspec:apply
git worktree remove ../worktrees/<name>
```

---

## Seam to Phase 5

```
Git operations complete (merge/PR/keep)
→ Phase 5 archive proceeds with completion check
```

---

## Discipline Skills Active

| Skill | When |
|-------|------|
| `verification-before-completion` | Before declaring finish "done" |
| `finishing-a-development-branch` | Core discipline for branch disposition |
