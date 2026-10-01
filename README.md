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
  requests a Copilot review of the pull request and iterates — replying to
  every inline comment with attribution, answering suppressed low-confidence
  findings in a PR comment, and resolving each conversation — stopping after
  the first round that surfaces no newly confirmed bug, and after three rounds
  at most.
- [`address-copilot-review`](commands/address-copilot-review.md) — works
  through the latest Copilot review on the current pull request: confirms or
  refutes each finding empirically, fixes or declines it, pushes, replies on
  each inline thread with the outcome and evidence (including unanswered
  comments from earlier rounds), and answers the findings Copilot lists only
  in its review body (suppressed low-confidence ones and "previously missed"
  ones) in a single PR comment. Every reply and comment names Copilot as the
  source and carries the Claude Code attribution line.

## License

[MIT](LICENSE)
