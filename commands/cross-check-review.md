---
description: Layered cross-check review — prose vs. code, names vs. values, coverage vs. reachability
---

Run a layered cross-check review of this codebase. Each layer compares two
representations of the same intent — where they disagree, one of them is a bug.
Work one layer at a time, and report findings for my confirmation before fixing.

If arguments are given, limit the review to them (paths, modules, or a subset
of layers): $ARGUMENTS

Layer 1 — Prose vs. code. Review all documentation, docstrings, comments, and
user-facing strings (errors, warnings, log messages, CLI help) for spelling,
grammar, clarity, and above all accuracy: verify every concrete claim against
the code itself — documented parameters, defaults, and return shapes against
actual signatures and constants; usage examples and doc-embedded help text
against real program output; each error/warning message against the condition
that actually triggers it (a message can state the opposite of its check).
Flag copy-pasted text describing the wrong function, module, or protocol.

Layer 2 — Names vs. values. Review identifiers (variables, parameters,
constants, type and field names, CLI flags) for names that misdescribe what
they hold or do: variables reassigned to a different type or meaning
mid-function; declared struct/type fields that no code ever produces;
configuration knobs wired to the wrong constant (leaving a documented setting
dead); singular/plural mismatches; names copy-pasted from a sibling module;
the same concept named differently across sibling APIs.

Layer 3 — Coverage vs. reachability. For each uncovered line, determine
whether the branch is genuinely reachable and whether the condition guarding
it is correct BEFORE writing a test. A nearly-unreachable error branch usually
means the validation above it is broken — fix the validation. Never write a
test that pins broken behavior, and never fake coverage.

Rules for every layer:

- Findings are hypotheses. Confirm each suspected bug empirically — run code
  that demonstrates the wrong behavior — before fixing, and report any claim
  that fails confirmation as not confirmed.
- Every confirmed bug fix gets a regression test asserting observable
  behavior; tests that assert on changed strings are updated in the same change.
- Renamed public API keeps the old name as a deprecated alias that warns.
- After each layer, run the full test suite, linter, and type checker, and
  record fixes in the changelog if the project keeps one.
- Use parallel review agents to sweep large surfaces, but verify every quoted
  finding against the actual source before acting on it.

Deliver each layer's findings ranked by severity (bugs concealed by the
mismatch first, then user-facing text problems, then internal nits), each with
file:line, verbatim evidence, and the proposed fix.
