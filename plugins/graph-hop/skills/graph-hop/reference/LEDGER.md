# Ledger mode — persistent cross-thread memory

Without a ledger, graph-hop is amnesiac: every invocation re-lists threads, re-reads them, and
re-derives which thread knows what. The ledger closes that loop. It is **the Atlas token-lever
recursed onto the skill itself** — Atlas distills builds into a searchable store so the agent
spends tokens only on new parts; the ledger distills *thread conclusions* into a searchable store
so the agent spends tokens only on new questions.

It is **not** a new database. It reuses two stores that already exist in a Claude Code session:
file-memory nodes (markdown + frontmatter, the same format the memory system uses) and
context-mode's FTS5 index for search. No embedded graph DB, no new dependency.

## Where it lives

A **global**, cross-project store — because the whole point is hopping between patterns that span
projects:

```
~/.claude/graph-hop/ledger/
  thread-<slug>.md        one node per consulted thread
  INDEX.md                one line per node, newest first
```

Global, not project-scoped: a thread about model routing informs a finance app and a hackathon
build alike. Project memory (`~/.claude/projects/<p>/memory/`) stays for project facts; the ledger
is the cross-thread layer above it.

## Node shape

```markdown
---
thread: 6a571625-3b68-83e8-ad1b-2ebe297528cc
title: NodeKit
url: https://chatgpt.com/c/6a571625-3b68-83e8-ad1b-2ebe297528cc
prior: the model-routing + evolution-ledger framing of NodeKit
last-consulted: 2026-07-22
turns-at-last-read: 24
---

## Concluded
- stage-1 retrieval card budget = 6144 bytes
- rank on fielded BM25 + ontology, no embeddings
- start Atlas from a populated internal corpus, not IR/CLI/MCP

## Edges
- contradicts [[thread-nk-users]] on what the ranker scores
- forked-from [[thread-nodekit]]   (shares turns 0-13 — NOT independent)

## Stale-after
- any change to computeNodeKitSourceHash invalidates the candidate-bound conclusions
```

`prior` is the one line council mode needs to pick threads that disagree. `edges` are what make it
a graph rather than a list. `forked-from` is load-bearing: forked threads agreeing is not
independent confirmation, and the ledger must record it so synthesis does not double-count.

## When to write

After harvesting any thread — single, council, or pairing. Write or update its node with: the
prior, the conclusions that survived, edges to other nodes, and the fact that would make it stale.
Bump `turns-at-last-read` so the next visit knows whether the thread grew.

Then append/update one line in `INDEX.md`:

```
- [[thread-nodekit]] — NodeKit model-routing prior · 24 turns · 2026-07-22
```

## When to read

**Before** listing or reading any thread. Query the ledger first:

```
ctx_search(queries: ["<the question>", "<the domain>"], source: "graph-hop-ledger")
```

- Node exists and `turns-at-last-read` matches the live thread → use the recorded conclusions;
  do not re-read the body.
- Node exists but the thread has grown → read only the new turns since `turns-at-last-read`.
- No node → cold read, then write one.

For council selection, read the `prior` and `edges` of candidate nodes to pick threads that
disagree — a graph query, not five full re-reads.

## Staleness is mandatory, not optional

A ledger that serves stale conclusions is worse than no ledger. Two guards:

1. Every node carries `stale-after` — the concrete fact that invalidates it. Check it before
   trusting the node.
2. `turns-at-last-read` vs the live turn count tells you if the thread moved. If it did, the node
   is provisional until reconciled.

When a conclusion is stale, say so in the synthesis and re-read; never present a stale node as
current. This is the same discipline the whole skill applies to threads.

## Kill criterion

The ledger earns its place only by cutting re-reads. Concrete test: a council over 3
already-ledgered threads must skip the full-body read of **at least 2** of them. If it does not
measurably reduce reads, the ledger is dead weight and comes out. Do not keep it on faith.

## What it must never become

- **A source of truth.** The threads are the source of truth. The ledger is a cache with a
  staleness contract.
- **A fourth memory silo.** It reuses file-memory format + context-mode FTS5 on purpose. If a fact
  belongs in project memory, put it there and link it; do not duplicate.
- **An embedded graph DB.** Files are nodes, `[[links]]` are edges, FTS5 is the query. That is
  enough. A real graph engine is engineering for a caching problem the file-graph already solves.
