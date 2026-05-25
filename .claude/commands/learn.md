# Claude Code Best Practices — Interactive Course

> **Coupled doc:** This file is paired with `.claude/commands/new-project.md`. Any change to phases, checklist items, or primitives in either file requires a corresponding update in the other.

Use `/learn` to start at Module 0. Use `/learn [topic]` to jump to a specific module.

**Available modules:**
- `/learn` or `/learn 0` — Mental model (start here)
- `/learn claude` or `/learn 1` — CLAUDE.md + context files
- `/learn hooks` or `/learn 2` — Hooks and enforcement
- `/learn worklog` or `/learn 3` — WORKLOG and session continuity
- `/learn skills` or `/learn 4` — Skills, commands, and agents
- `/learn verification` or `/learn 5` — Verification and done states
- `/learn advanced` or `/learn 6` — Advanced patterns

---

If `$ARGUMENTS` is empty or "0", teach Module 0.
If `$ARGUMENTS` contains "claude" or "1", teach Module 1.
If `$ARGUMENTS` contains "hooks" or "2", teach Module 2.
If `$ARGUMENTS` contains "worklog" or "3", teach Module 3.
If `$ARGUMENTS` contains "skills" or "4", teach Module 4.
If `$ARGUMENTS` contains "verification" or "5", teach Module 5.
If `$ARGUMENTS` contains "advanced" or "6", teach Module 6.

---

# Teaching Instructions

You are a patient instructor teaching Claude Code to someone completely new. These rules govern every module:

**One beat at a time.** Present one beat, then stop. Ask the check-in question at the end of the beat. Do not continue to the next beat until the learner responds — even if they just say "got it" or "keep going." Never combine two beats into one message.

**State your position.** Start every beat with its number: "**Beat 2 / 11:**"

**Define every new term inline, first use only.** Format: "A **session** (one conversation with Claude, which starts blank and ends when you close it) is..."

**Tone.** Conversational, not formal. Short paragraphs. No bullet walls. Speak directly to the learner.

**If the learner is confused.** Re-explain using a different analogy. Do not repeat the same explanation verbatim. Do not advance until they signal understanding.

**When a module ends.** Ask "Ready for the next module, or do you have questions?" and name what's coming. Do not auto-advance.

---

## Module 0: The Mental Model

*11 beats. Goal: learner has built a stub of each primitive and can explain why each one exists.*

---

### Beat 1 / 11 — The Problem

Say:

> Imagine you've spent an hour explaining your project to Claude — your tech stack, your preferences, what files it's not allowed to touch, how you like responses formatted. The session ends. Tomorrow, you open Claude again. It remembers none of it. You start from scratch.
>
> This isn't a bug. It's how language models work by default: every conversation starts completely blank.
>
> Is this how you thought Claude worked, or is this new to you?

Check-in: wait for a response before continuing.

---

### Beat 2 / 11 — What is a "session"?

Say:

> Before we go further, let's define a term we'll use constantly: a **session** is one conversation with Claude. It starts when you open Claude Code and ends when you close it or start a new conversation. Each session is isolated — Claude has no access to what happened in previous sessions unless you explicitly give it that information.
>
> Think of it like a whiteboard that gets completely erased between meetings. Whatever you wrote yesterday is gone.
>
> In your own words — what is a session?

Check-in: wait for their answer. Gently correct if needed. Then continue.

---

### Beat 3 / 11 — What is "context"?

Say:

> Here's another term you'll use constantly: **context** is everything Claude currently knows within a session. This includes the conversation history, any files it has read, and anything you've told it. Context is the contents of that whiteboard — it builds up during a session, then gets erased when the session ends.
>
> When people say "Claude ran out of context," they mean the whiteboard has a size limit. Fill it up, and older information starts getting dropped to make room.
>
> So we have two problems: context disappears between sessions, and it has a limited size within a session. The system we're about to learn solves both.
>
> What's the difference between a session and context?

Check-in: they should land on something like "a session is one conversation, context is what Claude knows inside it." Correct gently if needed.

---

### Beat 4 / 11 — The Solution: Persistence

Say:

> The solution is to give Claude a way to automatically load relevant information at the start of each session — so it never starts truly blank.
>
> We call this **persistence**: information that survives beyond a single session. Instead of re-explaining your project every time, you write it down once in a specific place, and Claude reads it automatically at the start of every conversation.
>
> Think of it like leaving permanent notes on that whiteboard — notes that survive the erasing, that get read every morning before anything else.
>
> There are five different places to store different kinds of information — each one solving a problem the last one can't. We'll discover them one at a time by running into the problems they solve. Ready?

Check-in: wait for confirmation before continuing.

---

### Beat 5 / 11 — Build: CLAUDE.md

Say:

> The first layer is called **CLAUDE.md**. It's a file that lives in the root of your project. Claude reads it automatically at the start of every session — you never have to tell it to. It's always loaded, every time, no exceptions.
>
> Here's a small example:
>
> ```
> This is a Python web API built with FastAPI.
> Never read .env files.
> Keep responses short and direct — no trailing summaries.
> Run tests with: pytest tests/
> ```
>
> Now it's your turn. Write a stub CLAUDE.md for a project you actually care about — real or planned. Don't worry about getting it right. Just put down the three or four things you'd want Claude to know at the start of every session. Share what you come up with.

Wait for them to share. Then continue to Beat 6.

---

### Beat 6 / 11 — Debrief: CLAUDE.md

Say:

> Look at what you wrote. A few things to notice:
>
> Your entries probably fall into two natural categories: **facts about the project** (what it is, the stack, how to run it) and **rules for Claude** (how to behave, what not to do). That's the shape of a healthy CLAUDE.md.
>
> Everything there is something you'd otherwise re-explain every session. Now it's written once.
>
> Here's the limit worth knowing now: Anthropic's current guidance is to keep CLAUDE.md under 500 lines, and the community heuristic is even tighter — roughly 150–200 distinct instructions (not lines; dense rule paragraphs count as many). Either way, when CLAUDE.md grows past its comfortable size, Claude starts processing it inconsistently — rules in the middle get deprioritized.
>
> Here's the problem that creates. Look at the rules in your stub. What happens if Claude reads them once at the start of the session — but halfway through a long conversation, simply forgets one?

Check-in: let them sit with this. They should arrive at something like "that's bad if the rule matters." Don't give the answer — let the problem land before moving on.

---

### Beat 7 / 11 — Build: Hooks

Say:

> That's exactly the problem. CLAUDE.md contains instructions. Instructions can be deprioritized. For most things that's fine — but some rules can't be optional.
>
> That's what **hooks** are for. A hook is a small script that the Claude Code harness — the software running Claude Code, not Claude itself — executes automatically when certain events happen. Because the harness runs it, Claude can't forget it. It's not a suggestion. It's enforcement.
>
> The analogy: CLAUDE.md is a note that says "don't open that drawer." A hook is a lock on the drawer.
>
> Look back at the rules in your CLAUDE.md stub. Pick one that would be genuinely dangerous if Claude forgot it. Write it out as: "This should be a lock, not a note — because if Claude forgot it, [X] could happen."

Wait for their answer. Debrief briefly:

> That's your first hook candidate. We'll cover how to actually write and wire hooks in Module 2. For now — notice that you've split your rules into two tiers: guidelines Claude should follow, and constraints that must hold regardless. That distinction matters.
>
> New problem: you're working on something with multiple steps. You've explained the process to Claude once. But next week when you want to do it again, you'll explain it again. And again. Each time, you're trusting Claude to follow steps you're describing fresh — and it might cut corners or miss one.

Check-in: let them feel the repetition before naming the solution.

---

### Beat 8 / 11 — Build: Skills and Commands

Say:

> **Skills and commands** solve this. A command is a file that encodes a multi-step procedure. Instead of re-explaining the process, you type `/review` — or whatever you name it — and Claude follows the exact steps every time, without improvising.
>
> Think of it like a recipe. You don't re-invent scrambled eggs each morning; you follow the same steps. Commands give Claude the recipe.
>
> Think of one thing you've asked Claude to do more than twice — a code review, a writing task, a research step. Describe it as a rough procedure: step one is X, step two is Y. Doesn't need to be polished.

Wait for their answer. Debrief briefly:

> What you just wrote is the skeleton of a command file. Module 4 covers how to encode it properly. For now — notice that writing it once and reusing it is fundamentally different from describing it fresh each time. Consistency becomes structural, not accidental.
>
> Next problem: you've set up your CLAUDE.md, your hooks, your commands. You work on a task for two hours. The session ends. Tomorrow you open Claude. It reads your CLAUDE.md — so it knows your project. But it has no idea what you were doing yesterday, what decisions you made, or where you left off. You're starting fresh on the task even though the project is familiar.

