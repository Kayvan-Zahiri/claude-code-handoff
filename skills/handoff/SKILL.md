---
name: handoff
description: Write HANDOFF.md at the repository root so Codex, Cursor or a new Claude Code session can continue this task. Run it before you switch agents.
argument-hint: "[note for the next agent]"
disable-model-invocation: true
allowed-tools:
  - Bash(git rev-parse *)
  - Bash(git branch *)
  - Bash(git ls-files *)
  - Bash(git status *)
  - Bash(git diff *)
  - Bash(git log *)
  - Edit(HANDOFF.md)
---

The developer is about to continue this task in another agent: Codex, Cursor or a new Claude Code session. That agent cannot see this conversation. Write one file, HANDOFF.md, so it can carry on without the developer explaining the task again.

## Snapshot, taken when /handoff ran

An empty value, or "(Bash completed with no output)", means none. Outside a git repository, skip the git values. Otherwise, if a value shows an error or says shell commands are disabled, run that git command yourself or leave the detail out.

Repository root: !`git rev-parse --show-toplevel || echo "(not a git repository)"`

Branch (empty when HEAD is detached): !`git branch --show-current 2>/dev/null || echo "(unknown)"`

Existing HANDOFF.md at the root: !`git ls-files --cached --others -- ":/HANDOFF.md" 2>/dev/null || echo "(unknown)"`

git status --short, paths relative to the current directory:
!`git status --short 2>/dev/null || echo "(unavailable)"`

git diff --stat, not staged, paths from the repository root:
!`git diff --stat=200 --stat-count=60 2>/dev/null || echo "(unavailable)"`

git diff --cached --stat, staged:
!`git diff --cached --stat=200 --stat-count=60 2>/dev/null || echo "(unavailable)"`

Last 10 commits:
!`git log --oneline -n 10 2>/dev/null || echo "(no commits yet)"`

Note typed after /handoff: $ARGUMENTS

## Steps

1. Write to `<repository root>/HANDOFF.md`. Outside a git repository, write `HANDOFF.md` in the current directory and leave out the git details.
2. If HANDOFF.md already exists, Read it first. The snapshot names it when it exists; outside a git repository, just try to Read it.
   - If its first line starts with `Next agent: read this whole file`, an earlier /handoff wrote it. Replace it. If this session started from that file, keep its quoted request first in the new one, because those are the developer's original words.
   - Otherwise it is not from /handoff. Do not overwrite it. Ask the developer what to do, and stop.
3. Write HANDOFF.md from the template below, following the rules.
4. Reply in at most three lines: where the file is, and that the next agent should start with "Read HANDOFF.md, then continue."

## Rules

- Quote the developer exactly: character for character, typos included. Never paraphrase inside quotation marks.
- Quote the message that started this task and every later message that changed it: corrections, constraints, priorities and the next step they asked for. Skip messages such as "ok" or "continue". Trim only very long messages, such as pasted logs, and mark each cut with [...].
- Never invent a quote. If the start of the task has dropped out of this conversation, for example after compaction, write "The original wording is not available in this session." and quote what you still have.
- Write only what this session showed. Mark anything you did not verify as "not verified". Say a check passed only if it ran in this session and you saw it pass.
- Replace secrets such as API keys, tokens, passwords and private keys with [redacted], inside quotes too.
- Change nothing except HANDOFF.md. Do not run tests or builds. Do not stage, commit or push.
- Keep every heading. Under a heading with nothing to report, write "None."
- Keep it under about 120 lines. Name files and commands; do not paste code or whole diffs.

## Template

````markdown
Next agent: read this whole file before you do anything else, then start at "Next step".

# Handoff: <the task in a few words>

Written by /handoff in Claude Code on <date>. Branch: <branch>. Check claims against the code before you rely on them.

## The developer's request, in their own words

> <the message that started this task, quoted exactly>

Later instructions, quoted exactly, oldest first:

> <message>

Note at handoff: <the note typed after /handoff, quoted exactly; leave this line out if there is none>

## Decisions made

- <decision>. Why: <reason>. Decided by: <the developer, or the agent without the developer's confirmation>.
- Tried and dropped: <approach>. Why: <reason>.

## What changed

```
<git status --short and git diff --stat from the snapshot; past about 40 lines, keep the files that matter and say how many lines you left out>
```

- `<path>`: <what changed and why, in one line>
- Commits made in this session: <hash and subject, or "none">

## Done

- <item>. Checked: <command and what it showed, or "not checked">.

## Left to do

- <item>

## Next step

<the first thing the next agent should do, as concretely as you can>

## How to check the work

- `<command>`: <what it showed in this session, or "not run in this session">
- <a manual check, if any>
````
