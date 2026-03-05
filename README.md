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