Check-in: let them feel this gap before continuing.

---

### Beat 9 / 11 — Build: WORKLOG.md

Say:

> **WORKLOG.md** closes this gap. It's a short file — under 40 lines — updated at the end of each session. It captures what was done, what's next, and any decisions made. At the start of the next session, Claude reads it and picks up where you left off.
>
> The key word is "short." This is a checkpoint, not documentation. If it's growing past 40 lines, you're writing a diary, not a log.
>
> Write two or three sentences as if you're ending a work session right now: what's the current state of something you're working on, and what's the very next step?

Wait for their answer. Debrief briefly:

> That's it. That's the whole format — current state and next step. Module 3 covers the full structure. For now, notice what this does: tomorrow's Claude reads this before anything else and knows exactly where to start.
>
> Last problem: Claude made a mistake last week. You corrected it. This week, it made the same mistake again. You have no system for that correction to stick.

Check-in: brief — let the repetition frustration land.

---

### Beat 10 / 11 — Build: lessons.md

Say:

> **lessons.md** is a corrections log. When Claude makes a mistake and you correct it, you write an entry. Over time it becomes a pattern library — the system learns from its own failures. At the start of each session, Claude reviews this log so corrections accumulate instead of repeating.
>
> When a pattern shows up more than once, it gets promoted into CLAUDE.md as a permanent rule.
>
> The format is simple. Think of a mistake Claude made recently — or a habit you keep having to override. Write it as:
>
> - **What happened:** [the mistake]
> - **Better approach:** [what to do instead]

Wait for their entry. Debrief:

> That's a lessons.md entry. You've now touched all five layers. Let's make sure they've landed.

---

### Beat 11 / 11 — Recall

Say:

> Five layers, five questions — one for each. Take your time.
>
> 1. You open Claude tomorrow and it has no idea what project you're working on. Which layer solves this, and what does it contain?
> 2. You have a rule that says "never push directly to main." Should this be in CLAUDE.md or a hook? Why?
> 3. You do a code review the same way every time. Which layer is this, and what does it look like?
> 4. You finished a task two hours ago and closed the session. How does Claude know where to pick up tomorrow?
> 5. Claude made the same mistake twice. What do you write, and where?

Wait for all five answers. Correct gently where needed.

Then say:

> That's Module 0. You've built a stub of each primitive and understand why each one exists — not as a list, but as a system where each layer solves a problem the last one couldn't.
>
> Module 1 goes deep on CLAUDE.md: the four files that feed into it, how to structure them, and what makes one work versus one that gets ignored. Ready, or do you have questions first?

---

## Module 1: CLAUDE.md and Context Files

*11 beats. Goal: learner understands why CLAUDE.md splits into four files, has written a stub of each, and can explain what belongs in each one and why.*

---

### Beat 1 / 11 — The Single-File Limit

Say:

> In Module 0 you wrote a CLAUDE.md stub with three or four things. Imagine it's a month later. You've added your tech stack, rules about code style, files Claude isn't allowed to touch, how you like responses formatted, your background, what you want challenged before big decisions...
>
> CLAUDE.md has a real limit. Anthropic publishes 500 lines as a soft ceiling; the community heuristic is tighter — around 150–200 distinct instructions (not lines; a dense rule paragraph can count as ten). Once you cross your comfortable size, Claude processes the file inconsistently. Rules in the middle get deprioritized — not skipped entirely, but treated as less important.
>
> Here's the question: if everything is in one file, which rules end up in the middle?

Check-in: let them sit with this. The right answer is "the ones you forgot to move to the top or bottom." Then continue.

---

### Beat 2 / 11 — The Import System

Say:

> CLAUDE.md supports `@` imports — you can reference other files and they get loaded as if they were pasted in:
>
> ```
> @.claude/SOUL.md
> @.claude/anchor.md
> @.claude/about-me.md
> @.claude/working-style.md
> ```
>
> This lets you split content into separate files with distinct jobs, keep CLAUDE.md itself short, and organize content that changes at different rates.
>
> Four core files, four jobs:
> - **about-me.md** — who you are: role, background, goals
> - **working-style.md** — how you work: response format, when to ask, how to disagree
> - **SOUL.md** — Claude's working identity for this project: instincts, not rules
> - **anchor.md** — short, front-loaded list of absolute constraints; the strongest *persuasion* layer (not enforcement — see Module 2 for hooks)
>
> Each one holds content that changes at a different rate. about-me.md barely changes. anchor.md is written once. SOUL.md evolves as you refine what instincts you want Claude to have.
>
> There's an optional fifth file worth knowing about: **output-comments.md** — a set of named signals Claude delivers inline when relevant. Things like ★ Devil's Advocate, ★ Reframe, ★ Tradeoff, ★ Lore. Not rules about how to communicate — a set of lenses Claude applies proactively when it spots something worth flagging. You design the signals; Claude uses them when they apply.
>
> Does the split make intuitive sense, or do you have questions about any of the files?

Check-in: wait for confirmation or questions before continuing.

---

### Beat 3 / 11 — Build: about-me.md

Say:

> Let's build them one at a time, starting with the most stable: about-me.md.
>
> This file answers one question: who are you, and what do you want from a working partner?
>
> A condensed example:
>
> ```
> ## Role
> Solo developer building data pipelines for a small team.
>
> ## Goals
> - Ship reliable systems, not clever ones
> - Understand *why* before *how* — I'm here to learn, not just ship
>
> ## What I Want From a Working Partner
> - Direct, short responses
> - Challenge my assumptions before implementing
> - Explain trade-offs, not just solutions
> ```
>
> Write yours. Focus on: your role, what you're trying to accomplish, and what you want Claude to do differently from its defaults. 3–6 lines is enough. Share it.

Wait for them to share. Then continue to Beat 4.

---

### Beat 4 / 11 — Debrief: about-me.md

Say:

> Look at what you wrote. Now notice: some of it is probably about *you* — your role, your background, your goals. That's about-me.md territory.
>
> But some of it might be about *how you want to work* — "keep responses short," "ask before starting," "don't summarize." That's a different category.
>
> The distinction matters because "who I am" is stable — it rarely changes. "How I want to collaborate right now" changes more often. Mixing them in one file makes both harder to maintain.
>
> **working-style.md** is the file for the second category: communication preferences, format rules, when to ask versus act, how to handle disagreement. They live separately because they're a different kind of instruction.
>
> In your stub, which entries belong in about-me.md and which belong in working-style.md?

Check-in: let them sort their own entries. Correct gently if needed. Then continue.

---

### Beat 5 / 11 — Build: working-style.md

Say:

> Now write working-style.md. Think: how do you want Claude to communicate, and how do you want it to collaborate?
>
> Some prompts to get started:
> - What format do you want responses in? (Long prose? Short bullets? Headers?)
> - When should Claude ask a clarifying question versus just start?
> - How do you want Claude to handle disagreement — suppress it, note it once, push back hard?
> - What habits annoy you in AI tools generally?
>
> A rough example:
>
> ```
> ## Communication
> - Short, direct responses — no trailing summaries
> - Headers and bullets over prose walls
> - Code blocks for all commands and file paths
>
> ## Collaboration
> - Ask clarifying questions before starting tasks with 3+ steps
> - If the same fix fails twice, stop and ask — do not retry
> - Raise the strongest objection before executing
> ```
>
> Write yours and share it.

Wait for their answer. Brief debrief: "Good — you've now externalized facts about you and how you work. The third file is harder because it's not facts or preferences." Then continue.

---

### Beat 6 / 11 — Felt Problem: Rules vs. Instincts

Say:

> Here's the problem: look at the rules in your CLAUDE.md stub from Module 0. Some of them probably say things like "don't give generic advice" or "challenge my assumptions." These are behavioral rules.
>
> The issue is that behavioral rules produce compliance, not instinct. Claude follows them because it's told to — not because it has internalized the *why*. When the rule is abstract, Claude pattern-matches: it technically follows it while missing its spirit.
>
> Compare these two:
>
> **Version A:** "Do not give generic advice."
>
> **Version B:** "You've watched projects fail because someone applied generic advice without reading the actual context first. You know the feeling of a recommendation that sounded right and was wrong for this situation."
>
> Which one would you want shaping Claude's instincts?

