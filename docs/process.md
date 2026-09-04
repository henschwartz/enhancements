# Proposal Process

Enhancements for new capabilities and refinements
across [Praxis] repositories.

The process is deliberately light: open an issue,
let maintainers triage it, and work on it once it's
accepted. Most changes need nothing more.

## How New Features Happen

```
Issue -> Triage -> Accepted -> Work it
```

**Open an issue.** This is the entry point.
Describe what you want and why. Every new issue
starts labeled `triage/needs-triage`.

**Maintainers triage it.** They mark the issue
either `triage/accepted` or `triage/declined`. If
a change is large or risky enough to warrant a
written proposal first, maintainers will say so
clearly by adding `triage/needs-proposal`.

**Build it.** Once an issue is `triage/accepted`,
it's fair game to work on according to its project
status and milestone.

> **Nothing here is guaranteed.** Acceptance does
> not guarantee a feature ships, and a feature can
> be changed, reworked, or removed at any stage.

## Lifecycle

### 1. Issue

Open an issue describing the change. Focus on
*what* and *why*, not implementation details. The
issue is labeled `triage/needs-triage`
automatically.

> **Note**: It's fair to directly ping maintainers
> asking for triage consideration when things get
> stuck.

### 2. Triage

A maintainer reviews the issue and applies one of:

| Label | Meaning |
|-------|---------|
| `triage/accepted` | Approved. Fair game to work on. |
| `triage/declined` | Not proceeding. A reason is given. |
| `triage/needs-proposal` | Accepted in principle, but a written proposal is required first (see below). |

Maintainers assign owners and set the project
status and milestone that govern when the work
happens.

### 3. Work it

Once accepted, implement the change in the target
repository. Work proceeds according to the issue's
project status and milestone. Small changes (bug
fixes, minor enhancements, documentation) never
needed process to begin with, they just get done.

## When a Proposal Is Required

Most issues do not need a proposal. Maintainers
will ask for one, by adding `triage/needs-proposal`,
when a change spans multiple PRs, introduces a new
architectural pattern, affects a project's public
interface, or is otherwise complex enough to
warrant a written design.

When a proposal is requested:

1. Create a proposal file in `proposals/` and
   submit it as a PR. File naming convention:

   ```console
   <5-digit-issue-number>_<kebab-case-slug>.md
   ```

2. The first PR should contain the **What?** and
   **Why?** sections. Add the **How?** section in a
   follow-up once the direction is agreed. See the
   [template] for the full structure.

3. Iterate until a maintainer marks the proposal's
   status as `accepted`.

> **v0.x.x simplification**: During pre-1.0
> development, the **How?** section does not
> require an upfront design document. Once the
> **What?** and **Why?** are agreed on, the
> **How?** can simply list the PRs that implement
> the solution. A full requirements and design
> writeup is welcome but not required until 1.0.

A proposal PR should link the issue it came from
and list its `authors`, `stakeholders`, and
affected `repos` in frontmatter. See the [template].

[template]: ../proposals/template.md

## Experimental Phase (at maintainer discretion)

For some changes, maintainers may ask that the
feature be prototyped in the [experimental repo]
first, or shipped behind an experimental flag,
before it lands as standard. This is decided
case by case, not required by default.

When it applies, code lands behind the
`experimental` build tag and carries the
`experimental` label on its PRs, getting lighter
scrutiny while the design settles. After a soak
period, a maintainer may promote it to standard by
removing the build tag, which requires a full
review of the whole feature.

See [experimental-phase.md] for how prototyping
works and [pr-review.md] for how standard and
experimental PRs are reviewed in the target repos.

[experimental repo]: https://github.com/praxis-proxy/experimental
[experimental-phase.md]: experimental-phase.md
[pr-review.md]: pr-review.md

## Affected Repositories

Proposals must declare which repositories they
affect using the `repos` frontmatter field. Valid
values:

| Value | Repository |
|-------|-----------|
| `praxis` | Core proxy server and framework |
| `ai` | AI inference proxy and filters |
| `operator` | Kubernetes Gateway API operator |
| `extproc` | Envoy ExtProc gRPC server |
| `conventions` | Shared lint/config template |
| `experimental` | Experimental filters and features |
| `grid` | AI Grid control plane |
| `forge` | Declarative dev environments |
| `policy` | OPA/Rego policy engine |
| `demos` | Demo configurations |
| `pingora` | Vendored Pingora fork |

## Stakeholders

Every proposal must list its stakeholders in the
frontmatter. Stakeholders are people with a vested
interest in the outcome of a proposal: maintainers,
domain experts, downstream consumers, or anyone
whose work is directly affected by the change.
Stakeholders are expected to review and provide
feedback throughout the proposal lifecycle.

Authors are the people writing and driving the
proposal. Stakeholders are the people who need to
be kept informed and whose input is essential for
the proposal to succeed. An author may also be a
stakeholder.

## Graduation Criteria

Every proposal must list graduation criteria in
the frontmatter. These are the conditions that must
be satisfied before a maintainer will advance the
proposal's status (e.g. `proposed` to `accepted`,
`experimental` to `released`).

Graduation criteria serve as a TODO list for the
proposal. They capture important open items that
must be resolved before the proposal can graduate,
without necessarily blocking the current PR. If a
concern is real but can be addressed in a follow-up
iteration, add it as a graduation criterion and
merge the PR. The criterion holds up the status
change, not the pull request.

Good graduation criteria are specific and
verifiable:

- "How? section with requirements and design"
- "Benchmark results for candidate implementations"
- "Storage trait API reviewed by stakeholders"
- "Prototype implemented in experimental repo"

Avoid vague criteria like "general agreement" or
"feels ready."

Released proposals should have an empty
`graduation_criteria` list, since all criteria were
met when the status advanced to `released`.

## Status Values

See [statuses.md] for the full list of status
values, their definitions, and allowed transitions.

[statuses.md]: statuses.md

[Praxis]: https://github.com/praxis-proxy
