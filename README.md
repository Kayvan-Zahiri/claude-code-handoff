# handoff

A Claude Code plugin for switching agents. Before you leave Claude Code for Codex, Cursor or a new Claude Code session, type `/handoff`. It writes `HANDOFF.md` at the root of your repository, and the next agent reads that file instead of you explaining the task again.

The note leads with your request in your own words, as you typed it in the session. Then it lists the decisions made, what changed, what is done, what is left, the next step and how to check the work.

## Install

In a Claude Code session:

```text
/plugin marketplace add Kayvan-Zahiri/claude-code-handoff
/plugin install handoff@handoff
```

Or from your shell:

```bash
claude plugin marketplace add Kayvan-Zahiri/claude-code-handoff
claude plugin install handoff@handoff
```

On Claude Code v2.1.292 or later, one shell command does both: `claude plugin install handoff --marketplace Kayvan-Zahiri/claude-code-handoff`.

To try it for one session without installing anything:

```bash
git clone https://github.com/Kayvan-Zahiri/claude-code-handoff
claude --plugin-dir ./claude-code-handoff
```

## Use

1. Before you switch, type `/handoff` in Claude Code. You can add a note for the next agent: `/handoff the login test still fails on CI`.
2. Open the next agent in the same repository and start with: `Read HANDOFF.md, then continue.`

Run it while Claude Code can still answer. `/handoff` needs one more model turn to write the note, so it can't run after a usage limit has stopped the session.

If another skill already uses `/handoff`, type `/handoff:handoff`.

## What HANDOFF.md contains

- **First line**: tells the next agent to read the whole file before it does anything else.
- **Your request, in your own words**: the message that started the task and every later message that changed it. The skill asks for exact quotes, typos included. If the start of the task has dropped out of the session, for example after compaction, the note says so instead of guessing.
- **Decisions made**: what was decided, why, who decided it, and approaches that were tried and dropped.
- **What changed**: `git status --short` and `git diff --stat` from the moment you ran `/handoff`, one line per important file, and the commits made in the session.
- **Done**: each finished item and how it was checked, or "not checked".
- **Left to do** and **Next step**: what remains, and the first thing the next agent should do.
- **How to check the work**: the commands used in the session and what they showed, or "not run in this session".

Here is the shape, as an illustration rather than output from a real session:

```markdown
Next agent: read this whole file before you do anything else, then start at "Next step".

# Handoff: CSV export for the orders page

Written by /handoff in Claude Code on 2026-10-13. Branch: csv-export. Check claims against the code before you rely on them.

## The developer's request, in their own words

> add a csv export button to the orders page, same filters as the table

Later instructions, quoted exactly, oldest first:

> dont build the whole file at once, some customers have 200k orders

## Decisions made

- Stream rows from the query instead of building the whole file first. Why: the 200k-row case. Decided by: the developer.
- Tried and dropped: the table component's built-in export. Why: it ignores the server-side filters.

## Next step

Pass the table's filters to `export_orders()` and add a test for an empty result.
```

## What it runs

- One skill and nothing else: no hooks, no MCP servers, no background processes, no scripts.
- When you type `/handoff`, Claude Code runs read-only git commands in your repository: `git rev-parse --show-toplevel`, `git branch --show-current`, `git ls-files`, `git status --short`, `git diff --stat`, `git diff --cached --stat` and `git log --oneline`.
- The skill pre-approves writes to files named `HANDOFF.md`, so Claude Code usually writes the note without a permission prompt. It tells Claude Code not to run tests or builds, not to stage, commit or push, and not to change any other file.

## Privacy

- No server, no account and no telemetry. The plugin makes no network calls of its own.
- `HANDOFF.md` is written to your disk and goes nowhere else.
- The note is written by the Claude Code session you are already in, so the git snapshot reaches the model the same way the rest of that conversation does. No other service is involved.
- The skill asks for secrets such as API keys and tokens to be replaced with `[redacted]`. This is best-effort, so read the file before you commit or share it.

## Limits

- It can only quote what the session still holds. After `/compact` or an automatic compaction, your first message may be gone.
- The model in your session writes the note, and it can be wrong or incomplete. The note tells the next agent to check claims against the code.
- It does not commit `HANDOFF.md`. Add `HANDOFF.md` to `.gitignore` if you want it out of your history.
- If a `HANDOFF.md` that `/handoff` did not write is already there, the skill tells Claude Code to ask you before it replaces it.
- Outside a git repository it writes `HANDOFF.md` in the current folder, without the git details.
- The git snapshot is written for bash and zsh, the shells Claude Code's Bash tool uses. Where Claude Code runs commands in PowerShell instead, the snapshot can fail.
- Writing the note uses part of your Claude Code usage, like any other turn.

## Update and uninstall

```bash
claude plugin update handoff@handoff
claude plugin uninstall handoff@handoff
```

In a session, `/plugin uninstall handoff@handoff` does the same as the second command.

## License

MIT. See [LICENSE](LICENSE). This is an independent project, not made by or affiliated with Anthropic.

I also make BuildGist, which offers this when you switch between Claude Code, Cursor and Codex on an Apple silicon Mac, and explains each meaningful run: https://buildgist.com/?ref=gh