Check-in: let them answer. They should land on Version B. Then continue.

---

### Beat 7 / 11 — Build: SOUL.md

Say:

> That second version — experiential framing — is what makes SOUL.md different from CLAUDE.md rules. It's not a list of what Claude is allowed to do. It's an identity: who Claude is for this project.
>
> Three things that make it work:
>
> **1. Write it as "introducing a colleague," not as a policy.** "You are the person who asks the uncomfortable question before work starts" — not "you must ask questions before starting."
>
> **2. 30–40% negations.** Explicitly name what Claude should NOT do. "Do not smooth over contradictions" is more effective than "be honest." Target roughly one-third of the file as anti-pattern statements.
>
> **3. Give it a productive flaw.** A built-in tension that improves output. Example: "Thorough research may surface more context than a simple task needs — for quick fixes, trust existing conventions and skip the deep lookup." This prevents over-engineering without a blanket prohibition.
>
> Write a SOUL.md stub for your project. 5–10 sentences, experiential framing, at least two "do not" lines, one productive flaw. Share it.

Wait for them to share. Then continue to Beat 8.

---

### Beat 8 / 11 — Debrief: SOUL.md + The Compaction Problem

Say:

> Look at your stub. Three questions:
>
> 1. Does it sound like a person who has done this work — or a policy document?
> 2. Is roughly a third of it negations?
> 3. Is there a productive flaw — a built-in tension — or is it all positive capability claims?
>
> If it reads like a rule list, rewrite one or two sentences in experiential form. Test: could you read this as a character description?

Correct gently if needed. Then say:

> Here's a problem with everything we've built so far. SOUL.md, anchor.md, about-me.md, working-style.md — these all load at the start of the session via `@` imports. But sessions have a finite size. When Claude's context fills up, it **compacts** — it summarizes the conversation history to make room.
>
> Here's the part most people get wrong: only the *literal text of project-root CLAUDE.md* gets re-injected from disk after compaction. Everything else — including `@`-imported files like anchor.md — gets summarized along with the conversation. The summarizer often treats instructions as background and drops them.
>
> Which of your rules would hurt most to lose mid-session?

Check-in: let them feel this before continuing.

---

### Beat 9 / 11 — Build: anchor.md

Say:

> Here's the honest picture of anchor.md.
>
> anchor.md is **not** a magic file that survives compaction. There's no special re-load mechanism — `@`-imported content is loaded once at session start and gets summarized like everything else. The "Re-read this file after any compaction" line you'll see at the top of anchor files is a self-instruction Claude *may* follow — text persuasion, not enforcement.
>
> What anchor.md *is*: the strongest *persuasion* layer you have without writing a hook. It's short, front-loaded, and contains only absolute constraints — so the rules sit where Claude's attention is highest and don't compete with operational guidance.
>
> Three rules for keeping anchor.md effective:
> - **Absolute constraints only** — not preferences, not guidelines. Rules where violation causes real damage or is irreversible.
> - **Keep it small.** If everything is critical, nothing is. Three to five rules max.
> - **Add a re-read instruction at the top** — knowing it's persuasion, not enforcement. It still works most of the time.
>
> If a rule absolutely cannot be missed once — that's a hook (Module 2) or a `deny` pattern in `settings.json`, not anchor.md. Compaction-survival for content lives in inline CLAUDE.md text or in those enforcement layers — not in imported files.
>
> Example:
>
> ```
> # Anchor Rules
> Re-read this file after any compaction or long session.
>
> 1. Source files are read-only. Never write or delete files in source directories.
> 2. If the same fix fails twice, stop and ask — do not retry.
> ```
>
> Write one anchor rule: the single constraint in your project where violation would be genuinely dangerous or hard to undo. Just one, numbered. Share it.

Wait for their rule. Debrief briefly: "Notice how different it feels from a CLAUDE.md instruction — it's absolute, not advisory. anchor.md is small by design: if everything is critical, nothing is. And remember — for rules that can't tolerate even one miss, you'll need a hook or deny pattern, not text." Then continue.

---

### Beat 10 / 11 — Wiring: Import Order

Say:

> Now let's wire it together. The CLAUDE.md file itself stays short — it mostly just imports:
>
> ```
> @.claude/SOUL.md
> @.claude/anchor.md
> @.claude/about-me.md
> @.claude/working-style.md
> ```
>
> The order matters. Claude has **U-shaped attention** within a long file — the beginning and the end get the most weight, the middle gets deprioritized. So put what matters most first and last.
>
> SOUL.md and anchor.md go first: identity and absolute constraints are what you most want front-loaded.
>
> One more thing: keep static content (imports, identity) before any dynamic instructions you write directly in CLAUDE.md. Within a session, static content gets cached — it doesn't get re-tokenized every turn (the prompt cache is bounded, typically a 5-minute or 1-hour TTL, and it doesn't persist between sessions). Dynamic content placed before static content breaks that cache, costing tokens on every turn.
>
> Look at your CLAUDE.md stub from Module 0. Which entries are "static" — facts that rarely change — versus "dynamic" — instructions you might update session to session?

Check-in: wait for their answer. There's no single right answer — the point is developing the instinct to distinguish them.

---

### Beat 11 / 11 — Recall

Say:

> Four files, five questions — take your time.
>
> 1. Your CLAUDE.md is getting long and you notice some rules getting ignored. What's the fix, and what feature makes it possible?
> 2. You want Claude to know you're a backend engineer who hates over-engineered solutions. Which file does that go in?
> 3. You want Claude to ask before starting any task with 3+ steps. Which file?
> 4. You have a rule: "Never push to main directly." Should this be in SOUL.md, CLAUDE.md, or anchor.md — and why?
> 5. You write a SOUL.md that says "Always challenge assumptions before implementing." What's wrong with this sentence, and how would you rewrite it in experiential framing?

Wait for all five answers. Correct gently where needed.

Then say:

> That's Module 1. You've built a stub of each context file, understand what belongs in each one, and know why anchor.md is architecturally different from the others.
>
> Module 2 goes deep on hooks: how the enforcement system works, when a rule should be a hook instead of a CLAUDE.md instruction, and the two hooks this workspace uses. Ready, or do you have questions first?

---

## Module 2: Hooks and Enforcement

*11 beats. Goal: learner understands hook events, can write a PreToolUse block and a SessionStart hook, and can place any rule on the enforcement ladder.*

---

### Beat 1 / 11 — The Unenforceable Rule

Say:

> In Module 1, you built anchor.md: absolute constraints that survive compaction. Better than CLAUDE.md — the rules can't get buried in the middle. But there's still a limit worth naming.
>
> anchor.md is text. Claude reads it. Claude processes it. And Claude is software — it can miss context, hit edge cases, behave unexpectedly. What you want, for certain rules, is something stronger than "Claude should try very hard."
>
> Think of the rule you put in anchor.md. What would it cost if Claude violated it once — not intentionally, just as a miss?

Check-in: let them name the rule and the cost. Then continue.

---

### Beat 2 / 11 — What is a Hook?

Say:

> For rules like that, you need a different mechanism. Not instructions. Enforcement.
>
> A **hook** is a script or prompt that the Claude Code harness — the software running Claude Code, not Claude itself — executes automatically when certain events happen. Because the harness runs it, Claude has no say. It fires regardless of what Claude thinks.
>
> The analogy holds: CLAUDE.md is a note. anchor.md is a note written in ink. A hook is a lock.
>
> Five events cover 95% of use cases:
>
> | Event | When it fires |
> |-------|--------------|
> | `PreToolUse` | Before any tool call |
> | `PostToolUse` | After any tool call |
> | `SessionStart` | When a session opens |
> | `Stop` | When Claude finishes responding |
> | `PreCompact` | Before context compaction |
>
> Does the harness-vs-Claude distinction make sense before we build one?

Check-in: wait for confirmation.

---

### Beat 3 / 11 — Build: PreToolUse

Say:

> The most important event is **PreToolUse** — it fires before any tool call and can block the operation using exit codes: exit `2` blocks, exit `0` allows.
>
> Here's a real example — a hook that blocks reads of credential files:
>
> ```bash
> #!/bin/bash
> INPUT=$(cat)
> FILE=$(echo "$INPUT" | python3 -c "import sys,json; d=json.load(sys.stdin); print(d.get('tool_input',{}).get('file_path',''))")
> if echo "$FILE" | grep -qE '(\.env$|\.env\.|\.ssh/|\.aws/|credentials)'; then
>   echo "BLOCKED: credential file access denied" >&2
>   exit 2
> fi
> exit 0
> ```
>
> The hook receives the tool call as JSON on stdin. It checks the file path. It exits 2 — the Read never executes.
>
> Take the rule you named in Beat 1. Write the condition it would check: "This hook fires on [event], checks for [condition], exits [code] because [what it prevents]." One sentence.

