# New Project Setup

> **Coupled doc:** This file is paired with `.claude/commands/learn.md`. Any change to phases, checklist items, or primitives here requires a corresponding update in learn.md (the relevant module), and vice versa.

Use this when starting a new project. It captures every primitive from the Ai Folder workspace and explains when each one matters. For a deep-dive on any phase, use `/learn [topic]`.

---

## Phase 0: Before You Touch Code

### Step 1: Set your target stage

Decide which stage you're building to before answering anything else. Stage 0 decisions are wrong for Stage 3 — knowing the target prevents over-engineering early and under-engineering late.

| Stage | What you're building | Gate criteria before advancing |
|-------|---------------------|-------------------------------|
| **0 — Prototype** | Core loop works locally | Happy path executes; no auth, no hosting, minimal error handling |
| **1 — Personal use** | Works for you reliably | Error handling on critical paths; you can run it daily without babysitting |
| **2 — Demo / MVP** | Others can use it | Auth, basic UX, deployable; public-facing reliability |
| **3 — Production** | Meets SLAs | Observability, security hardening, monitoring, rollback procedures |

### Step 2: Answer the 8 kickoff questions

Answer all 8 before anything else. They form the spec that every later decision traces back to.

**Understanding the project:**
1. What does this project do in one sentence? (If you can't say it in one sentence, the scope isn't defined yet.)
2. Who uses it, and what do they need?
3. What's the tech stack? (Language, framework, database, key libraries.)
4. Where is the entry point? (The file or command where execution begins.)

**Understanding the work:**
5. What needs to be done? (The task list, feature set, or bug report.)
6. What does "done" look like for the first milestone? (Acceptance criteria or test conditions — without this you cannot verify.)
7. What constraints exist? (Deadlines, backward compatibility, performance budgets, forbidden dependencies.)
8. What could go wrong? (Failure modes, security concerns, data loss risks — name them now so Claude can avoid them.)

If you can't answer 1-6 clearly, the project isn't scoped enough to start.

### Step 3: Write PRODUCT_SPEC.md

Create this file in the project root before touching code. The NOT-in-MVP section is critical — without it, AI will implement features you didn't ask for because they seem related.

```markdown
# Product Spec: [Project Name]
**Target stage:** [0 / 1 / 2 / 3]
**Last updated:** [YYYY-MM-DD]

## What It Does
[One sentence — from question 1]

## Who Uses It
[From question 2]

## Tech Stack
[From question 3]

## In MVP
- [Feature 1]
- [Feature 2]

## NOT in MVP
- [Explicit exclusion 1 — things Claude might add that you don't want]
- [Explicit exclusion 2]

## Constraints
[From question 7]

## First Milestone Done State
[From question 6 — verifiable, not vague]
```

### Step 4: RPI GO/NO-GO gate

For any task with 3+ steps, Claude must present this before writing a single line of implementation. You approve before work starts.

- **R — Requirements:** What exactly will be built and what's excluded (reference PRODUCT_SPEC.md)
- **P — Plan:** Implementation order and rationale — what gets built first and why
- **I — Issues:** What could go wrong and the mitigation for each risk

Skipping this gate is the primary cause of wasted implementation work. If Claude starts coding before presenting RPI, interrupt and ask for it first.

---

## Phase 1: Directory Structure

```
project/
├── CLAUDE.md                    # Project memory and behavioral constitution
├── CLAUDE.local.md              # Personal notes (gitignore this)
├── WORKLOG.md                   # Active task list and session continuity
├── tasks/
│   └── lessons.md               # Corrections → patterns → rules
├── .claude/
│   ├── settings.json            # Permissions, deny, hooks
│   ├── settings.local.json      # Personal overrides (gitignore this)
│   ├── SOUL.md                  # Agent identity
│   ├── about-me.md              # Who you are, what you want from the partner
│   ├── working-style.md         # How you want Claude to behave
│   ├── anchor.md                # Compaction-resistant critical rules
│   ├── hooks/
│   │   ├── env-guard.sh         # PreToolUse: blocks .env reads
│   │   └── ctx-monitor.sh       # PostToolUse: warns at 60/80 tool calls
│   ├── commands/                # Custom slash commands (.md files)
│   ├── skills/                  # Encoded SOPs (Rule of Three)
│   ├── agents/                  # Specialized subagent definitions
│   └── rules/                   # Path-scoped conditional rules
└── .mcp.json                    # MCP server configuration (if needed)
```

