---
description: Address the latest Copilot review on the current PR — fix or decline each finding with evidence, push, reply on every inline thread, and answer every finding Copilot listed only in its review body (suppressed, previously missed) in one PR comment
---

Address the latest Copilot code review on this pull request. Every comment is
a hypothesis, not a verdict: evaluate each one on its merits against the
actual code, fix what is confirmed, decline what is not, and leave a written
record on the PR of which happened and why.

If an argument is given, treat it as the pull request number or branch to work
on; otherwise use the pull request for the current branch: $ARGUMENTS

Step 1 — Gather the review. Identify the pull request (`gh pr view`) and fetch
the most recent review submitted by Copilot (the `copilot-pull-request-reviewer`
bot) together with all of its inline comments (`gh api --paginate
repos/{owner}/{repo}/pulls/{number}/reviews` and `gh api --paginate
.../pulls/{number}/comments`, filtered by review id). Always paginate: the
API returns thirty items per page, and every reply you post on a thread is
itself recorded as a review, so after a round or two the newest Copilot
review sits past the first page and an unpaginated listing makes it look as
if there is no new review at all. If the newest review you find is one you
already answered, check the pull request's inline comments for a newer
Copilot comment before concluding nothing arrived.

Read the review body in full, every section, not just the list of inline
findings. Copilot's overview groups findings under headings, and some of
those groups have no inline thread at all: "Comments suppressed due to low
confidence" and "Previously missed" (findings in code that did not change
since the last review, listed only in the body). Any finding that appears in
the body without a matching inline comment still needs an answer. Do not
skim the body by filtering it to bullet lines or link anchors, since the
non-inline findings are written as paragraphs under their own headings and
that is exactly how they get overlooked. Before moving on, reconcile the
count: the number of findings the body claims must equal the inline comments
plus the body-only findings you collected.

Also list every earlier Copilot review on the pull request and note any
inline comment from a prior round that still has no reply.

Step 2 — Evaluate each finding. For every inline and body-only finding, read
the surrounding code rather than the quoted snippet alone, and confirm or
refute the claim empirically — run the code, a test, or a reproduction that
demonstrates the behavior. Decide one of:

- Fix: the finding is confirmed, or the suggested change is a clear
  improvement. Make the change, add or update a regression test asserting the
  observable behavior where a bug was confirmed, and keep any renamed public
  API available under its old name as a deprecated alias that warns.
- Decline: the code is correct as written, the suggestion would be worse, or
  the finding is out of scope for this pull request. Record concrete evidence
  for the decision (the guarding condition, the spec section, the test that
  covers it, the command output) so the reply can cite it.

Do not fix a finding you cannot confirm just to make the comment go away, and
do not decline a finding merely because it is inconvenient.

Step 3 — Verify and push. Run the full test suite, linter, and type checker.
Commit the accepted changes with a message that says what was fixed and that
the changes respond to Copilot review, record fixes in the changelog if the
project keeps one, and push to the pull request branch. Note the commit hash;
the replies reference it.

Step 4 — Reply to every inline comment. Post each response as a reply on
that comment's own thread (`gh api
repos/{owner}/{repo}/pulls/{number}/comments/{comment_id}/replies`), never as a
top-level pull request comment, so the discussion stays next to the code it
concerns. Do not quote or paraphrase Copilot's finding in the reply: it sits
directly above, and repeating it only pads the thread. Open with a short
attribution that names Copilot as the source of what is being answered
("Copilot flagged this because ...", "Copilot asked for ..."), then state the
outcome:

- For a fix: what changed and the commit that contains it.
- For a decline: why the code is correct or the suggestion was not taken,
  with the evidence gathered in Step 2.

End every reply with the Claude Code attribution line:

    🤖 Generated with [Claude Code](https://claude.com/claude-code)

Resolve each conversation once it has been answered either way. Do the same
for any unanswered comment from an earlier Copilot review found in Step 1, so
that no Copilot comment on the pull request is left without a response.

Step 5 — Answer the body-only findings. Suppressed and previously-missed
findings have no inline thread to reply to, so post one pull request comment
(`gh pr comment`) that, for each of them, quotes the finding from Copilot's
review body (this is the one place quoting is needed, since nothing on the
page otherwise shows what is being answered), names Copilot as its source,
and gives the same fix-or-decline outcome, with evidence, as an inline reply
would. End the comment with the same Claude Code attribution line. If the
review body lists no finding outside the inline comments, say so briefly in
the final report instead of posting an empty comment.

Rules:

- Attribute every reply and comment: name Copilot as the source of the
  finding, and end with the Claude Code attribution line. Quote the finding
  only in the PR comment for body-only findings, never in an inline reply.
- Never reply "done" or "fixed" without saying what changed; never decline
  without evidence.
- Do not re-request a Copilot review as part of this command; that is a
  separate decision.
- If the pull request has no Copilot review, or Copilot is not enabled for the
  repository, say so and stop rather than substituting a self-review.

Finish with a short report: one line per finding (inline, prior-round, and
body-only) with its outcome (fixed in <commit>, declined because <reason>),
plus anything left outstanding.
