# useful-ai-prompts

Reusable prompts for AI coding assistants.

## Commands

The [`commands/`](commands/) directory holds slash commands for
[Claude Code](https://claude.com/claude-code). To install one, copy it to
`~/.claude/commands/` (available in every project) or a project's
`.claude/commands/` directory, then invoke it by filename, e.g.
`/cross-check-review`. The prompts are plain Markdown, so they also work
pasted into any capable AI assistant.

- [`cross-check-review`](commands/cross-check-review.md) — a layered review
  process that finds real bugs by comparing two representations of the same
  intent: prose vs. code, names vs. values, code vs. official references
  (RFCs, PEPs, service API docs), and coverage vs. reachability.
  Each layer reports findings for confirmation, and every suspected bug must
  be demonstrated empirically before it is fixed. An optional final layer
  requests a Copilot review of the pull request and iterates — responding to
  every comment and resolving each conversation — until a pass raises no new
  actionable issues.

## License

[MIT](LICENSE)
