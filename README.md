# Brane Project RFCs

This repository stores approved/ RFCs.

An RFC is a document that describes a requirement and the proposed changes that will solve it.

## Active RFCs

(No rfcs yet)

## The process

- Fork this repo
- Copy `0000-template.md` to `text/0000-my-feature.md` (where 'my-feature' is descriptive. Don't assign an RFC number yet)
- Fill in the RFC. Put care into the details: RFCs that do not present convincing motivation, demonstrate understanding of the impact of the design, or are disingenuous about the drawbacks or alternatives tend to be poorly-received.
- Submit a pull request. As a pull request the RFC will receive design feedback from the larger community, and the author should be prepared to revise it in response.
- Build consensus and integrate feedback. RFCs that have broad support are much more likely to make progress than those that don't receive any comments.
- Eventually, the team will decide whether the RFC is a candidate for inclusion in Brane Project. RFCs that are candidates for inclusion in Brane Project will enter a "final comment period" lasting 3 calendar days. The beginning of this period will be signaled with a comment and tag on the RFCs pull request.
- An RFC can be modified based upon feedback from the team and community. Significant modifications may trigger a new final comment period. An RFC may be rejected by the team after public discussion has settled and comments have been made summarizing the rationale for rejection. A member of the team should then close the RFCs associated pull request.
- An RFC may be accepted at the close of its final comment period. A team member will merge the RFCs associated pull request, at which point the RFC will become 'active'.

## The RFC lifecycle

Once an RFC becomes active, then authors may implement it and submit the feature as a pull request to the Brane's repo. Becoming 'active' is not a rubber stamp, and in particular still does not mean the feature will ultimately be merged; it does mean that the core team has agreed to it in principle and are amenable to merging it.

Furthermore, the fact that a given RFC has been accepted and is 'active' implies nothing about what priority is assigned to its implementation, nor whether anybody is currently working on it.

Modifications to active RFCs can be done in followup PRs. We strive to write each RFC in a manner that it will reflect the final design of the feature; but the nature of the process means that we cannot expect every merged RFC to actually reflect what the end result will be at the time of the next major release; therefore we try to keep each RFC document somewhat in sync with the language feature as planned, tracking such changes via followup pull requests to the document.

## Implementing an RFC

The author of an RFC is not obligated to implement it. Of course, the RFC author (like any other developer) is welcome to post an implementation for review after the RFC has been accepted.

If you are interested in working on the implementation for an 'active' RFC, but cannot determine if someone else is already working on it, feel free to ask (e.g. by leaving a comment on the associated issue).

## Reviewing RFCs

TBD

---

**Brane Project RFC process owes its inspiration to the [React RFC process](https://github.com/reactjs/rfcs), [Svelte RFC process](https://github.com/sveltejs/rfcs), and the [Yarn RFC process](https://github.com/yarnpkg/rfcs)**
