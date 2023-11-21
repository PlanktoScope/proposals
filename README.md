# proposals

PlanktoScope project design documents

## Introduction

The PlanktoScope project is migrating to a design-driven development process.
Significant changes to the hardware, software, and development processes of the
project should first be discussed, and sometimes formally documented, before
they can be adopted as part of the PlanktoScope and/or its development process.

This document describes the process for proposing, documenting, and implementing
changes to the PlanktoScope project.
Note that changes to the PlanktoScope community are covered by a separate
process which does not rely on participants to understand how to use GitHub.

This process is an adaptation of the Go programming language's
[proposal process](https://github.com/golang/proposal) and was also inspired by
the Oxide Computer Company's
[Requests for Discussion process](https://rfd.shared.oxide.computer/rfd/0001).

## The Proposal Process

The proposal process is a process for reviewing a proposal and reaching a
decision about whether to accept or decline the proposal.

### Definitions

- A **proposal** is a suggestion filed as a GitHub issue, identified by having
  the `proposal` label.

- A **design document** is the expanded form of a proposal, written when the
  proposal needs more careful explanation and consideration.

### Steps

1. The proposal author
   [creates a brief issue](https://github.com/PlanktoScope/PlanktoScope/issues/new/choose)
   describing the proposal.
   Note: A design document is optional at this point.
   Note: A non-proposal issue can be turned into a proposal by simply adding the
   `proposal` label.

2. A discussion on the issue tracker aims to triage the proposal into one of
   three outcomes:
   
    - Accept proposal, or
   
    - Decline proposal, or
   
    - Ask for a design document.
   
   If the proposal is accepted or declined, the process is done.
   Otherwise the discussion is expected to identify concerns that should be
   addressed in a
   more detailed design document.

3. The proposal author writes a [design document](#design-documents) to work out
   details of the proposed design and address the concerns raised in the initial
   discussion.

4. Once comments and revisions on the design document come to an end, there is a
   final discussion on the issue, to reach one of two outcomes:
   
    - Accept proposal, or
   
    - Decline proposal.
   
   After the proposal is accepted or declined (whether after step 2 or step 4),
   implementation work proceeds in the same way as any other contribution.

## Detail

### Goals

- Ensure that broad input is collected and consensus is facilitated on important
  decisions, to increase the quality and effectiveness of decisions.

- Make sure that proposals get a proper, fair, and timely evaluation with a
  clear answer.

- Record and announce decisions clearly, to reduce surprise for people who will
  be impacted by decisions.

- Make past proposals easy to find, to avoid duplicated effort.

- Make sure contributors know how to write good design documents.

### Scope

The proposal process should be used for any notable change or addition to the
PlanktoScope's software, hardware, or development processes.
"Notable" includes externally visible changes in all projects under the
[github.com/PlanktoScope](https://github.com/PlanktoScope) organization.
It also includes changes with consequences for backwards-compatibility of the
PlanktoScope hardware and software.
Proposals are appropriate in the following examples:

- Add or change a project process.

- Make an architectural or design decision for hardware or software.

- Change an API, tool, or process used by PlanktoScope end-users.

- Change an API, tool, or process used only by project contributors.

Since proposals begin (and will often end) with the filing of an issue, even
small changes can go through the proposal process if appropriate.
Deciding what is appropriate is a matter of judgment we will refine through
experience.
If in doubt, file a proposal.

### Compatibility

All official versions of the PlanktoScope hardware must continue to be usable
with all future versions of the PlanktoScope software.
Every proposed change must maintain this promise.
In cases where a proposed change interacts with a feature of the PlanktoScope
hardware which is only present in some hardware versions, the proposal must
describe a mechanism for handling such a situation (for example, only enabling a
feature on certain hardware versions; or providing fallback functionality); a
uesign document may be needed for such a proposal.

The PlanktoScope software is not yet mature enough to guarantee compatibility
across versions of the software.
In general, proposed changes should maintain backwards-compatibility across
public interfaces, including data and metadata schemas.
When backwards-compatibility must be broken, a proposal must be made with a
design document explaining and addressing the consequences of breaking
compatibility.

### Design Documents

As noted above, some (but not all) proposals need to be elaborated in a design
document.

- The design document should be added to the
  [PlanktoScope proposals repository](https://github.com/PlanktoScope/proposals)
  in a new pull request as `design/NNNN-shortname.md`, where `NNNN` is the
  GitHub issue number (padded with leading zeros if it has less than 4 digits)
  and `shortname` is a short name (a few dash-separated words at most).
  If the pull request is made directly in the repository (rather than being made
  in a GitHub fork) the name of the Git branch for the pull request should be
  `NNNN-shortname`. 
  The name of the pull request should be `design doc: NNNN-shortname`.

- The design document should follow [the template](design/TEMPLATE.md).

- The design document should address any specific concerns raised during the
  initial discussion.

- It is expected that the design document will go through multiple revisions.

- For ease of review, design documents should be wrapped around the 80-column
  mark. [Each sentence should start on a new line](https://rhodesmill.org/brandon/2012/one-sentence-per-line/)
  so that comments can be made accurately and the diff kept shorter.

- Comments on GitHub pull requests should be restricted to grammar, spelling,
  formatting, or procedural errors related to the preparation of the proposal
  itself.
  All other comments should be addressed to the related GitHub issue.

When writing design documents, it is important to consider various aspects of
decision-making, which might include:

- Documenting the viable options (or a subset of options if we can easily rule
  some out) and the benefits and drawbacks of each option.

- Documenting our reasoning, including data and references wherever possible,
  to make it easy for our future selves (and future contributors) to understand
  the decisions we've made and why.

- Describing implications for various stakeholders of the PlanktoScope project,
  such as FairScope, PlanktoScope end-users, and/or PlanktoScope project
  contributors.

- Describing implications for various technical aspects of the PlanktoScope as a
  platform, such as (but not limited to) approachability, transparency,
  compatibility, extensibility, security, and stability.

Not every design document should have the same level of detail.
Some have more sense of urgency and therefore might not be as rigorous as
others.
Use your best judgement about the level of depth to include.

### Quick Start for Experienced Committers

Experienced committers who are certain that a design document will be required
for a particular proposal can skip steps 1 and 2 and include the design document
with the initial issue on GitHub Issues.

### Proposal Review

Pending proposals are reviewed by the proposal review group at the weekly
PlanktoScope software team meetings.
The principle goal of proposal review at these meetings is to make sure that
proposals are receiving attention from the right people, by tagging relevant
developers, raising important questions, pinging lapsed discussions, and
generally trying to guide discussion toward agreement about the outcome.
The discussion itself is expected to happen on GitHub Issues, so that anyone can
participate in discussion.

These meetings also identify proposals where consensus has been reached and the
process can be advanced to the next step (by marking the proposal accepted or
declined or by asking for a design document).

Decisions made in meetings to move proposals through the stages of the process
are posted to
[github.com/PlanktoScope/PlanktoScope/issues/282](https://github.com/PlanktoScope/PlanktoScope/issues/282)
after the weekly meeting, so that anyone interested in which proposals are under
active consideration can follow that issue.

Proposal issues are tracked in the
[Proposals project board](https://github.com/orgs/PlanktoScope/projects/4) on
GitHub.
The current state of the proposal is captured by columns in that project board,
as described below.

The proposal review group can, at their discretion, make exceptions for
proposals that do not need to go through all stages, fast-tracking them to Final
Comments or even Accept/Decline, such as for proposals that do not merit the
full review or that need to be considered quickly due to pending releases.

#### Draft

Proposal authors can add issues to the Draft column for proposals which are not
yet ready for review.

#### Submitted

Once their proposal is ready for review, the author should add their issue to
the Submitted column or (if they are unable to do so) add a comment to notify
the proposal review group, who will add their issue to the Submitted column.

The weekly proposal review meetings aim to look at all the issues in the Under
Review and Final Comments columns.
Afterwards, if there is time, then proposals from the Submitted column are
selected to be moved to Under Review.
Holding proposals in Submitted until attention can be devoted to them (at which
point they move to Under Review) ensures that progress is made moving active
proposals out to Accepted or Declined, so that new work does not prevent old
work from being finished.

#### Under Review

Issues in the Under Review column are reviewed at each weekly proposal meeting
to watch for emerging consensus in the discussions.
The proposal review group may also comment, make suggestions, ask clarifying
questions, and try to restate the proposals to make sure everyone agrees about
what exactly is being discussed.

#### Final Comments

If an issue discussion seems to have reached a consensus to accept or decline
the proposal, the proposal review group will move the issue to the Final
Comments column and post a comment noting that change and indicating whether the
proposal is likely to be accepted or likely to be declined.

If the proposal review group determines that no consensus is likely to be
reached and that the default of declining the proposal is appropriate, the
proposal review group will move the issue to the Final Comments column and post
a comment noting that change and indicating that consensus is unlikely.

Once an issue is moved to the Final Comments column, this marks the final period
for comments that might change the recognition of consensus.

#### Accepted

One week after a proposal moves to Final Comments because consensus was reached
to accept the proposal, if there was no change in consensus then the proposal
review group will move the proposal to the Accepted column.
If significant discussion happens during that week, the proposal review group
may leave the proposal in Final Comments for another week or even move the
proposal back to Under Review.

Once a proposal is marked Accepted, the `proposal-accepted` label is applied, it
is moved out of the Proposal milestone into a work milestone, and the issue is
repurposed to track the work of implementing the proposal.

The default work milestone is Backlog, indicating that the work applies to a
PlanktoScope hardware/software release itself but has not been assigned to a
particular release.
Alternatively, the proposal may be assigned to the milestone for a specific
release.

#### Declined

One week after a proposal moves to Final Comments because consensus was reached
to decline the proposal, if there was no change in consensus then the proposal
review group will move the proposal to the Declined column.
If significant discussion happens during that week, the proposal review group
may leave the proposal in Final Comments for another week or even move the
proposal back to Under Review.

Once a proposal is marked Declined, it is closed.

The proposal review team may decline a proposal without progressing through the
Under Review or Final Comments stages, including for the following reasons:

- If a proposal duplicates a previously decided proposal, and there is no
  significant new information which might change the previous decision.
- If a proposal directly contradicts the core design of some aspect of the
  project (when that aspect was intentionally designed), or if a proposal is
  impossible to implement efficiently or at all.
  However, if it seems like there is general interest from others, or that
  discussion may lead to a feasible proposal, the proposal may be reopened and
  the discussion continued.
- If the original author closes the issue or retracts it in a comment.
  If it seems like there is still general interest from others, the proposal may
  be reopened and the discussion continued.
- If a proposal is obsoleted by changes to the project that have been made since
  the proposal was filed.
  If it seems like there is still general interest from others, or that
  discussion may lead to a different, non-obsolete proposal, the proposal may be
  reopened and the discussion continued.

### Consensus and Disagreement

The goal of the proposal process is to reach general consensus about the outcome
in a timely manner.

If proposal review cannot identify a general consensus in the discussion of the
issue on the issue tracker, the usual result is that the proposal is declined.
It can happen that proposal review may not identify a general consensus and yet
it is clear that the proposal should not be outright declined.
For example, there may be a consensus that some solution to a problem is
important, but no consensus on which of two competing solutions should be
adopted.

If the proposal review group cannot identify a consensus nor a next step for the
proposal, the decision about the path forward passes to the PlanktoScope project
maintainers (currently [@ethanjli](https://github.com/ethanjli) and
[@tpollina](https://github.com/tpollina)), who review the discussion and aim to
reach a consensus among themselves. If so, they document the decision and its
rationale on the issue.

If consensus among the maintainers cannot be reached, which is even more
unusual, the arbiter (currently [@tpollina](https://github.com/tpollina))
reviews the discussion and decides the next step, documenting the decision and
its rationale on the issue.

## Help

If you need help with this process, please contact the PlanktoScope project
contributors by posting to the appropriate development channel on the
PlanktoScope Slack.
