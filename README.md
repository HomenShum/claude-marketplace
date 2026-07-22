# HomenShum — Claude Code marketplace

A [Claude Code](https://claude.com/claude-code) plugin marketplace. One install, versioned
updates, every skill in one place.

This exists because a skill cloned loosely into `~/.claude/skills/` reaches exactly one machine and
never updates. A marketplace fixes both: `/plugin` installs it anywhere and delivers updates when a
plugin's version bumps.

## Install

```
/plugin marketplace add HomenShum/claude-marketplace
/plugin install graph-hop@homenshum
```

Then browse the rest:

```
/plugin
```

## Update

When a plugin's version bumps, refresh the marketplace and update:

```
/plugin marketplace update homenshum
/plugin update graph-hop@homenshum
```

## Plugins

| Plugin | What it does |
|---|---|
| **graph-hop** | Drive your ChatGPT threads from Claude Code over Chrome CDP — consult one thread or fan a question across many, then synthesize agreement / contradiction / novel into a verdict. |
| **agentic-ui-qa** | Universal QA + dogfooding protocol for agentic UIs: persona-driven end-to-end drive, artifact-verified claims, Agentic UI Bar scoring, bounded fix-revamp loop. |
| **drawio-skill** | Diagrams, flowcharts, architecture/UML/ER figures, and mind maps as `.drawio` XML, exported via the native draw.io CLI. |
| **easier-to-read-submissions** | Make every commit, branch, and PR easier to read: per-surface changelog entries and a verified demo recording when UI changed. |

## Migrating from a loose skill install

If you previously cloned one of these into `~/.claude/skills/`, remove that copy after installing
the plugin, so the skill is not registered twice:

```bash
rm -rf ~/.claude/skills/graph-hop
```

## License

MIT © Homen Shum. Each plugin also carries its own LICENSE.