---

## Phase 2: CLAUDE.md Template

```markdown
@.claude/anchor.md
@.claude/SOUL.md
@.claude/about-me.md
@.claude/working-style.md

# [Project Name]

[One sentence: what this is and what it does.]

## Tech Stack
[Language, framework, database, key libraries]

## Commands
- Build: `[command]`
- Dev: `[command]`
- Test: `[command]`
- Lint: `[command]`

## Key Directories
- `src/` -- [what lives here]
- `tests/` -- [test framework and conventions]

## Operating Mode

### Plan Mode
Enter plan mode (Shift+Tab x2) for any task with 3+ steps or architectural decisions.

### Subagent Strategy
Delegate focused research/planning to subagents. Keep main context for execution.

### Self-Improvement Loop
After any correction: add entry to `tasks/lessons.md`. Promote to CLAUDE.md if universal.

### Verification Before Done
Define done as a verifiable state before starting. Prove it works; don't self-report.

## Don't Rules
- [Project-specific constraints]
- If the same fix fails twice, stop and ask

## Session Continuity
- At session start, read WORKLOG.md and tasks/lessons.md
- "Save progress": update WORKLOG.md, under 40 lines, replace don't append
```

---

### SOUL.md Design Principles

The soul defines Claude's identity for this project. Three KB-backed principles that make it effective:

**1. Positioning — put the soul first.**
CLAUDE.md uses U-shaped attention: first and last positions receive the highest attention weight. The `@.claude/SOUL.md` import must be the first or second line. Soul placed in the middle gets compressed.

**2. Anti-pattern budgets (30-40%).**
Explicitly tell Claude what NOT to do in SOUL.md. Naming failure modes makes them less likely: *"Do not smooth over contradictions. Do not pad responses with approval. Do not offer AI/LLM guidance without KB backing."* Target 30-40% of the soul file as negations.

**3. Productive flaw.**
Give the agent a built-in tension point that improves output quality. Example from this workspace: *"Thorough KB queries may surface more context than trivial tasks need. For simple fixes, trust existing conventions and skip the lookup."* This prevents over-engineering without a blanket prohibition.

**Why soul × skill is multiplicative:** A weak soul with strong skills produces mediocre output. A strong soul amplifies every skill invocation. Write the soul as if introducing a colleague to a new hire, not as a list of instructions.

---

### CLAUDE.md Full Operating Manual (18-Section Slot Table)

For production-grade projects. Fill only the slots that matter — leave the rest out.

| # | Section | What goes in it |
|---|---------|-----------------|
| 1 | Workflow Orchestration | When to use plan mode, thinking mode, or both |
| 2 | Parallelism & Isolation | Subagent strategy, git worktree patterns |
| 3 | Verification Before Done | Required proof steps: tests, logs, diffs |
| 4 | Demand Elegance | When to pause and ask for a better solution |
| 5 | Autonomous Bug Fixing | Expectations for end-to-end bug resolution |
| 6 | Self-Improvement Loop | How to capture lessons from corrections |
| 7 | Task Management Protocol | Checklist-driven execution order |
| 8 | Prompting Standards | Challenge/review mode, specification quality |
| 9 | Skills & Reuse | When to promote repeated tasks to commands/skills |
| 10 | Data & Analytics | How to use available CLIs, MCPs, or APIs |
| 11 | Learning & Explanation Mode | Output style preferences (diagrams, walkthroughs) |
| 12 | Terminal & Environment Awareness | Branch display, shell conventions |
| 13 | Documentation Spine | Required summary docs and navigation order |
| 14 | Self-Modification Rules | When Claude may edit CLAUDE.md itself |
| 15 | Tests, CI & Databases | Do not assume they exist; ask before introducing |
| 16 | Hooks & Automation | Recommended hooks and when they apply |
| 17 | Pull Request Behavior | Auto-PR rules, summary requirements, merge gating |
| 18 | Final Principles | Simplicity first, no laziness, minimal impact |

**Prompt cache note:** Order CLAUDE.md with static content first (imports, identity), dynamic content last. Static prefixes are cached; dynamic content at the top breaks caching on every session.

