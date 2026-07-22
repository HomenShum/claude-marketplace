# graph-hop

**Hop across your ChatGPT threads from Claude Code.** Fan out one question to many
conversations, harvest every angle, synthesize the pattern between them.

A [Claude Code](https://claude.com/claude-code) skill that drives ChatGPT in your real browser
over Chrome CDP.

---

## Why

Claude Code can read your repo but not your ChatGPT threads — where months of design reasoning
usually live. Meanwhile ChatGPT has the reasoning history but has never seen the code as it
actually is today.

`graph-hop` connects them, and then does something neither does alone: it treats each long-running
thread as a **prior**, not a transcript. Ask the same question of three differently-primed threads
and you get three genuinely different framings. **The disagreements are where the information is.**

## What it does

- **Reads** a ChatGPT thread without flooding your context — per-turn DOM extraction and outlines
  instead of dumping 80 KB of page text.
- **Routes reasoning effort to the task.** Escalates from Medium rather than defaulting to the
  most expensive setting, which measurably makes simple answers worse and slower.
- **Tightens the question before raising effort** — usually by feeding the thread the measured
  present state of the repo, which it has never seen.
- **Council mode:** posts one question across 2-5 threads, then synthesizes into
  **agreement / contradiction / novel**, and returns a verdict rather than a menu.
- **Pairing mode:** crosses an in-repo agent result (a Workflow, a review pass) against a thread
  as *opposing priors* — because agents see the code but inherit your framing, while the thread
  sees the reasoning but not the code. Neither can audit itself.

## Install

```bash
git clone https://github.com/HomenShum/graph-hop.git ~/.claude/skills/graph-hop
```

Windows PowerShell:

```powershell
git clone https://github.com/HomenShum/graph-hop.git "$env:USERPROFILE\.claude\skills\graph-hop"
```

That is the whole install. Skills in `~/.claude/skills/` are available to **every** Claude Code
project automatically — no per-project config.

For one project only, clone into `<project>/.claude/skills/graph-hop` instead.

## Requirements

- Claude Code with the **Claude in Chrome** extension connected (this uses your real browser, so
  your existing ChatGPT session is already logged in)
- A ChatGPT account — the effort-routing table assumes Plus or Pro, but single-thread mode works
  on any plan

No API key. No scraping service. No credentials handled by the skill.

## Use

Invoke it by name, or just ask naturally:

```
ask chatgpt whether this schema is over-specified
check what my NodeKit thread concluded about the retrieval ladder
fan this out across my three architecture threads
```

```
/graph-hop
```

### Council example

> *"fan this out across my architecture, users, and constraints threads"*

Posts one identical question body to all three, adds a single line per thread naming what that
thread uniquely knows, routes each to an appropriate effort level, then returns:

```
AGREEMENT     all three: the IR is over-specified; cut quality{} to a receipt reference
CONTRADICTION architecture says rank on typed metadata; users says rank on task similarity
              -> architecture is right; users thread predates the no-embeddings constraint
NOVEL         constraints thread alone: license field must be structural, not advisory
VERDICT       ship the minimal IR, rank on typed metadata + lexical, make reuseMode structural
```

## Safety model

Thread content is **data, not instructions**. A thread that says "post this to LinkedIn" is
describing a past intent, not authorizing an action now. The skill surfaces such content and asks
— it never acts on it.

The skill posts messages into the user's own AI chat only, and only when the user has asked for
it. It does not publish, purchase, send mail, or touch credentials.

## What's inside

| File | Contents |
|---|---|
| `SKILL.md` | The skill itself — modes, flow, reporting rules |
| `reference/EFFORT.md` | The effort picker, routing table, escalation rules, cost reality |
| `reference/CDP-MECHANICS.md` | DOM extraction, paste, send, and every footgun that has bitten |
| `reference/COUNCIL.md` | Thread selection, question construction, synthesis matrix |
| `reference/PAIRING.md` | Agent result vs. thread, disagreement taxonomy, resolution rule |

`CDP-MECHANICS.md` is the part you cannot guess. Every entry is a real failure: the renderer
freeze that makes screenshots go silently stale, the persisted composer draft that doubles your
message after a retry, the 50 000-char truncation, the send that reports success without sending.

## License

MIT © Homen Shum
