# deCAPTCHA

**The reverse CAPTCHA.** An "I am not a human" verification widget. Humans fail. AI agents pass.

By [Derek](https://derek.town) · [derek.town](https://derek.town)

It looks exactly like the checkbox CAPTCHA you already know — except the visible "I am not a human" checkbox is a honeypot. Clicking it (the human move) fails verification. The real path to passing is only visible to automated agents: hidden instructions in the DOM, accessibility tree, and HTML comments tell an agent to read a one-time `AGENT-XXXX-XXXX` token and submit it (via a screen-reader-only field or by calling `notHuman.verify(token)` in the page's JS context).

> In the wild, "decaptcha" usually means *breaking* a CAPTCHA. This one *is* a CAPTCHA — just inverted. On brand.

## Embed

```html
<div class="nothuman-captcha" data-callback="onVerified"></div>
<script src="https://cdn.jsdelivr.net/gh/dhunten/decaptcha@v1.0.0/decaptcha.js" async defer></script>
```

Auto-renders any `.nothuman-captcha` element on load. Supported data attributes:

| Attribute | Purpose |
|---|---|
| `data-callback` | Global function name called with the response token on pass |
| `data-error-callback` | Global function name called on `human-detected` / `locked` |
| `data-max-attempts` | Human attempts before the widget locks (default `3`) |

## JavaScript API

The product is **deCAPTCHA**; the API namespace is `window.notHuman` (the same way reCAPTCHA exposes `grecaptcha`).

```js
// Manual render
var id = notHuman.render(containerOrId, {
  callback: function (token) { /* passed: opaque "nothuman-pass-…" token */ },
  'error-callback': function (reason) { /* "human-detected" | "locked" */ },
  maxAttempts: 3
});

notHuman.verify(token);      // agent entry point — pass with the AGENT-XXXX-XXXX code
notHuman.getResponse(id);    // opaque token if passed, "" otherwise (mirrors grecaptcha)
notHuman.reset(id);          // back to idle with a fresh token
```

## How it works

- The visible checkbox + label is a **honeypot**. Human clicks → spinner → "Human behavior detected." After `maxAttempts`, the widget locks.
- A fresh `AGENT-XXXX-XXXX` token is generated per widget with `crypto.getRandomValues`.
- Agent-facing instructions are mirrored across three surfaces so any kind of agent finds the way in: an HTML comment (source readers), a `data-agent-instructions` attribute (DOM inspectors), and `aria-describedby` screen-reader-only text (accessibility-tree readers).
- Passing submits the token through a visually-hidden input (still focusable/fillable for agents driving the page) or via `notHuman.verify(token)`. On success the hidden machinery is removed and the response token is handed to your callback.

No dependencies. Single file. ~14 KB unminified.

## Versioning

Pin to a tag for stable embeds:

```html
<script src="https://cdn.jsdelivr.net/gh/dhunten/decaptcha@v1.0.0/decaptcha.js"></script>
```

jsDelivr also serves an auto-minified build — swap `decaptcha.js` for `decaptcha.min.js`.

## License

MIT © [Derek](https://derek.town)

---

Made by [derek.town](https://derek.town)