**Instruction count (not line count):** LLMs reliably follow ~150-200 instructions per interaction. Claude's system prompt consumes ~50, leaving 100-150 for CLAUDE.md + rules + skills combined. A 50-line file with 50 distinct instructions loads more heavily than a 200-line file with 30 instructions and examples. Prioritize ruthlessly — remove instructions that don't measurably change output.

### Knowledge Type Placement Matrix

Use this before adding anything to CLAUDE.md. Each type has exactly one correct home.

| Knowledge type | Example | Correct home |
|---------------|---------|-------------|
| Project identity | What this project is and does | CLAUDE.md header |
| Absolute rules | Never read .env | `anchor.md` |
| Working style | Response length, disagreement handling | `working-style.md` |
| User context | Your role, goals, success criteria | `about-me.md` |
| Agent identity | Who Claude is for this project | `SOUL.md` |
| Session state | What was done, what's next | `WORKLOG.md` |
| Corrections | What went wrong, better approach | `tasks/lessons.md` |
| Recurring SOP | Deploy sequence, review protocol | `.claude/skills/` |
| Path-specific rules | Python conventions, KB editing rules | `.claude/rules/*.md` |
| Hard OS-level enforcement | Block .env, block production writes | `settings.json` hooks |

---

## Phase 2b: Context File Templates

These four files are imported by CLAUDE.md and define the working relationship. Fill them once per project.

### `.claude/about-me.md`

```markdown
# About Me

## Role
[Your title and function — e.g., "Solo developer building internal tools" or "ML engineer, strong Python, new to frontend"]

## Goals for This Project
- [Primary goal]
- [Secondary goal]

## Success Criteria
[What does a successful outcome look like? Be specific.]

## What I Want From a Working Partner
- [Communication preference, e.g., "Challenge my assumptions before I commit"]
- [Learning preference, e.g., "Explain the why, not just the how"]
- [Directness preference, e.g., "Tell me when something is a bad idea"]
```

### `.claude/working-style.md`

```markdown
# Working Style

## Communication
- [Response length preference, e.g., "Short, direct — no trailing summaries"]
- [Format preference, e.g., "Headers and bullets over prose"]
- [Emoji preference, e.g., "No emojis unless asked"]

## Collaboration
- [When to ask vs. proceed, e.g., "Ask before starting any task with 3+ steps"]
- [Failure handling, e.g., "If the same fix fails twice, stop and ask"]
- [Disagreement handling, e.g., "Disagree and commit — note concern once, then proceed"]

## Session Rhythm
- Read WORKLOG.md and tasks/lessons.md at session start
- Define "done" as a verifiable state before starting any task
- Use /compact at phase transitions (research → planning, planning → implementation)
```

### `.claude/SOUL.md` Design Checklist

When writing SOUL.md, verify:
- [ ] Soul is positioned as the first or second import in CLAUDE.md
- [ ] 30-40% of the content is negations (what not to do, failure modes to avoid)
- [ ] Includes a productive flaw (a built-in tension that improves output quality)
- [ ] Written as "introducing a colleague" not as "a list of rules"
- [ ] Includes identity continuity note: "This file defines working identity, not memory"
- [ ] Uses experiential framing, not rule-based framing (see below)

**Experiential framing (ExpertPrompting):** Write soul descriptions as experiences, not directives. Rules are explicit constraints; experiences become character — the model embodies them differently.

| Rule-based (weaker) | Experiential (stronger) |
|--------------------|------------------------|
| "Do not provide generic advice." | "You've seen too many projects fail because nobody told the truth early enough. Generic advice is the thing you're trying to prevent." |
| "Challenge assumptions before implementing." | "You are the colleague who asks the uncomfortable question before the team commits to a direction — not to slow things down, but because you've done the expensive rewrite before." |

Use experiential framing for identity statements. Use rule-based framing for explicit constraints (anchor.md). They serve different purposes.

---

## Phase 2c: WORKLOG.md Format Template

```markdown
# WORKLOG

**Last saved:** [YYYY-MM-DD]
**Status:** [One-sentence state of the project]

## Current State
- [Bullet: what's done and verified]
- [Bullet: what's in progress]

## Key Decisions
- [Decision + rationale — what was chosen and why]

## Next
1. **[Task name]** — [one line description] | Done = [verifiable state, not "it was done"]
2. **[Task name]** — [one line description] | Done = [verifiable state]

## Surprises
- [Anything unexpected — update the relevant skill immediately]
```

