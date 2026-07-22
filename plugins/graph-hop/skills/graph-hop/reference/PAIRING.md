# Pairing mode — in-repo result vs. thread, as opposing priors

Council mode crosses several ChatGPT threads. **Pairing mode crosses two different *kinds* of
reasoner**: an in-repo agent result (a Workflow, a subagent fan-out, a review pass) against a
long-running ChatGPT thread.

This is not "get a second opinion." The two are useful precisely because they **fail in opposite
directions**.

## Why they are genuinely opposed

| | In-repo agent result | ChatGPT thread |
|---|---|---|
| Sees | The actual code, real file paths, test output | Months of design reasoning and rejected alternatives |
| Blind to | Whether the *premise* it was handed is right | What the code actually looks like today |
| Primed by | Your framing — it inherits your plan's assumptions | Its own prior output, which it will defend |
| Catches | Facts: what exists, what compiles, what passes | Premises: whether you're solving the right problem |
| Cannot catch | A wrong premise you gave it | A fact that changed after it last spoke |

An agent told to "implement steps 1-5 of this plan" will implement steps 1-5 of a bad plan
flawlessly and report success. A thread asked to review that plan has no idea whether step 3
already exists. **Neither can audit itself. Each audits the other's blind spot.**

## Protocol

### 1. Run both independently — do not cross-contaminate

Do **not** show the thread the workflow's plan before asking, and do not seed the workflow with
the thread's spec as gospel. The value is in independent derivation. If the thread just reviews
the workflow's output, it becomes a rubber stamp with a prior.

### 2. Give each what it lacks

- **To the thread:** the measured present state — real paths, real counts, what is built vs.
  specced, and the constraints that invalidate obvious answers. Threads reason confidently about
  stale pictures.
- **To the agents:** the code, the conventions, and explicit permission to report blockers instead
  of inventing success.

### 3. Ask for falsifiable output from both

From the thread: named fields to cut, a concrete budget, a metric with a kill threshold.
From the agents: literal command output, and an honest "NOTHING RUN" when nothing was run.

Vague on either side makes the comparison meaningless.

### 4. Classify every disagreement by *type* before resolving

This is the step that makes pairing work. Do not argue the disagreement on its merits first —
classify it:

- **FACT** — the thread asserts something about the repo that is false or stale.
- **PREMISE** — the agents built on an assumption from your plan that the thread rejects.
- **SCOPE** — both are right about their own layer; they disagree on sequencing or what to build
  first.
- **TASTE** — no evidence separates them. Say so; do not manufacture a winner.

### 5. Resolution rule

> **The thread wins on premise. The agents win on fact.**

If the thread says "this whole section is the wrong shape," take it seriously even when the code
already exists — that's exactly what the agents structurally cannot see. If the thread says
"module X doesn't exist" and the agents read it at a path, the agents are right.

**SCOPE disagreements are usually the most valuable.** They mean you were about to build the right
thing in the wrong order.

### 6. Verdict

State what changes as a result: what gets kept, what gets discarded, what gets resequenced. If the
pairing changed nothing, say that too — a pairing that always produces a rewrite is a pairing that
isn't being judged honestly.

## Worked example

A Workflow was implementing an asset registry, ordered `IR schemas -> store -> CLI -> retrieval ->
MCP`, derived from a static repo audit. Independently, a ChatGPT thread at Pro effort was given the
measured repo state and asked three falsifiable questions.

| Disagreement | Type | Resolution |
|---|---|---|
| Thread: the asset schema is over-specified — split into a thin retrieval entry and a fat recipe fetched only for a selected item, cutting 16 field groups | **PREMISE** | Thread wins. The agents were faithfully implementing a single fat schema nobody had challenged. |
| Thread: do not start with the IR, Studio, or MCP — start with an internal pattern shortlist | **SCOPE** | Thread wins. The build order optimized for dependency cleanliness, not for the earliest falsifiable result. |
| Agents: `nodekit registry` verb is already taken; zero new deps allowed | **FACT** | Agents win. Read from the code; the thread had no way to know. |

Net: the sequencing was wrong and the schema was wrong, but the constraints were right. Neither
side alone would have produced that.

## Anti-patterns

- **Rubber stamp** — showing the thread the agents' output and asking "is this good?"
- **Merits-first** — arguing a disagreement before classifying it, so a stale fact gets debated
  as though it were a design position.
- **Fact deference** — letting the thread's confident tone override code you can read.
- **Premise deference** — dismissing a framing objection because "the code already works." Working
  code built on a wrong premise is the most expensive kind.
- **Manufactured winner** — forcing a resolution on a TASTE disagreement instead of naming it.