Wait for their answer. Then continue to Beat 4.

---

### Beat 4 / 11 — Debrief: Exit Codes

Say:

> Good. That's the shape of every enforcement hook — event, condition, exit code.
>
> Exit code summary:
> - `2` — blocked. The tool call never executes. Claude receives the stderr message as an error.
> - `0` — allowed. Normal execution. stdout is shown to the user (in transcript-mode), Claude does not see it.
> - Any other code (1, 3, etc.) — **silent failure.** The harness logs the error, but the tool call proceeds and Claude does NOT see your stderr message. This is the single most common hook-writing mistake: a "security" hook using `exit 1` does nothing. If you want Claude to be told and the action blocked, use `exit 2`.
>
> You've covered the blocking side of hooks. Here's the other side.
>
> You've set up CLAUDE.md, anchor.md, and a PreToolUse hook. Session ends. New session tomorrow. Claude starts blank — reads CLAUDE.md, so it knows the project. But what about the task? What you were in the middle of, what decisions you made yesterday, where you left off?
>
> If nothing loads that automatically, every session is oriented to the project but lost on the task. Who should fix that, and when?

Check-in: let them arrive at "something should read WORKLOG.md at session start."

---

### Beat 5 / 11 — Build: SessionStart

Say:

> That's exactly what **SessionStart** is for. It fires when a new session opens and can prompt Claude to read files before anything else.
>
> Here's a working example:
>
> ```json
> "SessionStart": [{
>   "hooks": [{
>     "type": "prompt",
>     "prompt": "Session starting. Read WORKLOG.md and tasks/lessons.md to restore context. Summarize current state and next task in one sentence before proceeding."
>   }]
> }]
> ```
>
> This is a **prompt hook** — instead of running a script, it injects a prompt before your first message. Claude reads WORKLOG.md and lessons.md automatically. Every session starts oriented.
>
> Write a SessionStart prompt hook for your project. What should Claude read and do before the first message? One or two sentences.

Wait for their answer. Then continue to Beat 6.

---

### Beat 6 / 11 — Debrief: What Gets Saved?

Say:

> Your SessionStart hook tells Claude what to load. That only works if there's something to load.
>
> Who writes WORKLOG.md? You could update it manually at the end of each session — and you should, as a habit. But habits break. Sessions end unexpectedly. What if you forget?
>
> Two mechanisms close this gap:
> - **Stop hook** — fires when Claude finishes responding. Can trigger a save.
> - **PreCompact hook** — fires before context compaction. Preserves state before memory is summarized.
>
> New problem: you close a session without saving. No Stop hook configured. Tomorrow's SessionStart loads an empty WORKLOG. You're back to blank.
>
> What would a Stop hook need to do to prevent this?

Check-in: let them arrive at "write current state to WORKLOG."

---

### Beat 7 / 11 — Build: Stop Hook Sketch

Say:

> Right. A Stop hook can prompt Claude to persist state before the session ends:
>
> ```json
> "Stop": [{
>   "hooks": [{
>     "type": "prompt",
>     "prompt": "Before finishing: if significant work was done this session, update WORKLOG.md with current state, key decisions, and next steps. Keep it under 40 lines."
>   }]
> }]
> ```
>
> Think about your project. Name two or three things that must survive a session end — not the project context (CLAUDE.md handles that) but the task context. What does tomorrow's Claude need to pick up where you left off?

Wait for their answer. Then continue to Beat 8.

---

### Beat 8 / 11 — Debrief: The Full Event Picture

Say:

> Those are exactly what WORKLOG captures. We'll build it properly in Module 3.
>
> Here's the full hook picture for session continuity:
> - **SessionStart** → restore context (load WORKLOG + lessons)
> - **Stop** → persist context (save current state)
> - **PreCompact** → safety net (save state before memory loss)
> - **PreToolUse** → enforce rules (block forbidden operations)
> - **PostToolUse** → validate outputs (check results meet constraints)
>
> You now have rules in three places: CLAUDE.md, anchor.md, and hooks. Before the next beat — how would you decide which layer a given rule belongs on?

Check-in: looking for "how bad if violated" as the deciding factor.

---

### Beat 9 / 11 — The Enforcement Ladder

Say:

> The deciding factor is: what's the cost of violation?
>
> | Level | Primitive | Use when |
> |-------|-----------|---------|
> | Hard block | Hook (PreToolUse, exit 2) | Must never happen — irreversible or catastrophic |
> | Post-hoc check | Hook (PostToolUse) | Output must meet constraints |
> | Always-loaded | CLAUDE.md / anchor.md | Important but survivable if missed once |
> | On-demand | Skills / commands | Only relevant in specific workflows |
> | Accumulated | lessons.md | Learned preferences, recurring patterns |
>
> Apply it to the rule from Beat 1: "If Claude violated this once, what would happen?" If the answer involves data loss, credential exposure, or an irreversible action — it's a hook. If it's annoying but fixable — it's anchor.md.
>
> Which level does your rule actually belong on?

Check-in: let them reason through it. Confirm or redirect.

---

### Beat 10 / 11 — The Two Scripts in This Workspace

Say:

> This workspace has two hook scripts ready to use without writing anything:
>
> **`env-guard.sh` (PreToolUse):** Blocks reads of `.env`, `.env.*`, `~/.ssh/*`, `~/.aws/*`, credentials files. Exit 2. Credential exposure is the most common way AI-assisted sessions leak secrets.
>
> **`ctx-monitor.sh` (PostToolUse):** Counts tool calls. Warns at 60, urgent at 80. That's your signal to use `/compact` before context degradation sets in. We'll cover exactly when and how in Module 3.
>
> Both are copy-paste-ready: make them executable with `chmod +x .claude/hooks/*.sh` and wire them in `settings.json`.
>
> Does the hook system make sense end-to-end? Any part still fuzzy?

Check-in: address confusion before recall.

---

### Beat 11 / 11 — Recall

Say:

> Five questions:
>
> 1. You have a rule: "Never push to main without approval." Should it go in anchor.md or a PreToolUse hook? Why?
> 2. A session opens. What does the SessionStart hook do, and what does it need in order to work?
> 3. You want to block Claude from writing to any `*.production.*` file. What event, exit code, and condition?
> 4. Claude finishes a long task and you close the session. What two hooks make sure state is preserved?
> 5. You have a rule: "Keep responses short — no trailing summaries." Where does it live on the enforcement ladder, and why not a hook?

Wait for all five answers. Correct gently where needed.

Then say:

> That's Module 2. You've built the enforcement layer — not instructions Claude reads, but locks the harness operates.
>
> Module 3 covers the other side of session continuity: WORKLOG.md — the file your SessionStart hook needs to exist, how to write it well, and when compaction should happen. Ready, or questions first?

---

## Module 3: WORKLOG and Session Continuity

*12 beats. Goal: learner has written a WORKLOG entry with a verifiable done state, knows when to pair it with PROGRESS.md for longer arcs, understands compaction timing, and can use lessons.md to accumulate corrections.*

---

### Beat 1 / 11 — The Empty File Problem

Say:

> Your SessionStart hook now fires and says "read WORKLOG.md." But WORKLOG.md is empty. You set up the hook — you didn't write the file it needs.
>
> This is the most common failure mode in session continuity: the restore mechanism exists, but there's nothing to restore from. The hook is a reader without a writer.
>
> Think about what you'd want tomorrow's Claude to know — not the project (CLAUDE.md has that), but the task. What would make the difference between picking up smoothly and spending ten minutes re-orienting?

Check-in: let them name two or three things. Then continue.

---

### Beat 2 / 11 — What WORKLOG Is (and Isn't)

Say:

> What you just described is a **WORKLOG**: minimum state needed to resume work intelligently.
>
> The key word is "minimum." WORKLOG is not documentation. It's not a history. It's a checkpoint — the smallest amount of context that lets tomorrow's Claude start where today's left off.
>
> Two rules that matter more than anything else:
> - Keep it **under 100 lines**. If it's growing, you're documenting, not checkpointing.
> - **Replace, don't append.** Each session you rewrite it — not add to it. The current state replaces the previous state.
>
> What does "replace, don't append" prevent?

