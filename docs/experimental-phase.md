# Experimental Phase Guide

The [experimental repo] is where features can be
prototyped before they land as standard. It is not
required by default: maintainers decide, case by
case, when a change should go through it first. When
they do, this guide describes how it works.

[experimental repo]: https://github.com/praxis-proxy/experimental

## Why Prototype First?

Prototyping in a separate repo provides:

- **Low-risk iteration.** Experimental code does
  not affect the stability of production repos.
- **Fast feedback.** Authors can iterate on design
  without the overhead of full CI and review in
  the target repo.
- **Proof of feasibility.** A working prototype
  demonstrates the proposal is implementable, not
  just theoretically sound.
- **Informed design.** The How? section is stronger
  when informed by a working prototype.

## Prototype Path

When maintainers ask for a prototype (typically for
new filters, features, or capabilities):

1. Implement a working prototype in the
   experimental repo.
2. The prototype must demonstrate the core
   functionality described in the What? section.
   It does not need to be production-ready.
3. If a proposal exists, update its
   `experimental_impl` frontmatter field with a link
   to the implementation (PR or branch URL).
4. Once the prototype exists, code moves to the
   target repository behind an experimental flag. In
   the target repo, that work lands through
   `experimental`-labeled PRs behind the
   `experimental` build tag. See [pr-review.md] for
   how those PRs are reviewed and how the build tag
   is later removed.

[pr-review.md]: pr-review.md

### What Counts as a Sufficient Prototype?

- Core functionality works end-to-end
- Basic happy path is exercised
- Key design decisions are validated
- It does NOT need: production error handling,
  full test coverage, documentation, or
  performance optimization

## Exempt Path

Some changes structurally cannot use the
experimental repo. When a proposal exists for one of
these and a prototype is expected, set
`experimental_exempt: true` and provide a reason in
`experimental_exempt_reason`, then use one of the
alternatives below instead.

### Valid Exemption Categories

| Category | Examples |
|----------|---------|
| Operator CRD / control plane changes | New CRD fields, controller logic, Gateway API support |
| Core pipeline architecture changes | Pipeline execution model, filter trait changes |
| Build system / tooling changes | Build-time filter registry, workspace configuration |
| Cross-repo infrastructure | Shared crate changes, workspace dependency updates |
| Configuration schema changes | New config file format, YAML schema additions |
| Convention / process changes | Linting rules, CI workflow changes |

### Alternative for Exempt Proposals

Instead of prototyping in the experimental repo,
exempt proposals must use one of:

- **Feature flag in the target repo.** Gate the new
  functionality behind a compile-time or runtime
  feature flag for a soak period.
- **Experimental CRD version.** For operator
  changes, use a `v1alpha1` API version or
  equivalent.
- **Experimental build profile.** Use a Cargo
  feature or build profile that is not enabled by
  default.

The `experimental_impl` field should point to the
feature-flagged implementation in the target repo
instead of the experimental repo.

## Frontmatter Fields

```yaml
# Default path - link to experimental repo impl
experimental_impl: https://github.com/praxis-proxy/experimental/pull/42

# Exempt path - explain why
experimental_exempt: true
experimental_exempt_reason: "Core pipeline architecture change"
```
