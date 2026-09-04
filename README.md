# Praxis Enhancement Proposals

Centralized enhancement proposals for all [Praxis]
repositories.

[Praxis]: https://github.com/praxis-proxy

## How New Features Happen

```
Issue -> Triage -> Accepted -> Work it
```

1. **Open an issue.** Describe what you want and
   why. It starts life labeled
   `triage/needs-triage`.

2. **Maintainers triage it.** They mark it either
   `triage/accepted` or `triage/declined`. If a
   change is large enough to need a written
   proposal first, maintainers will say so
   clearly (`triage/needs-proposal`).

3. **Build it.** Once accepted, it's fair game to
   work on according to its project status and
   milestone.

Most changes need nothing more than this. See
[docs/process.md](docs/process.md) for triage
outcomes, when a proposal is required, and the
optional experimental phase.

> **Nothing here is guaranteed.** Acceptance does
> not guarantee a feature ships, and a feature can
> be changed, reworked, or removed at any stage.

## Structure

```
docs/
  process.md              # Full proposal lifecycle
  statuses.md             # Status definitions
  experimental-phase.md   # Experimental repo guide
  pr-review.md            # PR review in target repos
proposals/
  template.md             # Proposal template
  NNNNN_slug.md           # Individual proposals
```

## Links

- [Proposal Process](docs/process.md)
- [Status Definitions](docs/statuses.md)
- [Experimental Phase Guide](docs/experimental-phase.md)
- [Pull Request Review](docs/pr-review.md)
- [Proposal Template](proposals/template.md)
- [Contributing](CONTRIBUTING.md)

## License

Apache 2.0
