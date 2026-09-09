---
name: authoring-security
description: The security discipline every Minder plugin must follow. Use when a plugin fetches a URL, parses XML, interpolates user/config input into a request path or metric tag, or handles a secret. Enforces SSRF guards, defusedxml, input allowlists, and secret handling — the same rules the plugin-security-reviewer agent checks. Pair with [[authoring-guide]].
---

# authoring-security — the plugin security discipline

**Canonical reference (read, don't duplicate):** the worked SSRF guard in the
[`news`](https://github.com/minderhq/plugins/blob/main/news/__init__.py) plugin and
[`plugins/CONTRIBUTING.md`](https://github.com/minderhq/plugins/blob/main/CONTRIBUTING.md).
Trust boundaries are loose (single-tenant / self-hosted), so a leaked token must not
turn "add a feed" into "probe internal services." Build these in from the start.

## SSRF — any user/config-supplied URL
- **https-only**, require a hostname, then **resolve via `getaddrinfo` and reject**
  any resolved IP that is private / loopback / link-local (incl. the cloud-metadata
  endpoint `169.254.169.254`) / reserved / multicast.
- Check **every** returned A-record (defeats DNS-rebinding), and **fail closed** on
  resolution failure.
- **Re-vet every redirect** with an httpx response hook — the original-URL check does
  not cover a `302` to an internal address one hop later. Join relative `Location`
  before checking.

## XML — always defusedxml
Parse with `import defusedxml.ElementTree as ET`, never stdlib `xml.etree` — hardened
against XXE / entity-expansion on a hostile or MITM'd feed.

## Injection — allowlist before interpolation
Anything interpolated into a URL path or an InfluxDB tag gets a strict allowlist regex
first (e.g. `^[A-Za-z0-9._-]+\Z`). Block newline, `,`, `=`, space, `?`, `&`, `#` —
guards both query-string injection and line-protocol corruption.

## Secrets & code
- No hardcoded credentials; declare secret config fields with `secret: true` (masked
  on `GET /config`). Prefer keyless designs where possible.
- **No arbitrary code, no plugin-supplied HTML**; `DISPLAY.logo` is a lucide name.

## Testing — prove it
Security-critical logic (the SSRF classifier, redirect hook, input guards, tag
escaping) must be tested **directly**, not only stubbed. All HTTP is faked — no
network in tests. The `plugin-security-reviewer` agent will flag any gap above.