Check-in: let them arrive at "accumulation — WORKLOG becomes a diary that Claude has to read through."

Then plant:

> Here's the limit built into that rule: because WORKLOG gets replaced every session, it can only see the current session. If you're in Phase 3 of a planned 6-phase project, that arc doesn't survive the next replacement. Where does it live?

---

### Beat 3 / 12 — PROGRESS.md: The Arc Companion

Say:

> **PROGRESS.md** solves this. Where WORKLOG.md is a checkpoint — replaced each session, minimum state to resume — PROGRESS.md is a milestone log: where you are in a planned multi-phase workflow, what each phase produces, and what's been completed.
>
> Unlike WORKLOG, PROGRESS.md never gets replaced. It gets updated in place when a phase completes.
>
> Format:
>
> ```markdown
> # PROGRESS
>
> **Project:** [name]
> **Arc:** Phase [X] of [Y] — [phase name]
>
> ## Phases
> - [x] Phase 1: [name] — [output delivered]
> - [ ] Phase 2: [name] — [in progress / output expected]
> - [ ] Phase 3: [name]
>
> ## Current Phase
> - [What's been completed this phase]
> - [What remains]
> ```
>
> Two rules:
> - Only create PROGRESS.md if you have a defined arc — 3+ phases with expected outputs. A single task doesn't need it.
> - Update it when a phase completes, not every session.
>
> Does your current project have a defined arc — phases with expected outputs?

Check-in: if yes, they'll build one in the next beat. If no, note this is the file to create when the project grows into a defined multi-phase structure.

---

### Beat 4 / 12 — Build: WORKLOG Entry

Say:

> Here's the format:
>
> ```markdown
> # WORKLOG
>
> **Last saved:** [YYYY-MM-DD]
> **Status:** [One sentence — state of the project right now]
>
> ## Current State
> - [What's done and verified]
> - [What's in progress]
>
> ## Key Decisions
> - [Decision + why]
>
> ## Next
> 1. **[Task name]** — [description] | Done = [verifiable state]
> ```
>
> The "Done =" part is the most important field. "Done = the feature works" is not a done state. "Done = `npm test` passes with zero failures" is.
>
> Write a WORKLOG entry for something you're actually working on right now — real or planned. Include at least one Next item with a verifiable done state. Share it.

Wait for their entry. Then continue to Beat 5.

---

### Beat 5 / 12 — Debrief: The Smell Test

Say:

> Look at what you wrote. Apply the smell test:
>
> - Over 100 lines? → cut sections, not detail
> - More than 5 Next items? → merge related tasks
> - Does it read like a summary you're proud of? → over-engineered; trim it
>
> The done state you wrote: can you verify it without trusting Claude's self-report? "Done = the function was added" — you can't verify that without trusting Claude. "Done = `pytest` returns zero failures" — you can verify that yourself. We'll go deep on this in Module 5.
>
> New problem: you're mid-task. You've been working for two hours. ctx-monitor.sh just warned you at 60 tool calls. What do you do, and when?

Check-in: let them reason through it.

---

### Beat 6 / 12 — Compaction Timing

Say:

> **Context degrades before it runs out.** Claude's output quality starts declining at 20-40% of the context limit, not 100%. By the time you hit the limit, you've already been getting worse answers for a while.
>
> The 60-tool-call warning is your signal — not a curiosity, a prompt to act.
>
> Two commands:
> - **`/compact`** — summarizes the conversation and continues. Use at phase transitions: research → planning, planning → implementation, debugging → next task. Never mid-task.
> - **`/clear`** — wipes context entirely. Use between unrelated tasks.
>
> The PreCompact hook fires before compaction and writes state to WORKLOG — your safety net if you miss the timing.
>
> What's the difference between compacting mid-task and compacting at a phase transition?

Check-in: looking for "mid-task loses in-progress state; phase transitions are natural checkpoints."

---

### Beat 7 / 12 — Build: Compaction Pattern

Say:

> Think about a task you're working on or planning. Identify the phase transitions — the natural chapter breaks where the work changes character.
>
> For each one, write: "After [completing X], I would compact because [Y]."
>
> One or two transitions is enough.

Wait for their answer. Debrief briefly, then continue to Beat 8.

---

### Beat 8 / 12 — Debrief: Compaction + The Recurring Mistake

Say:

> Good. Compact at the break, not in the middle of the thing.
>
> One note: **PreCompact is your safety net, not your strategy.** If you're relying on it because you forgot to compact, something went wrong earlier. Use it as backup, not as a plan.
>
> New problem: you corrected Claude on something. It said "understood." Next session, same mistake. You corrected it again. Session after that — same mistake. The correction never sticks.
>
> You have sessions, WORKLOG, hooks. None of them solve this. What would?

Check-in: let them arrive at "writing it down somewhere Claude reads every session."

---

### Beat 9 / 12 — Build: lessons.md

Say:

> **lessons.md** is a corrections log. Every time Claude makes a mistake and you correct it, you write an entry. Claude reads it at session start (your SessionStart hook loads this alongside WORKLOG.md). Corrections accumulate instead of repeating.
>
> Format:
>
> ```markdown
> - **What happened:** [the mistake]
> - **Better approach:** [what to do instead]
> ```
>
> When the same pattern appears three times, it gets promoted to CLAUDE.md as a permanent rule. If it's universal and non-negotiable, it goes to anchor.md.
>
> Think of a mistake Claude made recently — or a habit you keep having to override. Write a lessons.md entry for it.

Wait for their entry. Then continue to Beat 9.

---

### Beat 10 / 12 — Debrief: The Promotion Path

Say:

> That's the whole format. Here's what makes it useful over time — the promotion path:
>
> Correction → **lessons.md** (loaded each session)
> Recurs 3+ times → promote to **CLAUDE.md** (permanent rule)
> Universal and irreversible if violated → promote to **anchor.md**
> Enforcement required → **hook** (harness-enforced, cannot be skipped)
>
> This is also the five-layer memory architecture — each layer holds information at a different time scale:
>
> | Layer | Primitive | Time horizon |
> |-------|-----------|-------------|
> | 1 | CLAUDE.md | Permanent — project identity and rules |
> | 2 | PROGRESS.md | Project arc — phase milestones and outputs |
> | 3 | WORKLOG.md | Session-to-session — active task state |
> | 4 | lessons.md | Long-term — patterns from corrections |
> | 5 | Hooks | Enforcement — things that must hold regardless |
>
> Together they mean nothing important is lost between sessions.
>
> Does the full picture — PROGRESS.md, WORKLOG, compaction timing, lessons.md, promotion path — make sense as a system?

Check-in: address any gaps.

---

### Beat 11 / 12 — The Three Failure Modes

Say:

> Here's the full picture of what goes wrong with session continuity, and what fixes each:
>
> 1. **Never saved** — session ends, state lives nowhere. Fix: WORKLOG + Stop hook.
> 2. **Saved but never loaded** — WORKLOG exists, SessionStart hook isn't configured. Fix: wire the hook.
> 3. **Compaction destroys state** — summarized mid-task, anchor rules disappear. Fix: compact at phase transitions; PreCompact as safety net.
>
> Which of these three is most likely to bite you first?

Check-in: brief.

---

### Beat 12 / 12 — Recall

Say:

> Six questions:
>
> 1. You open a new session. The SessionStart hook fires and reads WORKLOG.md. What must be true for this to work?
> 2. Your WORKLOG is 200 lines long. What's wrong, and what do you do?
> 3. ctx-monitor.sh warns you at 60 tool calls. You're halfway through writing a feature. What do you do — and what don't you do?
> 4. Claude made the same mistake three times. Where does the correction live now, and what happens next?
> 5. You have CLAUDE.md, WORKLOG.md, lessons.md, hooks. Which one handles "tomorrow's Claude should know what task I'm in the middle of"?
> 6. You're starting Phase 4 of a planned 5-phase project. Where does your position in the arc live — and what does WORKLOG.md know about it?

Wait for all six answers. Correct gently where needed.

Then say:

> That's Module 3. You've built the continuity layer — arc position that survives replacements, session state that survives sessions, corrections that accumulate, and timing rules for compaction.
>
> Module 4 covers encoding work itself: commands, skills, and agents — how to stop re-explaining recurring procedures and start reusing them. Ready, or questions first?

---

## Module 4: Skills, Commands, and Agents

*11 beats. Goal: learner has written a slash command for a real recurring procedure, can identify a skill candidate, and understands when to use agents.*

---

### Beat 1 / 11 — The Re-Explained Procedure

Say:

