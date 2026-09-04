# Contributing Enhancements

Thank you for your interest in improving Praxis.

## How New Features Happen

```
Issue -> Triage -> Accepted -> Work it
```

**Open an issue.** This is the entry point. Describe
what you want and why. Your issue starts labeled
`triage/needs-triage`.

**Maintainers triage it.** They mark it either
`triage/accepted` or `triage/declined`. If the
change is big enough to need a written proposal
first, they'll say so clearly with
`triage/needs-proposal`.

**Build it.** Once accepted, it's fair game to work
on according to its project status and milestone.

> **Nothing here is guaranteed.** Acceptance does
> not guarantee a feature ships, and a feature can
> be changed, reworked, or removed at any stage.

Small changes (bug fixes, minor enhancements,
documentation updates) don't need any of this. Just
open a PR.

## When a Proposal Is Required

Most issues don't need a proposal. Maintainers ask
for one (by adding `triage/needs-proposal`) when a
change spans multiple PRs, introduces a new
architectural pattern, affects a project's public
interface, or is complex enough to warrant a
written design.

When asked, create a file in `proposals/` using the
[template]. The filename must follow the convention:

```
<5-digit-issue-number>_<kebab-case-slug>.md
```

Start with **What?** and **Why?**; add **How?** in a
follow-up PR once the direction is accepted. Iterate
until a maintainer marks the proposal `accepted`.

At their discretion, maintainers may also ask that a
feature be prototyped in the [experimental repo]
first. See [experimental-phase.md] for details.

## Proposal Requirements

When a proposal is required, it must include:

- An `issue` link to the originating issue
- At least one author
- At least one stakeholder
- A `repos` list of affected repositories
- At least one graduation criterion

See the [template] for the full frontmatter schema.

## Affected Repositories

Valid `repos` values:

`praxis`, `ai`, `operator`, `extproc`,
`conventions`, `experimental`, `grid`, `forge`,
`policy`, `demos`, `pingora`

[template]: proposals/template.md
[experimental repo]: https://github.com/praxis-proxy/experimental
[experimental-phase.md]: docs/experimental-phase.md
