---
issue: https://github.com/praxis-proxy/praxis/issues/125
discussion: https://github.com/praxis-proxy/praxis/issues/125
status: proposed
repos:
  - praxis
authors:
  - henschwartz
graduation_criteria:
  - How? section added after the What? and Why? direction is accepted
  - Open questions closed in Decisions before How?
  - Phased delivery plan accepted with clear v1 JSON stats scope
  - Documented mapping from umbrella goals to landed sibling issues
  - v1 stats JSON includes process uptime and version identity (semver, git_sha when available, display string)
  - v1 stats JSON returns per-cluster upstream request and error counts plus endpoint health summary (healthy/total counts and per-endpoint healthy boolean)
  - v1 stats JSON returns per-listener active connection counts for HTTP and TCP listeners and documents listener-type gaps explicitly
  - v1 endpoint reflects runtime state after dynamic config reload where applicable
  - Integration test validates stats JSON shape against live traffic
  - Remaining phases (config dump, certs, HTML dashboard, profiling) tracked as follow-on issues or How? tranches
stakeholders:
  - shaneutt
  - twghu
  - alexsnaps
epic: Observability Epic
related:
  - 00792
  - 00794
  - 00796
  - 00797
  - 00798
  - 00799
---

# Admin Dashboard and Stats API

## What?

