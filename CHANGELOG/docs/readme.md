# README and plugin handoff

## 2026-10-15 — Link plugin sources and correct notice inventory

Give developers and coding agents direct links to all five plugin sources and explain the
external moving Git URL and fixed release commit. Replace the incorrect claim that every
plugin carries a LICENSE with links to the actual MIT files and Drawio's Apache-2.0 data notice;
all manifests, plugin code, versions, licenses, and attribution files remain unchanged.
**Commit**: this entry's commit (see `git log --follow`). **Author**: Codex.

The same handoff checker improved from 2/15 to 15/15. Existing manifest identity checks,
13 JSON files, 11 Python files, and eight JavaScript syntax checks passed before and after;
Drawio shape lookup and valid/invalid diagram checks retained their original outcomes.
This verifies repository documentation and local checks; it does not certify installation,
provider workflows, published releases, or complete third-party license compliance.
