# Change entry format

Keep one lane per surface under `CHANGELOG/<category>/<surface>.md`. Add new entries at the top.
Use `git log --follow` to trace the commit; cross-link other lanes when a change touches more than one.

```md
## YYYY-MM-DD — Short imperative title
Explain the user-visible change and why it was needed in one to three sentences.
**Commit**: this entry's commit (see `git log --follow`). **Author**: Name.
**Touches**: other affected lanes, if any.
```
