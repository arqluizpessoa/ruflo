# Security baseline (23 October 2026)

This repository uses the [Cloudflare security-audit-skill](https://github.com/cloudflare/security-audit-skill) as a **review method**, not as evidence that all issues are fixed.

## Protected boundaries

- **Authentication / authorization:** validate identity server-side; enforce organization, tenant, role and object permissions at the last trusted decision point (including background operations).
- **Secrets:** never commit service-role keys, API secrets, SMTP/IMAP passwords, private keys or real production `.env` secrets. Browser-exposed variables (`VITE_*`, `NEXT_PUBLIC_*`) are public by design. Supabase publishable/anon keys are public; they do not replace RLS.
- **AI / agents:** treat emails, documents, retrieved text, model output and MCP/tool responses as untrusted. Bind the action to the authenticated principal and exact approved operation; no prompt alone is an authorization check.
- **API / uploads:** constrain URLs to prevent SSRF, validate paths and object ownership, restrict upload type/size, verify webhooks and rate-limit potentially expensive requests.
- **CI / supply chain:** keep actions pinned and workflows least-privilege; review dependency upgrades before merging; do not execute untrusted PR code on privileged/self-hosted runners.
- **Privacy:** apply tenant/record ACLs to attachments, downloads, caches, logs, exports, and backups as well as primary data.

## What this automated check does

`.github/workflows/security-baseline.yml` runs a **source-only** guard that rejects tracked private-key material and high-confidence private credentials stored in `.env*` files. It does not execute application code, install dependencies, or require production secrets. It cannot see secrets in Git history, production settings or external services, and cannot substitute for a comprehensive security audit.

## Review workflow

For changes to auth, data access, agents, files, CI or infrastructure, use the Cloudflare skill's reconnaissance, threat-boundary analysis, independent confirmation and `confirmed` vs `needs_validation` verdicts. Run target-controlled tests only inside an appropriately isolated sandbox. For reference:

```sh
npx skills add https://github.com/cloudflare/security-audit-skill --skill security-audit
```

Before merging, require a code review, validate CI, verify environment configuration and review Supabase RLS / GitHub branch protection separately. A green workflow is **not** a claim of complete security.