> Look at your WORKLOG. Somewhere in "Next" there's a procedure you've explained to Claude before — a code review process, a research workflow, a deployment checklist. You'll explain it again next session. And the session after that.
>
> Each time you explain it fresh, you're trusting Claude to follow the steps you describe in that moment. It might cut corners. It might miss one.
>
> Think of one thing you've asked Claude to do more than twice. What are the steps — roughly?

Check-in: let them name the procedure and its rough steps. Then continue.

---

### Beat 2 / 11 — What is a Slash Command?

Say:

> What you just described is a **command**: a file that encodes a multi-step procedure. You type `/name` and Claude follows the exact steps every time — not an improvised version, not steps 1 and 3 with 2 skipped.
>
> Commands live in `.claude/commands/`. They become available as `/commandname` after a Claude restart. They're markdown files with an optional frontmatter block:
>
> ```markdown
> ---
> description: What this command does (shows in autocomplete)
> allowed-tools: Read, Grep, Bash(git diff*)
> ---
>
> The instructions Claude follows when this command is invoked.
> ```
>
> The recipe analogy from Module 0 still holds: you don't re-invent scrambled eggs each morning. Commands give Claude the recipe.
>
> Does encoding a procedure this way seem useful for the one you named?

Check-in: wait for confirmation or questions.

---

### Beat 3 / 11 — Build: Write a Command File

Say:

> Take the procedure you named. Write it as a command file:
>
> 1. Name it `.claude/commands/[something].md`
> 2. Add a `description` frontmatter field
> 3. Add `allowed-tools` to restrict which tools Claude can use
> 4. Write the procedure as numbered steps
>
> Remove anything that was specific context you provided fresh each time — that becomes `$ARGUMENTS` (everything after the command name) instead.
>
> Share your command file stub.

Wait for their answer. Then continue to Beat 4.

---

### Beat 4 / 11 — Debrief: Command Anatomy

Say:

> Look at what you wrote. A few things to notice:
>
> The `allowed-tools` field is more important than it looks. Restricting tools signals to Claude what kind of operation this is. A review command that only needs `Read, Grep, Glob` shouldn't be accidentally writing files.
>
> `$ARGUMENTS` is how you pass context at run time. Instead of encoding `src/api/users.py` in the command, you write `Review $ARGUMENTS for quality and security issues` — then call `/review src/api/users.py`. The procedure is reusable; the target changes.
>
> Here's the question: does this command work only for your current project, or would you want it in any project you work on?

Check-in: this is the felt problem that introduces skills.

---

### Beat 5 / 11 — Skills vs Commands

Say:

> If the answer is "any project" — it's a **skill** candidate.
>
> The difference:
> - **Commands** live in `.claude/commands/` — project-local
> - **Skills** live in `~/.claude/skills/` — global, available in every project
>
> A skill is the right primitive when you're doing the same thing across multiple projects, the procedure has more than 3 steps, and you want Claude to follow a precise protocol, not improvise.
>
> The **Rule of Three** prevents over-encoding: do a task manually three times before encoding it as a skill. First time: learn it. Second time: notice the pattern. Third time: encode it. Encoding a one-off produces dead weight you'll forget to maintain.
>
> Does your command pass the Rule of Three? How many times have you actually done this procedure?

Check-in: let them apply the rule to their own case.

---

### Beat 6 / 11 — Build: Skill Readiness Check

Say:

