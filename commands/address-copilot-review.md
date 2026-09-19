---
description: Address the latest Copilot review on the current PR — fix or decline each finding with evidence, push, reply inline with attribution, and answer suppressed findings in a PR comment
---

Address the latest Copilot code review on this pull request. Every comment is
a hypothesis, not a verdict: evaluate each one on its merits against the
actual code, fix what is confirmed, decline what is not, and leave a written
record on the PR of which happened and why.

If an argument is given, treat it as the pull request number or branch to work
on; otherwise use the pull request for the current branch: $ARGUMENTS

Step 1 — Gather the review. Identify the pull request (`gh pr view`) and fetch
the most recent review submitted by Copilot (the `copilot-pull-request-reviewer`
bot) together with all of its inline comments (`gh api
repos/{owner}/{repo}/pulls/{number}/reviews` and `.../pulls/{number}/comments`,
filtered by review id). Read the review body as well as the inline comments:
Copilot lists low-confidence findings it chose not to post inline under a
heading such as "Comments suppressed due to low confidence". Collect those too;
they have no inline thread but still need an answer. Also list every earlier
Copilot review on the pull request and note any inline comment from a prior
round that still has no reply.

Step 2 — Evaluate each finding. For every inline and suppressed finding, read
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

Step 4 — Reply to every inline comment, with attribution. Post each response
as a reply on that comment's own thread (`gh api
repos/{owner}/{repo}/pulls/{number}/comments/{comment_id}/replies`), never as a
top-level pull request comment, so the discussion stays next to the code it
concerns. Every reply names the source and quotes or paraphrases the finding
so a reader arriving later knows who raised it and what was asked, then states
the outcome:

- For a fix: what changed and the commit that contains it.
- For a decline: why the code is correct or the suggestion was not taken,
  with the evidence gathered in Step 2.

Resolve each conversation once it has been answered either way. Do the same
for any unanswered comment from an earlier Copilot review found in Step 1, so
that no Copilot comment on the pull request is left without a response.

Step 5 — Answer the suppressed findings. Suppressed findings have no inline
thread to reply to, so post one pull request comment (`gh pr comment`) that
quotes each suppressed finding from Copilot's review body and gives the same
fix-or-decline outcome, with evidence, as an inline reply would. If the review
suppressed nothing, say so briefly in the final report instead of posting an
empty comment.

Rules:

- Attribute every reply: name Copilot as the source and quote or link the
  finding it answers.
- Never reply "done" or "fixed" without saying what changed; never decline
  without evidence.
- Do not re-request a Copilot review as part of this command; that is a
  separate decision.
- If the pull request has no Copilot review, or Copilot is not enabled for the
  repository, say so and stop rather than substituting a self-review.

Finish with a short report: one line per finding (inline, prior-round, and
suppressed) with its outcome (fixed in <commit>, declined because <reason>),
plus anything left outstanding.
