# LoginWithNostr

**Add passwordless Nostr login to any website in 2 lines of code.**

Live at [loginwithnostr.com](https://loginwithnostr.com)

---

## What This Is

A developer-facing landing page that explains how to integrate "Login with Nostr" into any website. The actual login button is powered by [`login.js`](https://nostrkey.com/login.js), hosted from the NostrKey browser extension repo.

This repo contains:
- The marketing/docs site for developers
- Integration guides and examples
- Live demo of the login button

## How It Works (For Developers)

### 1. Add the script
```html
<script src="https://nostrkey.com/login.js"></script>
```

### 2. Add the button
```html
<div id="nostr-login"></div>
```

### 3. Listen for login
```javascript
document.addEventListener('nostr:login', function(e) {
    const pubkey = e.detail.pubkey;
    // That's the user's permanent, cryptographic identity.
    // Send it to your backend, use it as a user ID.
});
```

That's it. No backend required for the auth flow. No OAuth tokens. No passwords.

## Button Options

```html
<!-- Dark theme (default) -->
<div id="nostr-login" data-theme="dark"></div>

<!-- Light theme -->
<div data-nostr-login data-theme="light"></div>

<!-- Large button -->
<div data-nostr-login data-size="large"></div>

<!-- Auto-add a relay when user logs in -->
<div data-nostr-login data-relay="wss://your-relay.com"></div>
```

## Events

| Event | Detail | Description |
|-------|--------|-------------|
| `nostr:login` | `{ pubkey }` | User authenticated successfully |

## What Happens Under the Hood

1. User clicks "Login with Nostr"
2. Script checks for `window.nostr` (NIP-07 key manager like [NostrKey](https://nostrkey.com))
3. If installed: requests public key from the key manager
4. If not installed: redirects to [nostrkey.com/onboard](https://nostrkey.com/onboard), then polls for installation
5. On success: fires `nostr:login` CustomEvent with the user's public key
6. Your site receives a 64-character hex public key — that's the user's identity

## Bunker URLs (NIP-46)

### The problem with direct key access

When you use NIP-07 (`window.nostr`), the browser extension holds your private key and signs events directly. This is great for most use cases, but it means every signing request triggers a permission prompt — and the key is active in the browser's memory for as long as the extension is unlocked.

### What a bunker URL does

A **bunker URL** is an alternative login method defined by [NIP-46](https://github.com/nostr-protocol/nips/blob/master/46.md). Instead of giving a site direct access to your key, you create a temporary, revocable connection string that looks like this:

```
bunker://ab12cd34...?relay=wss://relay.nostrkey.com&secret=f9a1b2c3...
```

The site never sees your private key. Instead, signing requests travel through a relay to your key manager, which signs them and sends the result back. Think of it as a **proxy session** — the site gets the signatures it needs, but your key never leaves your device.

### How it works

```
┌──────────┐       ┌─────────────┐       ┌──────────┐
│  Website  │ ───►  │    Relay     │ ◄───  │ NostrKey │
│ (client)  │       │ (transport)  │       │ (signer) │
└──────────┘       └─────────────┘       └──────────┘
     1. Site sends signing request to relay
     2. Relay forwards to your key manager
     3. Key manager signs and returns via relay
     4. Site receives the signed event
```

- **No key exposure.** The private key stays in NostrKey. The site only ever receives finished signatures.
- **Revocable.** Close the session and the bunker URL is dead. No lingering access.
- **Portable.** A bunker URL works on any site that supports NIP-46 — paste it anywhere, on any device.
- **Relay-routed.** The connection goes through a relay (e.g. `relay.nostrkey.com`), so the site and your key manager don't need a direct connection.

### When to use it

| Scenario | Use NIP-07 | Use Bunker URL |
|----------|:----------:|:--------------:|
| Your own browser, trusted site | Yes | Either |
| Shared or public computer | No | Yes |
| Site you're trying for the first time | Maybe | Yes |
| Automated or long-running session | No | Yes |
| Mobile browser without extension support | No | Yes |

### Security

A bunker URL is a **bearer credential** — anyone who has it can request signatures on your behalf for as long as the session is active.

- **Never share it publicly.** Treat it like a session token.
- **Revoke when done.** Disconnect from NostrKey when you're finished.
- **One URL per session.** Generate a fresh one each time.

### Try it

Visit [loginwithnostr.com](https://loginwithnostr.com) — the site includes a live bunker URL generator. If you have [NostrKey](https://nostrkey.com) installed, you can create a bunker URL and test the full round-trip right from the page.

## Repo Structure

```
docs/              # Landing page (served via GitHub Pages)
  index.html       # Main site — developer docs + live demo
  CNAME            # Custom domain: loginwithnostr.com
README.md          # This file
```

## Local Development

```bash
# Serve locally
cd docs && python3 -m http.server 8080
# Open http://localhost:8080
```

## Deployment

Served via GitHub Pages from the `docs/` folder on `main` branch.

DNS (Hover):
- 4x A records → GitHub Pages IPs (185.199.108-111.153)
- CNAME `www` → `humanjavaenterprises.github.io`

## Ecosystem

LoginWithNostr is part of the [Humanjava](https://humanjava.com) product ecosystem:

| Component | Role |
|-----------|------|
| [NostrKey](https://nostrkey.com) | Key manager (browser extension + mobile apps) |
| [login.js](https://nostrkey.com/login.js) | The actual embed script (lives in nostrkey.browser.plugin.src) |
| [relay.nostrkey.com](https://relay.nostrkey.com) | Heartbeat relay + Apple Wallet pass service |
| [nostrkey.com/onboard](https://nostrkey.com/onboard) | User onboarding flow |
| **loginwithnostr.com** | This site — developer docs |

## Brand

- Accent color: Amber `#fbbf24`
- Background: Navy `#0f172a`
- Card: `#1e293b`
- System font stack (no custom fonts)