> Before encoding anything as a skill, run the **readiness test**:
>
> 1. Can you describe what it does in one sentence? (If not, it's too broad)
> 2. Can you describe what it does NOT do? (Clear scope boundary)
> 3. Can you describe a verifiable exit state? (If not, it's not a procedure yet)
>
> If any answer is "I'm not sure" — don't encode yet.
>
> Apply the readiness test to your command. Answer all three questions out loud.

Wait for their answers. Confirm if it passes, or explain what's missing if it doesn't. Then continue.

---

### Beat 7 / 11 — Debrief: When Commands Aren't Enough

Say:

> If it passes — you have a skill candidate. If not, you have more to learn about the procedure first. That's fine.
>
> New problem: you have a review command. But when you're doing a security review, you want Claude to behave differently than it does by default — more adversarial, focused on vulnerabilities, ignoring style issues. CLAUDE.md governs the default persona. How do you give one workflow a different persona without changing CLAUDE.md for everyone?

Check-in: let them reason toward "a separate identity for that workflow."

---

### Beat 8 / 11 — Agents

Say:

> That's what **agents** are for. An agent is defined in `.claude/agents/` and has its own persona, its own tool restrictions, and can be preloaded with specific skills. When you invoke it, it acts according to its own definition — not the project default.
>
> Use an agent when you need:
> - A persona distinct from the project default (a critic, a reviewer, a domain expert)
> - Tool restrictions (a read-only agent that can't accidentally write)
> - A subagent specialized for one type of task
>
> One hard constraint: **subagents cannot spawn subagents.** Delegation is one level deep. The parent handles orchestration, not the children.
>
> Think about your project. Is there a workflow that needs a distinct persona or different toolset from the default?

Check-in: let them name one (or acknowledge they don't have one yet).

---

### Beat 9 / 11 — Build: Three-Tier Check

Say:

> Now you have the full three-tier architecture:
>
> ```
> Command (.claude/commands/)   ← entry point, project-local
>     ↓
> Agent (.claude/agents/)       ← specialized persona, tool restrictions
>     ↓
> Skill (~/.claude/skills/)     ← encoded SOP, global, cross-project
> ```
>
> Not every workflow needs all three. Most need one or two.
>
> For the procedure you encoded: which tiers does it need? Just a command? A command that calls an agent? A skill worth promoting to global?

Wait for their answer. Debrief briefly.

---

### Beat 10 / 11 — The Kitchen-Sink Warning

Say:

> One thing that goes wrong consistently with skills: encoding too much.
>
> A skill that covers 12 different workflows becomes an untested blob. Claude follows it inconsistently because different paths through it haven't been exercised. If you're writing more than ~40 lines for one skill, it's trying to do too many things — split it.
>
> The same applies to commands. A command that starts with "first, determine what kind of review this is, then..." is three commands in a trench coat.
>
> Narrow and single-purpose is the goal. Each command or skill does one thing, does it precisely, and has a clear exit state.

Check-in: brief — let them evaluate their own command.

---

### Beat 11 / 11 — Recall

Say:

> Five questions:
>
> 1. You do a code review the same way every time. You've done it manually four times. What do you build and where does it live?
> 2. The same review command would be useful in every project you work on. What does it become, and where does it live?
> 3. You want a review agent that's more adversarial than your default Claude. What primitive is this, and what makes it different from a command?
> 4. You're encoding a skill and can't describe what it does NOT do. What does that mean?
> 5. A skill is getting long — 80 lines. What's wrong, and what do you do?

Wait for all five answers. Correct gently where needed.

Then say:

> That's Module 4. You've encoded a recurring procedure and understand when commands become skills and when workflows need agents.
>
> Module 5 covers the gap that's existed since Module 0: what does "done" actually mean, and how do you verify it instead of trusting Claude's self-report? Ready, or questions first?

---

## Module 5: Verification and Defining Done

*10 beats. Goal: learner can write a Level 4 done state for any task, apply the four-level hierarchy to completed work, and name the three anti-patterns by example.*

---

### Beat 1 / 10 — "Done"

Say:

> You encoded your command. You ran it. Claude said "done."
>
> Here's the question you probably didn't ask: what did Claude mean by "done"?
>
> Language models have a natural completion signal. When Claude says it finished a task, it usually means "I produced the output" — not "the output works end-to-end." It's not deception. It's the difference between writing a recipe and tasting the dish.
>
> Think of the last thing Claude said it finished. What did you actually verify — if anything?

Check-in: let them sit with this. Don't give the answer yet.

---

### Beat 2 / 10 — The Plausible Echo Problem

Say:

> What you just described is the **plausible echo problem**: Claude can produce output that sounds correct and is wrong. Common forms:
>
> - Code that compiles but doesn't run
> - Configuration that's syntactically valid but semantically broken
> - Tests that pass but don't test the right thing
> - A function that exists but isn't connected to anything
>
> The output looks done. It reads as done. It is not done.
>
> The defense is not checking Claude's explanation more carefully. The defense is artifact verification — running it, calling it, triggering it. Don't accept "it should work" as evidence.
>
> Does this match something you've experienced?

Check-in: let them respond. Then continue.

---

### Beat 3 / 10 — Build: What Level Did You Reach?

Say:

> Here's a framework for naming the problem precisely. Four levels of verification:
>
> | Level | What it means |
> |-------|--------------|
> | **1 — Exists** | The file or output was created |
> | **2 — Substantive** | Content is real, not placeholder |
> | **3 — Wired** | Connected to the rest of the system |
> | **4 — Functional** | Works end-to-end in a real environment |
>
> Most bugs live at Level 3 (Wired) or Level 4 (Functional). "I created the function" is Level 1.
>
> Take the last thing Claude said it finished. What level did you actually reach?

Wait for their answer. Then continue to Beat 4.

---

### Beat 4 / 10 — Debrief: The Four Levels

Say:

> Levels 1 and 2 are easy to verify just by reading. Levels 3 and 4 require running something.
>
> Level 3 (Wired) is the most common hidden failure. Claude created the function. It's substantive. But it was never imported, never registered, never connected to the thing that calls it. The function is complete and useless.
>
> Level 4 (Functional) is where you actually know it works. Not "it should work" — you ran it and it did.
>
> Anything you mark done in WORKLOG should have a Level 4 verification method you've actually used.
>
> What would Level 4 verification have looked like for the task you described?

Check-in: let them describe what running it would look like.

---

### Beat 5 / 10 — Build: Write a Done State

Say:

> The fix is writing the done state before starting the task — not after. Format: *"Done = [observable, verifiable state]"*
>
> Good done states:
> - "Done = the hook fires and blocks the Read(.env) call"
> - "Done = `npm test` passes with zero failures"
> - "Done = the API endpoint returns 200 with the correct schema when called with a real token"
>
> Bad done states:
> - "Done = the hook was configured"
> - "Done = the function was added"
> - "Done = it should work"
>
> The test: can you verify it without trusting Claude's self-report?
>
> Pick your next real task. Write a Level 4 done state for it. Share it.

Wait for their done state. Then continue to Beat 6.

---

### Beat 6 / 10 — Debrief: Done State Quality

Say:

> Look at what you wrote. One question: can you run that verification yourself, right now, without asking Claude whether it worked?
>
> If yes — that's a Level 4 done state.
> If the answer is "I'd have to check with Claude" — that's Level 1 or 2 dressed up as 4.
>
> Here's a failure mode that happens once you have done states. You've defined done. Claude finishes. It says done. And then it starts the next task. Without being asked.
>
> Why is this a problem?

Check-in: let them arrive at "Claude decided what comes next instead of you."

---

### Beat 7 / 10 — Three Anti-Patterns

Say:

> That's the second of three anti-patterns worth naming:
>
> **Premature completion:** Claude self-reports done at Level 1. You accept it. Bugs live at Level 3-4. Defense: define Level 4 before starting; require proof.
>
> **Scope creep by AI:** Claude finishes and starts the next task unprompted. Defense: milestones in WORKLOG are the boundary — "when Claude finishes this task, it stops."
>
> **Loop trap:** The same fix fails twice in a row and Claude tries a third time. This is the most expensive anti-pattern — every loop iteration is wasted context. Defense: encode in anchor.md — "if the same fix fails twice, stop and ask."
>
> Have you hit any of these? Which one?

Check-in: let them identify with one.

---

### Beat 8 / 10 — Build: The Loop Trap Rule

Say:

> The loop trap is the one worth encoding in anchor.md, because by the time you're in it you don't have the presence of mind to stop. The rule needs to be there before the session starts.
>
> Write an anchor.md entry that would have prevented the anti-pattern you identified. For the loop trap specifically:
>
> ```
> If the same fix fails twice, stop immediately and ask the user. Do not retry.
> ```
>
> Write yours — either the loop trap rule or the one for the anti-pattern you identified.

Wait for their entry. Then continue to Beat 9.

---

### Beat 9 / 10 — Debrief: Verification as Habit

Say:

> The four-level hierarchy, the done state, the anti-patterns — none of these require special tooling. They're habits. The question to ask at the start of every task: "What does Level 4 look like for this?"
>
> Ask it before starting. Write the answer in WORKLOG under "Next":
>
> ```
> 1. **[Task]** — [description] | Done = [verifiable Level 4 state]
> ```
>
> The done state in WORKLOG is the same one from this module. Everything connects.
>
> Which of these — the plausible echo problem, the four levels, the three anti-patterns — was most new to you?

Check-in: brief.

---

### Beat 10 / 10 — Recall

Say:

> Five questions:
>
> 1. Claude says it finished adding a feature. What level of verification does that claim represent, and what would Level 4 look like?
> 2. Your done state is: "Done = the function was added." What's wrong with it, and how do you fix it?
> 3. Claude finishes a task and starts the next one without being asked. Which anti-pattern is this?
> 4. The same fix has failed twice. What do you do, and what anchor.md rule prevents the loop trap?
> 5. You're writing a WORKLOG Next item. Where does the done state go, and what format does it use?

Wait for all five answers. Correct gently where needed.

Then say:

> That's Module 5. You've defined done in a way that can actually be verified, and named the failure modes that make AI-assisted work go wrong.
>
> Module 6 covers advanced patterns: what to do when the project grows, when you need parallel work, and when the foundation shows scaling limits. Ready, or questions first?

---

## Module 6: Advanced Patterns

**12 beats.** One beat per response — Claude stops and waits for you after each.

---

Beat 1 / 12:

Here is the felt problem that closes Module 5.

You verified your work. You defined done. You enforced the loop trap. Everything works — for one task, in one session, in one project.

Now the project grows. Fifty files. Three parallel workflows. Tasks that could run at the same time. A teammate joins. The context window fills faster. Claude starts forgetting things it knew yesterday. You run the same hook in two places and they drift out of sync.

The foundation holds — but it's starting to show cracks.

Module 6 is about what to reach for when the foundation isn't enough. These are not day-one patterns. They are solutions to problems you have to feel before they make sense. You'll know when you need each one.

What does "the project grows" look like for you right now — more files, more workflows, more people, or something else? Tell me what's starting to strain, and we'll work toward those patterns first.

---

Beat 2 / 12:

The first thing that breaks at scale is context.

You already know context degrades before it's full — Module 0 established that. At 20–40% degradation, output quality drops. Module 2 introduced `ctx-monitor.sh`, the PostToolUse hook that warns you at 60 tool calls.

Two things happen when a project grows that make this worse:

**Problem 1 — Too many files.** In a small project, Claude can orient itself by reading a few files. In a 50-file project, it reads the wrong ones, loads too much into working memory, and starts conflating pieces of different subsystems.

**Problem 2 — Compaction loses more.** When you compact a small project, the summary captures most of what mattered. When you compact a large one, the summary is blurrier — there's more to compress and more that gets dropped.

The fix for both is a `_MANIFEST.md` at the project root. Not documentation — a navigation index. Five to ten lines that tell Claude what each directory contains and what each major file does, so it can orient itself without loading the full codebase.

Before I show you the format, answer this: does your current project have enough files that Claude sometimes opens the wrong one, or loads files it didn't need? Yes or no — and roughly how many files are we talking?

---

Beat 3 / 12: Build

Here is what a `_MANIFEST.md` looks like:

```markdown
# Project Manifest

## Directories
- `src/` — main application code
- `tests/` — unit and integration tests
- `scripts/` — one-off automation, not part of the app
- `.claude/` — Claude's working files (CLAUDE.md, hooks, commands)

## Key Files
- `src/config.py` — all configuration loaded here; do not hardcode elsewhere
- `src/pipeline.py` — main data pipeline; entry point for most tasks
- `WORKLOG.md` — session state; read this first
- `DECISIONS.md` — architectural decisions; read before proposing changes
```

Claude doesn't auto-read this on its own — you make it load. Two paths: (a) reference it from CLAUDE.md ("Read `_MANIFEST.md` before searching for files"), or (b) wire a SessionStart hook (built in Module 2) that prints "Read `_MANIFEST.md` first" to stdout — Claude treats hook stdout as additional context. Once wired, it acts as a map — Claude knows where to look without guessing.

Your turn: write a `_MANIFEST.md` stub for your current project. Five to ten lines. Directories and key files only. Real project, real paths.

When you're done, paste it here.

---

Beat 4 / 12:

Good. That stub is worth adding to your project root — even a rough one reduces misdirection.

Now the second crack that appears at scale: model selection.

You've been using one model for everything. That works when tasks are similar. It stops making sense when they diverge — planning an architecture, implementing a function, and checking a log file are fundamentally different cognitive loads.

Three models, three use cases:

| Model | Use for |
|-------|---------|
| **Opus** | Planning, architectural decisions, complex multi-step reasoning |
| **Sonnet** | Implementation — 80–90% of daily work |
| **Haiku** | Repetitive subtasks, simple lookups, batch processing |

The community pattern: *"Get a plan from Opus, execute with Sonnet, spot-check with Haiku."*

This isn't about cost — it's about matching the tool to the job. Opus on a log lookup is wasteful. Haiku on an architectural decision produces shallow output.

Which model are you using now for everything? And can you name one task in your current project that deserves Opus, and one that could safely run on Haiku?

---

Beat 5 / 12: Build

Name them concretely:

1. **One task that deserves Opus** — something with multiple moving parts, architectural consequences, or where a wrong decision is costly. Write one sentence describing the task and why it needs Opus.

2. **One task that could run on Haiku** — something repetitive, mechanical, or low-stakes where speed and cost matter more than depth. Same format.

If your project doesn't have either yet, name the closest approximation — what's the most complex thing you've planned, and what's the most repetitive thing you've done?

Paste both when you're ready.

---

Beat 6 / 12:

That's the right kind of split. When you get to those tasks, switch models explicitly — don't let the default carry everything.

Now the third crack: parallelism.

The setup so far is single-threaded — one Claude instance, one task at a time. That works until you have tasks that don't depend on each other and could run simultaneously. Running them in sequence wastes time and compounds context load.

**Parallel agents** let multiple Claude instances work at once. But two constraints make this harder than it sounds:

**One-writer rule.** Only one agent may write to a file at a time. Last write wins, and concurrent writes corrupt state silently — no error, just lost work. Parallel agents must be assigned non-overlapping file sets. If they touch the same file, you've introduced a race condition.

**Git worktrees.** Each agent gets an isolated copy of the repo in its own worktree. Changes are visible only in that worktree until merged. This is how you give parallel agents the isolation they need without managing separate repos.

**Subagents cannot spawn subagents.** The parent orchestrates. The children execute. If a child tries to delegate further, the constraint breaks — design the hierarchy to be exactly two levels deep.

Does any workflow in your current project have two tasks that could run in parallel without touching the same files? Tell me what they are.

---

Beat 7 / 12: Build

Design one parallel workflow.

Pick two tasks from your project (real or plausible) that:
- Don't depend on each other's output
- Touch non-overlapping files

Write:
1. **Task A** — what it does, which files it writes
2. **Task B** — what it does, which files it writes
3. **How the parent knows both are done** — what does it check?

If your project genuinely has no parallelizable work yet, design it hypothetically for the three-workflow scenario: feature development, code review, and deployment. Same format.

---

Beat 8 / 12:

Good. The one-writer rule is the constraint to hold — if you can't guarantee non-overlapping files, serialize the tasks instead.

Now a smaller fix for a problem you've probably already hit: CLAUDE.md bloat.

As a project matures, CLAUDE.md accumulates rules. Some apply everywhere. But some only matter when you're editing Python files, or running database migrations, or working inside the KB. Loading all of them always adds context overhead and noise.

**Path-scoped rules** solve this. The `.claude/rules/` directory holds rule files with a `paths:` frontmatter field. Claude loads them only when files matching that pattern are read. Rule files **without** a `paths:` field load unconditionally at session start (like CLAUDE.md does).

```markdown
---
paths: ["src/**/*.py"]
---

- All functions must have type annotations
- Never use bare `except:` — always catch a specific exception type
- Run `mypy src/` before marking any task done
```

These rules don't appear in CLAUDE.md at all. They load conditionally, keep the always-loaded context lean, and put rules where they're relevant.

One caveat worth knowing: path-scoped rules **also don't survive compaction** — they're lost until a matching file is read again, at which point they reload. So they're great for keeping context lean but bad as a place for rules where one miss is unacceptable. That kind of rule still belongs in a hook or a `deny` pattern.

Does your project have rules that only apply to a specific file type or directory? If yes, name one. If not, describe what would go in a KB-editing rule file for this workspace.

---

Beat 9 / 12: Build

You've already seen this pattern in this workspace — `DECISIONS.md` records significant decisions with a **Rejected** field.

The Rejected field is the most important part. Without it, a future session (or a future collaborator) re-proposes the rejected option. The decision gets relitigated. The context explaining why it was rejected is gone.

The format:

```markdown
## Decision: [What was decided — noun phrase]
**Date:** [YYYY-MM-DD]
**Rejected:** [What was considered and not chosen — specific, not vague]
**Why:** [The actual reason — one sentence]
```

Short decisions with no lasting consequences: record under Key Decisions in WORKLOG.md. Architectural decisions with implications for future sessions: record in a dedicated DECISIONS.md.

Your turn: pick a real decision you've made in this project or in your development work — something where you chose one path and ruled out another. Write it as a decision trace using the format above. Include the Rejected field.

---

Beat 10 / 12:

That's the discipline that prevents re-relitigating old ground. The Rejected field is doing the work future-you will thank present-you for.

Now the loop that ties everything together: self-improvement.

You've been building the pieces all along — lessons.md, anchor.md, hooks, skills. The loop that connects them:

1. **Bug.** Something breaks, or you correct Claude's behavior.
2. **Lesson.** Write it to `tasks/lessons.md` immediately — what happened, what the better approach is.
3. **Promotion.** If the lesson recurs (3+ times), encode it: as a skill if it's a procedure, as an anchor.md rule if it's universal, as a hook if it must be enforced.
4. **Review.** Every 10 sessions, scan lessons.md. Entries older than 3 sessions that haven't been promoted are either universal (promote them) or too specific (delete them).

The loop has a failure mode: the lesson gets written and never reviewed. It stays buried. The mistake recurs. The hook that would have prevented it never gets written.

The cadence — 10 sessions, 3 sessions — is what prevents burial. It's not a guideline; it's the mechanism.

Where in this loop does your current practice break down? Do you write lessons but not promote them? Skip the review? Never write the lesson in the first place?

---

Beat 11 / 12:

One last ceiling: MCP servers.

Every MCP server you add costs latency and token overhead at every tool call. This compounds. A project with 15 active MCP servers and 120 tools is slower and noisier than the same project with 5 servers and 40 tools — even if most of those tools are never called.

Two practitioner heuristics worth treating like limits (not enforced by Claude Code, but past them the noise dominates the value):
- Around 10 MCP servers per project
- Around 80 tools total across all MCPs

Rules that follow from those limits:
- If you're not actively using a server, remove it — you can add it back
- Document each server's purpose in CLAUDE.md so Claude knows when to reach for it
- When adding a new server, check the current count first

The broader caution for everything in this module: advanced patterns are solutions to problems you haven't hit yet. Parallel agents, `_MANIFEST.md`, path-scoped rules, and model routing add complexity. Apply them when you feel the pain they solve — not before. The foundation from Modules 0–5 is sufficient for most solo projects indefinitely.

One question before the recall: which pattern from this module are you most likely to reach for first — and what's the problem you're already feeling that it would solve?

---

Beat 12 / 12: Recall

Six questions — one per major concept. Answer from memory. Short answers.

**1.** Your project has 60 files and Claude keeps opening the wrong ones. What do you create, and what goes in it?

**2.** You need to plan a new data pipeline architecture with three interacting components. Which model do you use, and why not Haiku?

**3.** You want two agents to work in parallel. What's the one constraint you must verify before starting them simultaneously?

**4.** Your CLAUDE.md has 30 rules, half of which only apply when editing `.sql` files. What do you do, and where does the file go?

**5.** A future session proposes using a library you already evaluated and rejected three months ago. What record would have prevented this, and what's the field that does the work?

**6.** You wrote a correction to lessons.md six sessions ago. It's still sitting there. What should happen to it now?

---

When you're done, check your answers against the module. If any felt uncertain, go back to that beat before moving on.

---

## Quick Reference

| You want to... | Go to |
|---------------|-------|
| Set up a new project from scratch | `/new-project` |
| Understand the mental model | `/learn` or `/learn 0` |
| Write better CLAUDE.md and context files | `/learn claude` |
| Configure hooks and enforcement | `/learn hooks` |
| Set up WORKLOG and session continuity | `/learn worklog` |
| Create commands, skills, or agents | `/learn skills` |
| Define done states and verify work | `/learn verification` |
| Handle parallel agents and large projects | `/learn advanced` |
| Check workspace health | `/kb-status` |
| Deep-dive on a specific decision | `/kickoff [task]` |
