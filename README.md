# Claude Code Guided Course

An interactive curriculum for setting up Claude Code well — built as a beat-by-beat walkthrough that teaches the mental model alongside the workspace files.

## What's in this repo

| File | Purpose |
|------|---------|
| `.claude/commands/learn.md` | The course itself — invoked as `/learn` (or `/learn 1`, `/learn hooks`, etc.) |
| `.claude/commands/new-project.md` | Companion command — sets up a new project from scratch using what `/learn` teaches |
| `.claude/commands/save-progress.md` | Slash command to save session state to `WORKLOG.md` and `PROGRESS.md` |
| `.claude/output-comments.md` | Optional context file — defines proactive ★ signals (Insight, Flag, Tradeoff, etc.) for Claude's responses |

## Course modules

`/learn` walks through six modules. Each module is a series of "beats" — short, single-concept teaching moments with a check-in at the end of each.

| Module | Topic | Beats |
|--------|-------|-------|
| 0 | Mental model: why each Claude Code primitive exists | 11 |
| 1 | CLAUDE.md and context files (SOUL, anchor, about-me, working-style) | 11 |
| 2 | Hooks and enforcement | 11 |
| 3 | WORKLOG and session continuity | 12 |
| 4 | Skills, commands, and agents | 11 |
| 5 | Verification and defining "done" | 10 |
| 6 | Advanced patterns | 12 |

Start with `/learn` (Module 0). Jump in with `/learn hooks`, `/learn 3`, etc.

## Install

### Option A — copy commands globally (available in every project)

```bash
git clone https://github.com/TheBitcoinBreakdown-95/Claude-Code-Guided-Course.git
cd Claude-Code-Guided-Course
mkdir -p ~/.claude/commands
cp .claude/commands/*.md ~/.claude/commands/
```

After restart, `/learn`, `/new-project`, and `/save-progress` are available in every Claude Code session.

### Option B — copy into a single project

```bash
git clone https://github.com/TheBitcoinBreakdown-95/Claude-Code-Guided-Course.git
cp -r Claude-Code-Guided-Course/.claude/* /path/to/your/project/.claude/
```

### Output comments (optional)

`output-comments.md` is a context file, not a command. To use it, copy it into a project's `.claude/` directory and import it from `CLAUDE.md`:

```bash
cp Claude-Code-Guided-Course/.claude/output-comments.md /path/to/your/project/.claude/
```

Then in that project's `CLAUDE.md`:

```
@.claude/output-comments.md
```

## How `/save-progress` works

It writes to two files in the project root:

- **`WORKLOG.md`** — replaced each session, under 40 lines: current state, key decisions, next steps with verifiable done states
- **`PROGRESS.md`** — updated in place, tracks long-running arcs across sessions (modules completed, beats done, files created)

The command file is comment-marked to make extending it easy — add another section to save additional files.

## Coupling note

`learn.md` and `new-project.md` are paired. Any change to phases, checklist items, or primitives in either file requires a corresponding update in the other. The header of `learn.md` flags this.
