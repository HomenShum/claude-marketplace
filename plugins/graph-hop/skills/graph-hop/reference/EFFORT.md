# Reasoning effort routing

## The picker

Observed on a ChatGPT Pro account, 2026-07-22, model GPT-5.6 Sol:

```
Instant · 5.5 · Medium · High · Extra High · Pro
```

`Instant` is non-reasoning. **Medium is the floor for anything that needs thinking.**

These were renamed on 2026-06-10. Guidance written before that date uses names that no longer
exist in the UI:

| Old | New |
|---|---|
| Instant | Instant |
| Thinking - Light | *removed* |
| Thinking - Standard | Medium |
| Thinking - Extended | High |
| Thinking - Heavy | Extra High |
| Pro Standard / Pro Extended | Pro |

Read the picker before assuming. Plans differ, and OpenAI renames these.

## Routing table

| Level | Use for |
|---|---|
| **Medium** | Recall from a thread, restating a prior decision, reformatting, "what did we conclude" |
| **High** | Default for real asks — spec review, next design increment, comparing options against criteria |
| **Extra High** | Ad-hoc advisor, when a missed constraint would cost a rebuild |
| **Pro** | One-shot whole-problem questions that are expensive to get wrong. Minutes per answer. |

## Four rules that matter more than the table

**1. Escalate; never open at the top.** Maximum effort on a simple task makes the output *worse* —
overthinking, hedging, caveats nobody asked for — at roughly 4-6x the cost and much higher
latency. Start at Medium, move up only when the reasoning comes back thin.

**2. Tighten the prompt before raising effort.** A vague question at Pro is a vague answer that
took five minutes. If the first answer was bad, ask whether the prompt was underspecified before
assuming the model was underpowered.

**3. Bump effort before switching models.** The common reflex on a bad answer is to reach for a
different model. One level of extra compute on the same model usually closes the gap.

**4. Code generation is the exception.** It is the one area where Medium to High is a large,
real gain — reasoning effort scales strongly with agentic coding performance. Do not leave
codegen on default.

## Cost awareness

Top-tier modes consume subscription quota far faster than their price ratio suggests.
Practitioner reports describe an entire weekly allowance burned in under two hours at the highest
setting, and quotas that used to last six days lasting two after a model change. Treat the top of
the ladder as an **ad-hoc advisor**, not a daily driver — even on Pro, even when the user says
cost is not a concern, because the constraint is rate limits rather than dollars.

## Choosing per thread in council mode

Do not put every thread on the same level. Route by what that thread is being asked for:

- Thread being asked to *recall* something → Medium
- Thread being asked to *reason about* the question → High
- The one thread whose framing you most expect to be wrong → Extra High or Pro

## Sources

- OpenAI ChatGPT release notes, 2026-06-10 model picker change
- GPT-5.5 thinking-effort practitioner guide (ud.hk, 2026-05-05)
- r/codex community consensus threads on Sol Medium vs Sol High as daily driver
