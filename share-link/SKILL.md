---
name: share-link
description: Use when user wants to share localhost, create a public URL, expose a local server, or let someone else view their dev server. Triggers on "share", "tunnel", "public URL", "expose port", "let them see my localhost"
---

# Share Link - Cloudflared Quick Tunnels

Expose any localhost port as a public HTTPS URL with one command. No account, no signup, no auth tokens.

## When to Use

- User wants someone else to see their local dev server
- User says "share", "tunnel", "public URL", "expose my localhost"
- You detect a dev server starting and user wants to share it

**Don't use:** ngrok (requires account), localtunnel (unreliable). Cloudflared quick tunnels are free, zero-config, and already installed on this system.

## Quick Reference

| Task | Command |
|------|---------|
| Share port | `cloudflared tunnel --url http://localhost:PORT --protocol http2 > /tmp/cloudflare-tunnel.log 2>&1 &` |
| Get URL | `sleep 8 && grep -aoE 'https://[a-z0-9-]+\.trycloudflare\.com' /tmp/cloudflare-tunnel.log \| head -1` |
| Stop | `pkill cloudflared` |
| Install | `brew install cloudflare/cloudflare/cloudflared` |

## Procedure

Follow this exact order. Skipping steps causes failures.

**Step 1: Install if needed**
```bash
command -v cloudflared || brew install cloudflare/cloudflare/cloudflared
```

**Step 2: Kill any existing tunnel**
```bash
pkill cloudflared 2>/dev/null; sleep 1; rm -f /tmp/cloudflare-tunnel.log
```

**Step 3: Configure allowedHosts (framework dev servers only)**

Framework dev servers block unknown hostnames. Skip for simple servers (Flask, Django, `python3 -m http.server`, etc.).

- **Vite** (`vite.config.*`): `server: { allowedHosts: ['.trycloudflare.com'] }`
- **Webpack Dev Server**: `devServer: { allowedHosts: ['.trycloudflare.com'] }`
- **Next.js**: No config needed

If you modify config, restart the dev server.

**Step 4: Create tunnel**
```bash
cloudflared tunnel --url http://localhost:PORT --protocol http2 > /tmp/cloudflare-tunnel.log 2>&1 &
```

`--protocol http2` is required — QUIC is blocked on many networks and the tunnel silently fails without it.

**Step 5: Wait and extract URL (8 seconds minimum)**
```bash
sleep 8
TUNNEL_URL=$(grep -aoE 'https://[a-z0-9-]+\.trycloudflare\.com' /tmp/cloudflare-tunnel.log | head -1)
echo "$TUNNEL_URL"
```

If empty, the tunnel is still connecting. Check with:
```bash
grep -a "Registered tunnel connection" /tmp/cloudflare-tunnel.log
```

If no registration lines, check errors: `tail -20 /tmp/cloudflare-tunnel.log`

**Step 6: Display URL prominently to user**

Show the URL clearly so they can share it.

## No Server Running?

For static files with no server:
```bash
python3 -m http.server 8000 &
```
Then tunnel port 8000. No allowedHosts config needed.

## Troubleshooting

| Symptom | Fix |
|---------|-----|
| "This host is not allowed" in browser | Add `.trycloudflare.com` to allowedHosts, restart server |
| Tunnel timeout / no URL after 15s | `--protocol http2` missing, or network blocks Cloudflare |
| Error 1033 / conflicting tunnels | `pkill cloudflared` first |
| Wrong content showing | Port mismatch — verify actual server port |
