---
description: Layered cross-check review — prose vs. code, names vs. values, code vs. spec, coverage vs. reachability, optional Copilot review loop
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

Layer 3 — Code vs. official references. Wherever the code implements or
consumes a published standard or external interface — an RFC, a W3C/WHATWG
spec, a language proposal (PEP or equivalent), a wire protocol or file format,
or a third-party service's API — verify the implementation against the current
official text, fetched fresh rather than recalled from memory. Check status
codes and their semantics, header and field names (and casing), required vs.
optional fields, value formats (dates, encodings, MIME types, units), limits,
ordering and state-machine rules, and error handling against what the code
actually sends, accepts, and validates. Flag conformance to obsoleted or
superseded versions: an RFC with a successor, a deprecated API version or
endpoint, parameters the vendor has removed or renamed. Cite the exact
reference (RFC number and section, PEP number, doc URL) as evidence for each
finding, and distinguish MUST violations from SHOULD deviations.

Layer 4 — Coverage vs. reachability. For each uncovered line, determine
whether the branch is genuinely reachable and whether the condition guarding
it is correct BEFORE writing a test. A nearly-unreachable error branch usually
means the validation above it is broken — fix the validation. Never write a
test that pins broken behavior, and never fake coverage.

Layer 5 — Second set of eyes (only if requested, and only if available). If
this layer was asked for and the work is on a pull request where the Copilot
code reviewer can be requested, request a balanced review from Copilot once
the other layers' fixes are pushed. Treat its comments like any other
findings — hypotheses, not verdicts: evaluate every comment on its merits
against the actual code, fix what is confirmed, and where you disagree, reply
with concrete evidence for why the code is correct as written. Respond to
every inline comment as a reply on its own thread, naming Copilot as the
source and quoting the finding, and resolve each conversation once it is
addressed either way. Copilot also lists low-confidence findings it chose not
to post inline in its review body (under a heading such as "Comments
suppressed due to low confidence"); evaluate those exactly like the inline
ones and, since they have no thread to reply to, answer them in a single
pull request comment that quotes each suppressed finding and gives its
fix-or-decline outcome with evidence. Then push the resulting fixes and
re-request review. Bound the loop: stop
after the first round that surfaces no newly confirmed bug, and run at most
three rounds regardless — a point already answered with evidence and merely
restated does not count as new. If the third round still surfaces confirmed
bugs, stop anyway and report what remains outstanding instead of iterating
further. If Copilot is unavailable, say so and skip this layer rather than
substituting a self-review for it.

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
