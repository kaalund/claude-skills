---
name: multiplay
description: Use when pair-coding with shared repo, enabling seamless turn-taking with git handoffs via handoff and takeover triggers
---

# Multiplay - Pair Programming Handoffs

Automates git handoff workflows for pair-coding sessions. One trigger per handoff/takeover instead of manual git sequences.

**Trigger words:** `handoff` (finish your turn) and `takeover` (start your turn). These are patterns Claude recognizes, not slash commands.

## When to Use

- Two developers taking turns with Claude Code on a shared repo
- User says "handoff", "takeover", "let my partner take over", "I'm taking over"
- User says "share my dev server", "create tunnel", or you detect a dev server starting

**Don't use for:** simultaneous branch work, solo dev, or repos without a shared remote.

## Triggers

**CRITICAL:** These are ATOMIC operations. Execute the entire workflow - never break into manual steps.

Before executing, verify remote exists with `git remote get-url origin`. If no remote, guide user: create repo on GitHub/GitLab, `git remote add origin URL`, grant partner access, `git push -u origin main`.

### handoff

Stages all changes, commits with descriptive message, pushes. Auto-recovers from push rejection.

When user says "handoff":
1. Run `git remote get-url origin` - if fails, tell user to set up remote
2. Run `git add -A`
3. Generate commit message from `git diff --stat` - describe what changed, not "handoff"
4. Run `git commit -m "descriptive message"`
5. Run `git push origin main`
6. If push rejected: run `git pull --rebase origin main` then `git push origin main`
7. If rebase has conflicts: pause for user resolution

**Do NOT:** stage/commit before running handoff (it includes that), pull before handoff (it handles rejection), break into manual steps.

### takeover

Syncs latest changes from partner.

When user says "takeover":
1. Run `git remote get-url origin` - if fails, tell user to set up remote
2. Run `git pull --rebase origin main`
3. Run `git status` to verify clean state
4. If rebase has conflicts: pause for user resolution

## Dev Server Sharing

When your partner wants to see your running dev server, **REQUIRED SUB-SKILL:** Use `share-link` to create the tunnel.

Auto-detect dev server output patterns that should prompt you to offer sharing:
- `Local: http://localhost:PORT` (Vite)
- `Server running at http://localhost:PORT` (generic)
- `ready - started server on` (Next.js)
- `App running at: http://localhost:PORT` (Vue CLI)

When detected or user says "share my dev server" / "create tunnel": invoke the `share-link` skill and follow its procedure.
