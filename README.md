# Thriftily

**Point your phone at a thrift-store item and instantly learn if it's worth flipping.**

Thriftily is a tiny web app for sourcing resale ("flip") opportunities while you walk a thrift
store, garage sale, or estate sale. Aim your phone, tap **Scan**, and a Claude vision model
identifies the most resale-worthy item in frame and rates it:

| Verdict | Meaning |
|:--:|---|
| 🟢 **FLIP** | Grab it — worth reselling for profit |
| 🟡 **MAYBE** | Borderline; look closer |
| 🔴 **SKIP** | Not worth the trouble |

Each result comes with an estimated resale price range and a one-line reason. It turns a slow,
error-prone eyeball judgment into a fast gut-check so you only invest attention in the promising
pieces.

> **Live app:** https://dsirois.github.io/Thriftily/

---

## Highlights

- **Zero install** — it's a single HTML file. Open the URL, "Add to Home Screen," done. No app
  store, no build step, no framework.
- **Bring-your-own-key** — you supply your own Anthropic API key; it's stored only in *your*
  browser and sent only to Anthropic. There is no shared backend and no server that sees your
  key.
- **Cheap** — uses the `claude-haiku-4-5` vision model, roughly **$0.001–0.003 per scan**.
- **Fast triage** — tap-to-scan by default, with an optional hands-free auto-scan mode.
- **Offline-capable hosting** — also runs from a plain static file server on a home LAN.

---

## Getting started

1. **Get an Anthropic API key** at [console.anthropic.com](https://console.anthropic.com) (you
   pay Anthropic directly for what you use).
2. **Open the app** on your phone: <https://dsirois.github.io/Thriftily/>
3. **Paste your key** on first launch → **Save & start**.
4. **Allow camera access** when prompted.
5. **Add to Home Screen** (Chrome ⋮ menu) so it launches fullscreen like a native app.

Then just aim at an item and tap **Scan item**. The phone buzzes on a FLIP. The **Auto** button
toggles a hands-free scan every 8 seconds — off by default, since every scan is a paid API call.

---

## How it works

```
camera → capture frame → downscale & JPEG-encode → Anthropic vision API
       → parse verdict → colored FLIP / MAYBE / SKIP card
```

The whole app is one ~8 KB `index.html`: inline CSS, vanilla JavaScript, no dependencies. It
calls the Anthropic Messages API directly from the browser (using the
`anthropic-dangerous-direct-browser-access` header) with a captured photo and a prompt asking
for a compact JSON verdict.

---

## Privacy & security

- **Your key never leaves your device except to reach Anthropic.** It lives in your browser's
  `localStorage` and is attached only to requests to `api.anthropic.com`.
- **The public site contains no secrets.** Anyone who opens the URL sees an empty app that asks
  for *their own* key — there is nothing shared between users.
- **No analytics, no tracking, no backend.** Photos are sent to Anthropic for the scan and are
  not stored anywhere by this app.

---

## Configuration

A couple of knobs live at the top of `index.html`:

- **`MODEL`** — the vision model. Defaults to `claude-haiku-4-5` (cheap). Swap to
  `claude-opus-4-8` if you want sharper judgments at higher cost.
- **Auto-scan interval** — the `setInterval(scan, 8000)` in `toggleAuto()` (milliseconds).
- **The prompt** — tune the FLIP/MAYBE/SKIP instruction to match your local resale market and
  margin threshold.

---

## Self-hosting

Because it's one static file, you can host it anywhere that serves HTTPS. Two paths:

**GitHub Pages (this repo).** Push to `main`; Pages redeploys in ~1 minute:
```bash
git commit -am "update" && git push
```

**Any static server (e.g. a home LAN box).** The camera API requires a *secure context*
(HTTPS or a whitelisted origin). Over plain HTTP on a LAN, enable the camera by adding the
origin to Chrome's `chrome://flags/#unsafely-treat-insecure-origin-as-secure` list. Example
systemd unit serving it with Python's built-in server:
```ini
ExecStart=/usr/bin/python3 -m http.server 8088 --directory /opt/thriftily
```

---

## Roadmap

- Running profit tally across a scanning session
- A saved list of FLIP hits to review later
- Barcode / label lookup for real comps on tagged goods (books, electronics)

---

## License

[MIT](LICENSE) © 2026 Dennis Sirois.

## Notes

Personal project — provided as-is, no warranty. You are responsible for your own API usage and
costs. Resale value estimates are a model's best guess, not appraisals; verify before you buy.
