# Hierarchical Agent Memory

Provenance-aware episodic and semantic memory with conflict resolution, hybrid retrieval, and reranking.

**Status: planning — implementation has not started.** This README describes intended scope and acceptance criteria. It does not establish working behavior, completed tests, security guarantees, or measured performance.

Part of an eight-repository agent-systems portfolio.

## Planned scope

- Extract candidate memories from episodes while retaining source, timestamp, and confidence.
- Resolve contradictory or superseded facts without silently erasing provenance.
- Combine lexical and vector retrieval, followed by a separately evaluated reranker.

## Planned setup and observability

The implementation is intended to provide a `docker-compose.yml` and `Makefile` so a reviewer can use `make demo` for a deterministic offline demonstration and `make test` for offline regression checks. **These commands and their supporting files are not implemented yet.** Live-model runs will be optional and separately documented with configuration and cost assumptions.

Every model call and tool execution is intended to emit an OpenTelemetry span. Planned aggregate reporting includes token usage, estimated USD per 1,000 requests, p50/p95 latency, and p50/p95 time to first token (TTFT) where streaming applies. Offline fixtures can verify instrumentation and behavior; live-provider performance requires separate measurements.

The core mechanics will use raw model SDKs with explicit control flow; no LangChain, LlamaIndex, or other orchestration framework will implement them.

See [PRODUCTION_CHECKLIST.md](PRODUCTION_CHECKLIST.md) for shared acceptance criteria.

## Proposed architecture and tradeoffs

An extraction pipeline proposes memories from recorded episodes. A policy layer handles versioning, conflicts, and retention before writing to an explicit store. Retrieval merges lexical and vector candidates, then optionally reranks them before context injection.

More memory can improve recall while increasing privacy exposure and stale-context risk. Reranking can improve relevance while adding latency and cost. Explicit memory policies are easier to inspect but require careful domain-specific evaluation.

## Roadmap and acceptance criteria

Complete the shared reproducibility and observability checklist, then implement and verify these project-specific criteria:

- [ ] Retain source provenance for every stored fact and support correction and deletion in the chosen storage design.
- [ ] Demonstrate deterministic conflict policies on contradictory, temporal, and duplicate fixtures.
- [ ] Compare a simple retrieval baseline against hybrid retrieval and reranking on a labeled set.
- [ ] Report retrieval quality, contradictory-memory rate, and answer correctness alongside shared latency and cost metrics.

## Candidate failure modes

These are design risks to investigate, **not tested or observed findings**:

- An extraction invents a fact, stores sensitive content unnecessarily, or merges distinct entities.
- A stale or conflicting fact outranks the current source of truth.
- Retrieval misses a relevant episode, or reranking suppresses evidence needed for the answer.

The implementation will add reproduction cases, actual outcomes, and mitigations as evidence becomes available.

## Baseline versus optimized metrics

No benchmark has been run. The following comparison is planned:

- **Baseline:** A single retrieval method over the same recorded memory corpus.
- **Optimized:** Conflict-aware hybrid retrieval plus reranking, using the same corpus and query set.

| Metric | Baseline | Optimized |
| --- | --- | --- |
| Input/output/total tokens per request | Not yet measured | Not yet measured |
| Estimated USD per 1,000 requests | Not yet measured | Not yet measured |
| End-to-end latency p50 | Not yet measured | Not yet measured |
| End-to-end latency p95 | Not yet measured | Not yet measured |
| Streaming TTFT p50, where applicable | Not yet measured | Not yet measured |
| Streaming TTFT p95, where applicable | Not yet measured | Not yet measured |
| Project-specific quality and failure rate | Not yet measured | Not yet measured |

Future results must identify the workload revision, model/provider configuration, environment, concurrency, sample counts, and quality criteria. Cost estimates must disclose pricing date and assumptions. Report the complete workload, including errors and retries, and retain raw results so baseline and optimized runs can be reproduced. Do not substitute target values for measurements.

## Portfolio

- [Raw Agent Tool Loop](https://github.com/sn41lz/agent-systems-tool-loop)
- [Context Window Engine](https://github.com/sn41lz/agent-systems-context-window)
- [Hierarchical Agent Memory](https://github.com/sn41lz/agent-systems-hierarchical-memory)
- [Multi-Agent Orchestrator](https://github.com/sn41lz/agent-systems-multi-agent-orchestrator)
- [Agent Evaluation Harness](https://github.com/sn41lz/agent-systems-eval-harness)
- [Secure Code Sandbox](https://github.com/sn41lz/agent-systems-secure-sandbox)
- [Durable Agent Runtime](https://github.com/sn41lz/agent-systems-durable-runtime)
- [Agent Platform API and SDKs](https://github.com/sn41lz/agent-systems-api-sdk)
