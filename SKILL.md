---
name: interrupt-skill
description: |
  Context interrupt management system for Claude Code. Saves and resumes work sessions
  with Socratic knowledge probing. Four commands: /interrupt (auto-record), /interrupti
  (guided record), /interrupt-init (setup), /interrupt-resume (resume with probing).
  Use when recording a work interruption, resuming an interrupted task, or managing
  context switches between tasks. Trigger words: interrupt, 中断记录, 恢复中断, resume task.
license: MIT
compatibility: Claude Code (primary), any agent supporting slash commands
metadata:
  author: texiwustion
  version: 1.0.0
---

# Interrupt Skill — Context Interrupt Management

A lightweight system for recording work interruptions and resuming them with memory probing. Prevents lost context when you're pulled away mid-task.

## Commands

| Command | Description |
|---------|-------------|
| `/interrupt-init` | One-time repo setup (run first) |
| `/interrupt <description>` | Auto-record an interruption (AI extracts structure) |
| `/interrupti` | Guided interruption recording (step-by-step prompts) |
| `/interrupt-resume [keyword]` | Resume with Socratic probing of current knowledge state |

## Core Concept

When you're interrupted mid-task, you record:
- **What** you were doing (task name)
- **Where** you stopped (current progress)
- **What's confirmed** (validated conclusions)
- **What's unresolved** (open questions)
- **Next steps** (what to do when resuming)
- **Activation keywords** (words that will help you find this record later)

On resume, the system probes your current understanding with targeted questions before diving back in — preventing you from blindly picking up where you left off without re-checking your mental state.

## Storage

Records are stored in `~/w/interrupts/` as a git repo:
- `active/` — pending interruptions
- `archived/` — completed/resumed records

Records are auto-synced to your remote git repo (configurable in `/interrupt-init`).

## Interrupt Record Format

```markdown
## Task Name

停在：current progress or blocker

已确认：
- confirmed conclusion 1
- confirmed conclusion 2

未确认：
- open question

下一步：
1. next action

激活词：keyword1; keyword2
```

## Install

```bash
# 1. Copy commands to Claude Code
cp commands/*.md ~/.claude/commands/

# 2. Copy scripts
mkdir -p ~/.claude/skills/interrupt-skill/scripts
cp scripts/init-repo.sh ~/.claude/skills/interrupt-skill/scripts/

# 3. Initialize the interrupts repo (run once)
/interrupt-init
```

Or with `npx skills add texiwustion/interrupt-skill` if supported by your skills manager.

## Workflow Example

**Getting interrupted:**
```
/interrupt working on auth refactor, extracted JWT logic to useAuth hook,
middleware still uses old pattern, need to update 3 files next
```

**Resuming later:**
```
/interrupt-resume auth
```
→ Claude asks: "You stopped at updating middleware. Do you remember which 3 files?"
→ If you remember → jump straight in
→ If you forgot → Claude walks you through the context

## Why This Matters

Context switching is expensive. This system makes resumption fast and reliable by:
1. Capturing context at interruption time (when it's fresh)
2. Probing your memory on resume (instead of assuming you remember everything)
3. Persisting records in git (survives machine restarts, shareable)