**Worklog smell test:** If it's longer than 100 lines for a simple task, cut sections not detail. More than 5 commits planned → merge related changes. If you're proud of how thorough it looks, you've over-engineered it.

**Naming convention:** `WORKLOG_feature-name_YYYY-MM-DD.md` for multi-feature work.

---

## Phase 2d: Decision Traces

Record non-obvious decisions so future sessions don't re-litigate them. Write one trace per significant choice; store in WORKLOG.md under "Key Decisions" until there are 10+, then promote to a dedicated `DECISIONS.md`.

```markdown
## Decision: [What was decided — noun phrase, not a sentence]

**Rejected:** [What was considered and not chosen]
**Why:** [The actual reason — constraint, evidence, or tradeoff that made this the right call]
**Date:** [YYYY-MM-DD]
```

**The full lifecycle for bugs and mistakes:**

```
Bug or mistake occurs
    → lessons.md entry (what happened, better approach)
    → If the pattern repeats: encode as a skill
    → If it's a hard constraint: promote to anchor.md rule
    → If it needs enforcement: wire as a hook
```

The lifecycle closes the loop. A mistake without a prevention step just repeats.

---

## Phase 3: settings.json Template

```json
{
  "permissions": {
    "allowedTools": [
      "Read", "Write", "Edit", "Glob", "Grep",
      "Bash(git *)", "Bash(npm *)", "Bash(npx *)"
    ],
    "deny": [
      "Read(./.env)", "Read(./.env.*)",
      "Read(~/.ssh/*)", "Read(~/.aws/*)",
      "Read(**/credentials*)", "Write(./production.config.*)"
    ]
  },
  "hooks": {
    "PreToolUse": [{
      "matcher": "Read|Grep|Edit|Write",
      "hooks": [{ "type": "command", "command": "bash '.claude/hooks/env-guard.sh'" }]
    }],
    "PostToolUse": [{
      "hooks": [{ "type": "command", "command": "bash '.claude/hooks/ctx-monitor.sh'" }]
    }],
    "SessionStart": [{
      "hooks": [{ "type": "prompt", "prompt": "Session starting. Read WORKLOG.md and tasks/lessons.md to restore context. Summarize current state and next task in one sentence before proceeding." }]
    }],
    "Stop": [{
      "hooks": [{ "type": "prompt", "prompt": "If meaningful work was done this session, update WORKLOG.md: what was done, what's next, any key decisions. Under 40 lines. Replace, don't append. Skip if read-only or trivial." }]
    }],
    "PreCompact": [{
      "hooks": [{ "type": "prompt", "prompt": "Context compacting. Write current session state to WORKLOG.md now: done, in progress, next. Replace, don't append." }]
    }]
  }
}
```

**Settings precedence (highest → lowest):** Enterprise policy → User (`~/.claude/settings.json`) → Project (`.claude/settings.json`) → Local (`.claude/settings.local.json`) → CLI flags. `deny` rules override `allowedTools` at any level.

**MCP context budget:** Cap at 10 MCP servers per project and 80 tools total across all MCPs. Every server adds latency and token overhead — install only what is actively used.

---

## Phase 4: Anchor Rules Template

```markdown
# Anchor Rules

Re-read after any compaction or long session. These are absolute.

1. **[Critical rule 1]** — [what and why]
2. **[Critical rule 2]** — [what and why]
3. **Stop on repeated failure.** If the same fix fails twice, stop and ask.
4. **[Sensitive files rule]** — Do not read .env, ~/.ssh/*, credentials.
```

---

## Phase 5: Hook Scripts

Copy from this workspace:
- `.claude/hooks/env-guard.sh` — PreToolUse .env protection
- `.claude/hooks/ctx-monitor.sh` — PostToolUse context counter (60/80 tool call warnings)

Make executable: `chmod +x .claude/hooks/*.sh`

---

## Phase 6: Commands, Skills, and Agents

### Three-Tier Architecture

```
Command (.claude/commands/)   ← entry point, user-facing
    ↓
Agent (.claude/agents/)       ← preloaded with relevant skills, handles orchestration
    ↓
Skill (~/.claude/skills/)     ← encoded SOPs, domain knowledge, reusable across agents
```

