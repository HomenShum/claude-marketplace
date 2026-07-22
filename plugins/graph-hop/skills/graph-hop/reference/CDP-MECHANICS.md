# CDP mechanics and footguns

Every item here cost a real failure. Read before driving a long thread.

## Reading a conversation

`get_page_text` truncates around 50 000 characters and dumps the whole thread into context.
Long threads exceed that. Extract per turn instead:

```js
(() => {
  const n = [...document.querySelectorAll('[data-message-author-role]')];
  return 'TURNS=' + n.length + '\n' + n.map((e, i) =>
    i + '|' + e.getAttribute('data-message-author-role') +
    '|' + (e.innerText || '').length +
    '|' + (e.innerText || '').trim().slice(0, 200).replace(/\s+/g, ' ')
  ).join('\n@@\n');
})()
```

Then pull only the turns that matter by index. For a long assistant answer, get its outline first
rather than the whole body:

```js
[...el.querySelectorAll('h1,h2,h3,h4,strong')]
  .map(x => x.innerText.trim())
  .filter(t => t && t.length < 110)
```

### Turns arrive late and incomplete

- After navigating, `TURNS=0` for several seconds. Wait 4-8s and re-query; do not conclude the
  thread is empty.
- The list is virtualized. Early turns may be absent until you scroll up.
- A turn that is only an attachment can be missing from `[data-message-author-role]` entirely, so
  turn indices are not a reliable message count.

### Return values get blocked

`[BLOCKED: Cookie/query string data]` fires when the returned string contains DOM class names,
URLs, or runs of `? & =` — which ordinary technical prose hits constantly (any answer containing
a URL or a query example). The evaluation succeeded; only the return was suppressed.

Sanitize before returning long extracted prose:

```js
t.replace(/https?:\/\/\S+/g, '[url]').replace(/[?&=]/g, ' ')
```

Return short computed summaries rather than raw attributes, and slice long answers into
sub-2 KB chunks by heading rather than returning a whole message body.

## Writing into the composer

The composer is `#prompt-textarea` — a contenteditable ProseMirror div, not a textarea. There is
also a zero-size `<textarea>` in the DOM; ignore it.

**Use a synthetic paste.** One transaction, survives long threads:

```js
const el = document.getElementById('prompt-textarea');
el.focus();
const dt = new DataTransfer();
dt.setData('text/plain', message);
el.dispatchEvent(new ClipboardEvent('paste', { clipboardData: dt, bubbles: true, cancelable: true }));
```

**Do not use `execCommand('insertText')` on a long thread.** ~3 KB into a 22-turn conversation
froze the renderer and timed out CDP at 45 s.

**Do not type multi-line text with the `type` action.** Newlines submit the message.

## The duplicate-draft trap

ChatGPT persists composer drafts across page reloads. So:

1. An insert that appears to fail (renderer freeze, CDP timeout) may have actually landed.
2. Reloading does not clear it.
3. Your retry appends, producing a silently doubled message.

**Always verify before sending:**

```js
const t = document.getElementById('prompt-textarea').innerText || '';
return 'len=' + t.length + ' dup=' + ((t.match(/FIRST_SIX_WORDS_OF_YOUR_MESSAGE/g) || []).length);
```

Expect `dup=1`. If higher, clear and re-paste:

```js
const el = document.getElementById('prompt-textarea');
el.focus();
const r = document.createRange(); r.selectNodeContents(el);
const s = window.getSelection(); s.removeAllRanges(); s.addRange(r);
document.execCommand('delete');
```

## Sending

```js
const b = [...document.querySelectorAll('button')]
  .find(x => /send prompt/i.test(x.getAttribute('aria-label') || ''));
if (b && !b.disabled) b.click();
```

Confirm it actually sent — do not report success on the click alone:

```js
const t = (document.getElementById('prompt-textarea').innerText || '').length;
const n = document.querySelectorAll('[data-message-author-role]').length;
const generating = [...document.querySelectorAll('button')]
  .some(x => /stop/i.test(x.getAttribute('aria-label') || ''));
return 'composerLen=' + t + ' turns=' + n + ' generating=' + generating;
```

Sent means `composerLen=0` **and** the turn count rose.

## The effort picker

The composer has a button whose label is the current level. Open it, then read the menu items —
they are `[role=menuitemradio]`, and are **not** inside the same container as the model name, so a
menu-scoped query will only find the model:

```js
const want = ['Instant','Medium','High','Extra High','Pro'];
const out = [];
document.querySelectorAll('div,button,li,span').forEach(e => {
  const t = (e.innerText || '').trim();
  if (want.includes(t)) {
    const r = e.getBoundingClientRect();
    if (r.width > 10 && r.height > 10 && r.y > 0)
      out.push(t + ' cx=' + Math.round(r.x + r.width/2) + ' cy=' + Math.round(r.y + r.height/2));
  }
});
return out.join('\n');
```

The selection persists across reloads. Re-read it after any reload before sending.

## Coordinates vs refs

Screenshot pixel space is scaled from the real viewport. Compute the factor before clicking:

```
scale = screenshotWidth / window.innerWidth      // e.g. 1512 / 2048 = 0.738
screenshotXY = pageXY * scale
```

Prefer a programmatic `.click()` on an element found by `aria-label`. Real mouse clicks are
needed only when React ignores synthetic events — opening the effort menu is one such case.

## When the renderer hangs

Symptoms: CDP `Runtime.evaluate` times out at 45 s, **and screenshots return a stale frame that
looks identical after every scroll**. The stale screenshot is the tell — do not trust the image.

Recovery: navigate to the same URL to reload, wait 8-16 s, then confirm the composer exists and
the turns are back. Check for a persisted duplicate draft afterwards. Nothing was sent unless a
send was confirmed.
