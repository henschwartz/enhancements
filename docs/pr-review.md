# Pull Request Review

How pull requests are reviewed in the Praxis
implementation repositories (`praxis`, `ai`, and
the others listed in [process.md]). This is
separate from the proposal process in this repo:
it governs the code that lands once a proposal is
being built.

Every PR takes one of two paths, decided by whether
it carries the `experimental` label.

## Standard PRs

The default. A PR with no `experimental` label is
held to the project's full review bar and gets
significant scrutiny before it is allowed into a
repo like `praxis` or `ai`. Expect review of
correctness, API surface, tests, documentation,
performance, and backwards compatibility. Code
merged this way is built by default and is
considered supported.

## Experimental PRs

A PR flagged with the `experimental` label is
treated as **incomplete**. Marking a PR
`experimental` is a statement that this is one of
several PRs that will iteratively shape the feature
forward, so each PR is reviewed with less scrutiny
than a standard PR: reviewers focus on direction
and containment rather than completeness.

Two rules apply to every experimental PR:

- **All new code must sit behind the `experimental`
  build tag.** The feature must not be built by
  default. This is what makes the reduced scrutiny
  safe: incomplete code cannot reach users who
  build normally.
- **The feature stays experimental until it
  graduates.** Anything gated this way may change,
  break, or be removed while it is behind the tag.

This is the code-level counterpart to the
`experimental` status in the [proposal lifecycle].

## Removing the `experimental` Build Tag

Taking a feature out from behind the `experimental`
build tag (so it builds by default and becomes
supported) requires a **full review of the entire
feature**, not the incremental review the
individual experimental PRs received.

- The full review is generally done through a
  single contrived PR that removes the build tag
  and presents the complete feature for review.
- Reviewers have latitude in how they run this
  "review of the whole feature." We are fairly open
  to reviewer preference on the mechanics.
- **Default to a PR when feasible.** Choose another
  form only when a PR genuinely does not fit the
  review.

Graduating a feature out from behind the build tag
corresponds to the `experimental` to `released`
status transition. See [statuses.md] for the status
values and [process.md] for the full lifecycle.

[process.md]: process.md
[statuses.md]: statuses.md
[proposal lifecycle]: process.md#lifecycle
