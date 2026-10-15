# HomenShum — Claude Code marketplace

A [Claude Code](https://claude.com/claude-code) plugin marketplace. Installable skills and
their source locations in one place.

This exists because a skill cloned loosely into `~/.claude/skills/` reaches exactly one machine and
never updates. A marketplace gives each machine the same install names and a place to refresh
plugin entries. Source and update limits are listed below.

## Install

```
/plugin marketplace add HomenShum/claude-marketplace
/plugin install graph-hop@homenshum
/plugin install proof-carrying-release@homenshum
```

Then browse the rest:

```
/plugin
```

## Update

Refresh the marketplace, then update an installed plugin:

```
/plugin marketplace update homenshum
/plugin update graph-hop@homenshum
```

## Plugins

| Plugin | What it does |
|---|---|
| [**graph-hop**](https://github.com/HomenShum/graph-hop) | Drive your ChatGPT threads from Claude Code over Chrome CDP — consult one thread or fan a question across many, then synthesize agreement / contradiction / novel into a verdict. |
| [**proof-carrying-release**](https://github.com/HomenShum/proof-carrying-release/tree/af39fa70ab61cd3e8b5071bcc2ccb7b47549ee74) | Carry one exact revision through Graphite, candidate/canary gates, canonical live proof, rollback, receipt closure, and recoverable only-main cleanup. |
| [**agentic-ui-qa**](./plugins/agentic-ui-qa) | Universal QA + dogfooding protocol for agentic UIs: persona-driven end-to-end drive, artifact-verified claims, Agentic UI Bar scoring, bounded fix-revamp loop. |
| [**drawio-skill**](./plugins/drawio-skill) | Diagrams, flowcharts, architecture/UML/ER figures, and mind maps as `.drawio` XML, exported via the native draw.io CLI. |
| [**easier-to-read-submissions**](./plugins/easier-to-read-submissions) | Make every commit, branch, and PR easier to read: per-surface changelog entries and a verified demo recording when UI changed. |

The [marketplace manifest](.claude-plugin/marketplace.json) defines these sources. Three plugins
are bundled here; each skill starts at `plugins/<name>/skills/<name>/SKILL.md`. `graph-hop` uses
an external Git URL without a commit pin; its version label does not pin repository content.
`proof-carrying-release` is fixed to the commit linked above. Updating the marketplace alone
does not advance that pin.

This repository has no package build or repository-wide CI workflow. Follow the linked skill's
prerequisites before running its commands; browser, draw.io, and provider access depend on the plugin.

## Migrating from a loose skill install

If you previously cloned one of these into `~/.claude/skills/`, remove that copy after installing
the plugin, so the skill is not registered twice:

```bash
rm -rf ~/.claude/skills/graph-hop
```

## License

The root [MIT license](LICENSE) covers marketplace-authored material. The bundled
[agentic-ui-qa license](plugins/agentic-ui-qa/skills/agentic-ui-qa/LICENSE) is also MIT.
`drawio-skill` and `easier-to-read-submissions` do not include separate LICENSE files in their
plugin directories.

Drawio's bundled shape data has an
[Apache-2.0 attribution notice](plugins/drawio-skill/skills/drawio-skill/data/SHAPE-INDEX-NOTICE.md).
Keep that notice with the data. For external plugins, inspect the license in the linked source
repository, using the pinned revision where one is specified.
