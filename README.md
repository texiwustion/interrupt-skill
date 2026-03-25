# interrupt-skill

> Context interrupt management for Claude Code — record interruptions, resume with memory probing.

## The Problem

You're deep in a task. Someone calls. You come back 30 minutes later and can't remember where you were or what you'd already figured out. You waste 10 minutes re-tracing your steps.

## The Solution

Four slash commands that work together:

```
/interrupt-init      # one-time setup
/interrupt <text>    # record an interruption (auto-structured by AI)
/interrupti          # interactive guided recording
/interrupt-resume    # resume with Socratic probing
```

On resume, Claude asks targeted questions about your current knowledge state *before* diving back in. This prevents you from picking up stale assumptions or re-doing work you already validated.

## Install

```bash
# Copy command files
cp commands/*.md ~/.claude/commands/

# Copy helper script
mkdir -p ~/.claude/skills/interrupt-record/scripts
cp scripts/init-repo.sh ~/.claude/skills/interrupt-record/scripts/

# Initialize the storage repo (run once)
# In Claude Code:
/interrupt-init
```

## Usage

**1. Record an interruption (auto mode):**
```
/interrupt I was refactoring the auth middleware, extracted JWT validation to
useAuth hook, the /api/admin routes still use the old pattern, need to update
route.ts, middleware.ts, and the test file
```

Claude extracts the structure automatically.

**2. Record with guided prompts:**
```
/interrupti
```
Claude asks step-by-step: what task, where stopped, what's confirmed, what's open, next steps, keywords.

**3. Resume a task:**
```
/interrupt-resume auth
```
Claude finds the matching record, probes your memory with 1-3 targeted questions, shows a resume summary, then jumps into the work.

## Record Format

```markdown
## Auth Middleware Refactor

停在：/api/admin routes still use old pattern

已确认：
- JWT validation extracted to useAuth hook
- New pattern works on /api/user routes

未确认：
- Whether test file needs mock updates

下一步：
1. Update route.ts
2. Update middleware.ts
3. Check test file

激活词：auth; middleware; JWT
```

## Storage Structure

```
~/w/interrupts/
├── active/       ← pending records
└── archived/     ← resumed/completed
```

Synced to a git remote of your choice (configured in `/interrupt-init`).

## License

MIT