Use this hierarchy for complex workflows. Simple one-off prompts don't need all three tiers — start with a command, promote to skill only after the Rule of Three.

### Slash Commands

Identify your 3-6 most repeated prompts and encode them. Example patterns:
- `/review` — git diff + code review prompt
- `/spec [feature]` — interview-first spec discovery
- `/deploy` — deployment checklist

Commands live in `.claude/commands/<name>.md`. Supports `$ARGUMENTS`, `$1`, `$2`.

```markdown
---
allowed-tools: Read, Grep, Glob, Bash(git diff*)
description: Review recent changes for quality, security, and performance
---
!`git diff HEAD~1`
Review the above changes. Flag security issues first, then quality concerns.
```

### Five Skill Patterns (Community-Sourced)

When encoding a skill, pick the pattern that fits:

| Pattern | When to use | Structure |
|---------|-------------|-----------|
| **Workflow skill** | Multi-step SOP repeated 3+ times | Numbered steps, verifiable exit state |
| **Domain knowledge skill** | Reference material Claude needs on demand | Headers + tables, no procedure |
| **Checklist skill** | Quality gate before a transition | Checkbox format, binary pass/fail |
| **Coaching skill** | Asks clarifying questions before acting | AskUserQuestion as default |
| **Meta-skill** | Creates or modifies other skills | Self-referential, use sparingly |

**Thin skills principle:** Good skills are approximately 40 lines. They link to canonical docs rather than duplicating them. If you're writing more than 40 lines for a single skill, split it or cut it — longer skills become untested blobs Claude follows inconsistently.

**3-question skill readiness test:** Before encoding a skill, verify you can answer all three:
1. What does this skill do in one sentence?
2. What does it explicitly NOT do? (scope boundary)
3. What's the verifiable exit state? (how you know it worked)

If you can't answer all three, the skill isn't ready to encode.

**Rule of Three:** Do a task manually 3 times before encoding it as a skill. The third time you're writing the same prompt, turn it into a command. If the command is used across multiple projects, promote it to a global skill.

### Agents

Define specialized agents in `.claude/agents/` when you need:
- A persona that's distinct from the project's default identity (e.g., a critic agent)
- Tool restrictions (agents can be scoped to a subset of tools)
- A subagent preloaded with domain-specific skills

**Key constraint:** Subagents cannot spawn subagents (one level of delegation only).

---

## Phase 7: Enforcement and Verification

### Enforcement Guarantee Ladder

Use this to decide where each rule lives:

| Guarantee | Primitive | When |
|-----------|-----------|------|
| Hard block | Hook (PreToolUse, exit 2) | Rule must NEVER be violated |
| Post-hoc check | Hook (PostToolUse) | Output must meet constraints |
| Always-loaded | CLAUDE.md / anchor.md | Always true for this project |
| On-demand | Skills / commands | Relevant only in specific workflows |
| Accumulated | Auto memory / lessons.md | Learned preferences, recurring patterns |

### Four-Level Artifact Verification Hierarchy

When Claude reports something as "done," verify it at the appropriate level. Self-reports are not evidence.

| Level | What it means | How to verify |
|-------|--------------|---------------|
| **Exists** | The file or output was created | Read the file; check it isn't empty or stub |
| **Substantive** | Content is real, not placeholder | Grep for TODO/placeholder; read key sections |
| **Wired** | Connected to the rest of the system | Trace the import chain; check entry point references it |
| **Functional** | Works end-to-end in a real environment | Run it; check logs; exercise the actual path |

Most bugs live at Wired or Functional. "I created the function" is Level 1. You need Level 4.

**Define done as a verifiable state before starting.** Example: "Done = the hook fires and blocks the read" not "Done = the hook was configured."

### Model Selection for Verification

- Use Opus for planning and architectural decisions (high reasoning, higher cost)
- Use Sonnet for 80-90% of implementation (balanced capability/cost)
- Use Haiku for repetitive subtasks (fast, cheap)
- Community pattern: "Get a plan from Opus, execute with Sonnet, spot-check with Haiku"

---

## Phase 8: AI-Specific Anti-Patterns

Wire the defenses against these before you need them. These are the most common failure modes from the KB.