Praxis operators need a **single operational picture** of a running
proxy: what listeners are up, which clusters and endpoints are
healthy, how much traffic is flowing, and whether configuration in
memory matches expectations. Today that picture is scattered across
log files, Prometheus scrapes, and ad-hoc inspection of YAML on disk.
Issue
[#125](https://github.com/praxis-proxy/praxis/issues/125) is the
**umbrella** for admin visibility: JSON admin APIs, optional HTML
dashboard, and profiling hooks.

Several sibling capabilities from this umbrella have already shipped
or have dedicated proposals. This document **coordinates the remainder**
and records how the pieces fit together. It does **not** re-open work
that is complete or already accepted elsewhere.

### Already delivered on main

| Capability | Tracking | Admin surface (if any) |
| --- | --- | --- |
| Expanded Prometheus metrics | [#794](https://github.com/praxis-proxy/praxis/issues/794) / [00794](proposals/00794_expand-prometheus-metrics-surface.md) | `GET /metrics` |
| Resolved pipeline inspection | [#796](https://github.com/praxis-proxy/praxis/issues/796) / [00796](proposals/00796_pipeline-info-admin-endpoint.md) | `GET /api/pipelines` |
| Runtime log level control | [#798](https://github.com/praxis-proxy/praxis/issues/798) / [00798](proposals/00798_dynamic-log-level-admin-api.md) | `PUT`/`GET`/`DELETE` `/api/log-level` |
| Non-blocking log delivery | [#797](https://github.com/praxis-proxy/praxis/issues/797) / [00797](proposals/00797_non-blocking-log-writer.md) | N/A (process logging, not admin API) |
| Configurable access log fields | [#799](https://github.com/praxis-proxy/praxis/issues/799) / [00799](proposals/00799_configurable-access-log-fields.md) | N/A (data-plane filter) |

### Proposed siblings (not yet on main)

| Capability | Tracking | Planned admin surface |
| --- | --- | --- |
| Live request tap | [#792](https://github.com/praxis-proxy/praxis/issues/792) / [00792](proposals/00792_live-request-tap-api.md) | `GET /api/tap` (SSE) |

### Umbrella scope still open

From
[#125](https://github.com/praxis-proxy/praxis/issues/125), the
following remain **under this proposal's coordination** (delivery may
be split across multiple implementation PRs after How? is accepted):

1. **Runtime stats JSON** — structured counters and gauges not
   conveniently available from a single Prometheus scrape (per-cluster,
   per-listener active connections, optional per-listener request rollups;
   per-filter counters deferred to Phase 2)
2. **Config dump** — read-only view of the **effective** configuration
   the process is running (not necessarily a verbatim file replay)
3. **Cluster and endpoint status** — health and selection state beyond
   what `/healthy` and `/ready` expose
4. **Listener status** — addresses, protocols, TLS, attached pipeline
   names (partially overlaps pipeline inspection; stats focus is
   operational counters)
5. **Certificate inspection** — present certs, expiry, and SAN summary
   on the admin listener
6. **Optional HTML dashboard** — browser UI consuming the JSON APIs
7. **Profiling endpoints** — heap and CPU profiles for deep debugging

### Phased delivery (proposed)

**Phase 1 (v1 implementation target).** `GET /api/stats` returning
JSON with:

- Process uptime (`uptime_secs` since process start) and **version identity**
  (see Decisions)
- Per-listener **active connection** counts for **HTTP and TCP**
  listeners (from `praxis_connections_active`; see Decisions for
  coverage and gaps)
- Per-cluster upstream request/error counts and **endpoint health summary**
  (see Decisions)
- **Per-listener HTTP request totals** only when a listener label is
  available on the underlying counter; today `praxis_http_requests_total`
  has no `listener` label, so Phase 1 either adds that instrumentation as
  part of implementation or omits per-listener request rollups and
  documents the gap (see Decisions)

Phase 1 does **not** include per-filter execution counters. Optional
`praxis_filter_duration_seconds` histograms (when
`metrics.filter_duration` is enabled) remain on `/metrics`; filter
invocation counts move to a follow-on phase once instrumentation exists.

Phase 1 is **read-only**, on the **existing admin listener**, with the
same bind policy as other `/api/*` endpoints. It complements rather
than replaces `GET /metrics`.

**Phase 2.** Config dump, cluster/endpoint detail APIs, and per-filter
execution visibility (invocation counts or derived summaries once
instrumentation exists).

**Phase 3.** Certificate inspection.

**Phase 4.** HTML dashboard (static assets served from admin or
documented external consumption pattern).

**Phase 5.** Profiling endpoints (likely behind explicit opt-in because
of sensitivity and cost).

Exact path names, field names, and nesting for Phase 1 remain open
until Decisions are confirmed.

### Goals

- Give operators a **documented, phased plan** for admin visibility
  under [#125](https://github.com/praxis-proxy/praxis/issues/125)
- Deliver **Phase 1 stats JSON** as the next concrete implementation
  slice after sibling observability admin APIs land
- Keep all surfaces on the **existing admin listener** and exposure
  model unless a phase explicitly requires otherwise
- Ensure JSON responses are **machine-readable** and suitable for a
  future `praxisctl` consumer ([#793](https://github.com/praxis-proxy/praxis/issues/793))
- Reflect **runtime state** after dynamic config reload where the
  underlying data is reloadable
- Cover Phase 1 with integration tests
- Sit under
  [Epic #160 Observability](https://github.com/praxis-proxy/praxis/issues/160)

### Non-Goals

- Re-implementing metrics, pipelines, log-level, or tap APIs already
  tracked above
- Mutating configuration through the stats or dashboard APIs
  ([#785](https://github.com/praxis-proxy/praxis/issues/785) explores
  durable config push separately)
- Replacing Prometheus; `/metrics` remains the time-series integration
  point for external monitoring
- Building a full-featured Grafana replacement in Phase 1
- Persisting admin-derived views across process restart
- New authentication beyond the existing admin bind policy in early
  phases

### Open Questions

1. **Stats vs metrics boundary.** Which counters live only on
   `/api/stats`, which are Prometheus-only, and which are duplicated
   with identical names?
2. **Config dump shape.** Redacted effective config as YAML JSON, or
   structured sections (listeners, clusters, filters) without raw
   secrets?
3. **Active connections.** Source of truth for connection counts per
   listener (Pingora stats vs custom atomics)?
4. **Per-listener HTTP request totals.** Does Phase 1 add a `listener`
   label to request counters or omit per-listener request rollups until
   instrumentation exists?
5. **Dashboard hosting.** Serve static HTML from the admin process vs
   document external hosting of a separate UI repo?
6. **Profiling safety.** Require explicit config flag, separate path
   prefix, or mutual exclusion with `allow_public_admin`?

## Why?

### Motivation

Prometheus metrics excel at time-series monitoring but are awkward for
**one-shot operational questions**: "How many requests hit listener X
since boot?", "Which upstream endpoints are unhealthy right now?", "What
config is actually loaded?" Operators currently stitch answers from
metrics, logs, YAML on disk, and multiple admin endpoints. The umbrella
[#125](https://github.com/praxis-proxy/praxis/issues/125) exists so
Praxis can offer a **coherent admin story**—JSON first, optional UI
later—without every feature inventing a new port or auth model.

Phasing matters because much of the observability epic
([#160](https://github.com/praxis-proxy/praxis/issues/160)) is already
landing as focused proposals ([#794](https://github.com/praxis-proxy/praxis/issues/794),
[#796](https://github.com/praxis-proxy/praxis/issues/796),
[#798](https://github.com/praxis-proxy/praxis/issues/798),
[#792](https://github.com/praxis-proxy/praxis/issues/792)). This
proposal **records the map**, avoids duplicate design, and points the
next implementation effort at **structured runtime stats**—the largest
remaining gap for `praxisctl` and a future dashboard.

Runtime admin APIs are **diagnostic overlays**: they describe what the
process knows now. They are not a substitute for durable configuration
managed through Git, the operator, or a future control plane
([#785](https://github.com/praxis-proxy/praxis/issues/785)). That
separation keeps incident-time inspection safe and predictable.

### User Stories

These are stakeholder needs derived from
[#125](https://github.com/praxis-proxy/praxis/issues/125);
they are not separate tracked issues.

- As an SRE, I want a single JSON stats endpoint so that I can
  quickly assess listener and cluster health during an incident without
  PromQL.
- As a platform operator, I want phased delivery documented so that I
  know which admin capabilities exist today and what is coming next.
- As a config author, I want a future read-only config dump so that I
  can verify the running process matches what I intended after hot
  reload.
- As an operator, I want optional HTML visualization later so that
  on-call engineers without Prometheus access can still see proxy health.
- As a maintainer, I want clear boundaries between stats, metrics, and
  tap so that we do not duplicate high-cardinality data in multiple
  formats.

## Decisions

Proposed design choices for each Open Question above. Confirm during
proposal review before implementation begins.

- **Stats vs metrics boundary.** `/api/stats` exposes **operational
  snapshots** suited to CLI and human inspection (counters since process
  start, current health summaries, connection counts). Prometheus
  `/metrics` remains the **canonical time-series export** for external
  monitoring. Duplicate only counters that are cheap and explicitly
  documented; do not add new high-cardinality labels to stats JSON.
- **Config dump shape.** Phase 2 returns **structured JSON sections**
  (listeners, clusters, TLS references) with **secrets redacted** by
  default; optional `?format=yaml` may come later. No raw file bytes in
  v1 of Phase 2.
- **Active connections.** Prefer **Pingora-reported connection stats**
  where available; document gaps per listener type in How? rather than
  inventing parallel accounting in Phase 1.
- **Listener-type coverage (Phase 1).** Anchor in today's metrics on
  `main`:

  | Listener protocol | Active connections in v1 | Request totals in v1 |
  | --- | --- | --- |
  | **HTTP** (with or without TLS) | Yes — `praxis_connections_active{listener}` tracks in-flight HTTP sessions | **Gap today** — `praxis_http_requests_total` has no `listener` label (only `method`, `status_class`, `route`, `cluster`). Phase 1 either adds listener labeling as part of stats implementation or omits per-listener HTTP request rollups and documents the omission in the JSON schema. |
  | **TCP** (with or without TLS) | Yes — `praxis_connections_active{listener}` tracks open TCP sessions | **N/A** — no HTTP request counter; byte/session throughput stays on `/metrics` or a later phase. |

  TLS is a listener property, not a separate protocol kind; stats entries
  include TLS enabled/disabled from resolved listener config.
- **Per-listener HTTP request totals.** If Phase 1 implementation adds
  a `listener` label to `praxis_http_requests_total` (or a dedicated
  per-listener counter), `/api/stats` exposes rollups per listener name.
  If not added in v1, the JSON schema documents the gap explicitly
  (connections yes, per-listener request totals omitted) rather than
  returning placeholder zeros.
- **Filter execution counters.** **Out of Phase 1.** Today Praxis exposes
  optional `praxis_filter_duration_seconds` histograms when
  `metrics.filter_duration` is enabled — timing only, not per-filter
  invocation counts across all builtins. Phase 2 (or a follow-on issue)
  adds filter execution visibility once counters exist or can be derived
  without high-cardinality surprises.
- **Version identity (Phase 1).** Structured fields anchored in today's
  `PRAXIS_VERSION` build metadata (`server/build.rs`, `praxis --version`):

  | Field | v1 | Notes |
  | --- | --- | --- |
  | `version.semver` | required | Cargo package version (e.g. `0.3.1`) |
  | `version.git_sha` | when available | Short git commit at build time; **omit** when git was unavailable |
  | `version.dirty` | when available | `true` when the build tree had uncommitted changes |
  | `version.display` | required | Same string as `praxis --version` / startup log (`PRAXIS_VERSION`) |

  Build timestamp, Rust toolchain, and container image digest are **Phase 2**
  additions (may appear in release qualification metadata elsewhere).
- **Endpoint health summary (Phase 1).** Per cluster, a snapshot (not a time
  series):

  | Field | v1 | Notes |
  | --- | --- | --- |
  | `healthy_endpoints` / `total_endpoints` | required | Mirrors `praxis_upstream_healthy_endpoints` and `praxis_upstream_total_endpoints` gauges |
  | `endpoints[].address` | required | Upstream socket (host:port) from resolved cluster config |
  | `endpoints[].healthy` | required | Boolean from active health-check state at snapshot time |
  | `endpoints[].last_check_at` | omitted | Phase 2 |
  | Per-endpoint latency, error rate, active connections, LB weight | omitted | Phase 2; stay on `/metrics` or later admin APIs |

  Cluster-level upstream **request** and **error** rollups in v1 use existing
  counters (for example `praxis_upstream_connect_failures_total` and HTTP
  request totals grouped by `cluster` label) — not per-endpoint request
  breakdown in v1.
- **Dashboard hosting.** Phase 4 ships as **optional static assets**
  served from the admin listener behind the same bind policy, plus
  documentation for air-gapped users who prefer JSON-only.
- **Profiling safety.** Profiling endpoints require an **explicit config
  opt-in** (`admin.profiling_enabled` or equivalent), default **off**,
  and are **disallowed** when `allow_public_admin: true` unless a
  separate maintainer-only override is accepted in How?.
