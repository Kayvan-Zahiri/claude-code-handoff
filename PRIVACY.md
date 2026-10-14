# Privacy policy for handoff-note

Effective 2026-10-14.

handoff-note is a Claude Code plugin by Kayvan Zahiri. It has no server, no account and no telemetry, and it collects nothing about you.

## What it reads

When you type `/handoff`, Claude Code reads the current session and runs seven read-only git commands in your repository: `git rev-parse --show-toplevel`, `git branch --show-current`, `git ls-files --cached --others -- ":/HANDOFF.md"`, `git status --porcelain`, `git diff --stat=200 --stat-count=60`, `git diff --cached --stat=200 --stat-count=60` and `git log --oneline -n 10`.

## What it writes

One file, `HANDOFF.md`, at the root of your repository, or in the current folder outside a repository. It changes no other file.

## What it sends

The plugin makes no network calls of its own and sends nothing to its author or to anyone else.

The note is written by the Claude Code session you are already in. The git snapshot reaches the model the same way the rest of that conversation does, under the terms you already have with your model provider. `HANDOFF.md` goes nowhere until another agent reads it. From then on it is part of that agent's chat, like any other file it reads.

## What it keeps

Nothing. `HANDOFF.md` stays on your disk until you delete it.

## Secrets

The skill asks Claude Code to replace secrets such as API keys, tokens and passwords with `[redacted]`. This is best-effort, so read the file before you commit or share it.

## Who it is for

Developers. It is not intended for people under 18.

## Contact and changes

Questions: open an issue at https://github.com/Kayvan-Zahiri/claude-code-handoff/issues. Changes to this policy are made in this file, and its history is in the repository's commits.