| Anti-Pattern | What it looks like | Defense |
|-------------|-------------------|---------|
| **Scope Creep by AI** | Claude finishes M2 and starts M3 unprompted | Define milestones in WORKLOG.md — they are the boundary. "When the agent finishes M2, it stops." |
| **Premature Completion** | Claude self-reports done when only Level 1 (Exists) is satisfied | Use Four-Level Verification Hierarchy — require Level 4 (Functional) for any task marked complete |
| **Context Pollution** | Long sessions where Claude mixes old task context into new work | `/clear` between unrelated tasks; `/compact` at phase transitions (not mid-task) |
| **Compaction Amnesia** | Anchor rules lost after compaction | `anchor.md` imported first in CLAUDE.md; re-read instruction in anchor itself |
| **Concurrent File Editing** | Multiple agents editing the same file; last write wins | One-writer rule: only one agent may write to a file at a time; use worktrees for parallel work |
| **Context Degradation** | Waiting until 100% context fill to compact | Compact at 20-40% degradation — performance degrades long before the limit; don't wait for auto-compaction |
| **Empty Context Files** | about-me.md / working-style.md created but never filled | Fill them in Phase 0, same session as project creation |
| **Loop Trap** | Retrying the same failing fix 3+ times | If the same fix fails twice, stop and ask — encode this in anchor.md |
| **Kitchen-Sink Skills** | Encoding everything as a skill, including one-off tasks | Rule of Three: only encode after doing manually 3 times |
| **Model-Task Mismatch** | Using Opus for repetitive subtasks (wasteful) or Haiku for complex reasoning (poor quality) | Match model to task: Opus → planning, Sonnet → implementation, Haiku → repetition |
| **Vision Compression** | Strategic context (the "why") gets compressed out as implementation detail grows; features ship correctly but solve the wrong problem | Checkpoint strategic context at each milestone in WORKLOG; use `anchor.md` to preserve the original goal; `_MANIFEST.md` for large projects |
| **Prompt Entropy** | A prompt grows through accumulated patches until Claude stops following it reliably; instructions are contradictory or redundant | Treat prompts like code: version them, test on change, prune rather than patch — rewrite from scratch when degraded |
| **Over-Automation Collapse** | Too many autonomous steps; one mistake compounds silently through the entire chain before you see output | Cap autonomous runs; require human checkpoint every N steps; prefer fewer verifiable steps over longer unsupervised chains |
| **State Corruption** | Multiple agents or sessions write to shared state without coordination; last-write-wins silently breaks things downstream | One-writer rule enforced via worktrees; always read current state before writing; use atomic file operations |
| **Analysis Paralysis** | 5+ reads with no writes; stuck in a research loop; no output after multiple rounds | Cap research at 3 rounds max; if still stuck after 2, write a plan before reading anything else; 5 reads with no writes is the stuck signal |

---

## Phase 9: First Session Checklist

**Foundation:**
- [ ] Answered all 8 kickoff questions (Phase 0)
- [ ] Created directory structure (Phase 1)

**Identity and context files:**
- [ ] CLAUDE.md written with all 4 imports — SOUL import first (Phase 2)
- [ ] about-me.md filled in (role, goals, success criteria — not just created)
- [ ] working-style.md filled in (communication, collaboration, session rhythm)
- [ ] SOUL.md has anti-pattern budgets (30-40% negations) and a productive flaw
- [ ] anchor.md created with 3-5 critical rules including "stop on repeated failure" (Phase 4)

**Enforcement:**
- [ ] settings.json configured with deny + all 5 hooks (PreToolUse, PostToolUse, SessionStart, Stop, PreCompact) (Phase 3)
- [ ] Hook scripts copied and made executable: `chmod +x .claude/hooks/*.sh` (Phase 5)
- [ ] MCP servers configured: ≤10 total, ≤80 tools across all MCPs

**Session continuity:**
- [ ] WORKLOG.md created using Phase 2c format template
- [ ] First task in WORKLOG.md has a verifiable Level 4 "done" state
- [ ] tasks/lessons.md created (empty is fine)

**Commands and skills:**
- [ ] At least 1 custom command created for the most repeated prompt (Phase 6)

**Hygiene:**
- [ ] .gitignore includes: CLAUDE.local.md, settings.local.json, .env, .env.*
- [ ] Committed to git: CLAUDE.md, settings.json, anchor.md, hooks/, commands/
