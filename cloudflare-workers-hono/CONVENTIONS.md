# Cloudflare Workers + Hono + Angular SaaS Conventions

Shared constants and patterns for full-stack SaaS on Cloudflare Workers.

## Stack

CF Workers+Hono v4.12+ | Angular 21+Ionic 8+PrimeNG 21 | D1/Neon | Drizzle v1 | Zod | Clerk Core 3 | Stripe | Inngest v4 | Resend | Bun 1.3 | TS 5.9 | Playwright v1.59+ | Vitest | ESLint+Prettier | PostHog | Sentry | GA4/GTM

## Angular 21

Zoneless by default (CLI scaffolds without Zone.js) | Vitest default test runner | Signal Forms experimental | Angular Aria library dev preview (8 patterns, 13 components) | MCP server in CLI for AI-assisted dev
Standalone-only (no NgModules) | Signal stores per feature | `providedIn: 'root'` | Control flow: `@if`/`@for`/`@switch`/`@defer`

## TypeScript 5.9

Stable TC39 Decorator Metadata | `strictInference` flag | 10-20% build perf gains | Conditional type narrowing improvements

## Drizzle v1 Patterns

`sqliteTable` for D1 | plural snake_case tables (`users`, `blog_posts`) | `$inferSelect`/`$inferInsert` for types | `createInsertSchema`/`createSelectSchema` for Zod (now `drizzle-orm/zod`) | batch API (not `BEGIN` — D1 doesn't support transactions) | prepared statements for repeated queries
v1 migration: `journal.json` removed — run `drizzle-kit up` to migrate. RQBv2: relations defined in one place.

## CF Workers Limits (2026)

Free: 100K req/day, 10ms CPU | Paid: unlimited req, 30s CPU default / 5min max | Memory: 128MB/isolate | Worker size: 3MB free / 10MB paid | D1 storage: 250GB→1TB (paid) | Cron: 5 free / 250 paid
D1 global read replication (beta): 40-60% latency decrease. Vectorize: 10M vectors/index, topK 50.

## Hono Patterns

`createFactory<{ Bindings: Env }>()` for reusable typed middleware chains | Method chaining `app.use().get().post()` preserves RPC type inference — never separate controller files | `hc<AppType>(BASE_URL)` for typed client | `@hono/zod-validator` on all bodies | `app.onError()+app.notFound()` centralized | Split large apps: `app.route('/path', subApp)`
Error envelope: `{ error: string, code?: string, details?: unknown }` | Rate limit public endpoints: KV-based per-IP | Turnstile on all forms | `GET /health` returns `{status, version, timestamp}`

## Hono Worker Starter

```typescript
import { Hono } from 'hono';
import { createFactory } from 'hono/factory';
import { secureHeaders } from 'hono/secure-headers';
import { cors } from 'hono/cors';

interface Env {
  DB: D1Database;
  KV: KVNamespace;
  AI: Ai;
  VECTORIZE: VectorizeIndex;
  TURNSTILE_SECRET: string;
}

const app = new Hono<{ Bindings: Env }>();
app.use('*', secureHeaders());
app.use('/api/*', cors({ origin: ['https://yourdomain.com'] }));
app.get('/health', (c) => c.json({ status: 'ok', version: '1.0.0', timestamp: new Date().toISOString() }));
export default app;
```

## Inngest v4 (Breaking — Mar 2026)

v3→v4 breaking: `EventSchemas` removed → `eventType()` per-event with Standard Schema (Zod/Valibot/ArkType) | serve options → client constructor | default mode → cloud (set `isDev:true` for local) | CF Workers: `inngest/cloudflare` adapter + `inngest.setEnvVars(c.env)`
New v4: `step.ai.infer()` offloads inference to Inngest infra (zero compute during wait) | `step.realtime.publish()` durable pub/sub | `useRealtime()` React hook

## Clerk Core 3 (Breaking — Mar 2026)

`@clerk/clerk-react` → `@clerk/react` | `<Protect>/<SignedIn>/<SignedOut>` → unified `<Show>` | `getToken()` now throws `ClerkOfflineError` | ~50KB bundle reduction
CLI: `clerk init` (framework detect) | `clerk config` (auth settings) | `clerk api` (BAPI access)
API Keys GA: machine auth for programmatic access | SCIM GA: auto user provisioning from IdP

## Stripe (Versioned Releases)

Semiannual named releases + monthly additive updates. Pin via `stripe-version` header.
Billing Meter API v2 (GA): metered prices, token/API-call billing. Entitlements API (GA): feature gating via Products.

## Security Headers (OWASP Top 10:2025)

Must add: HSTS | X-Content-Type-Options | X-Frame-Options | Referrer-Policy | COOP | COEP | CORP | Permissions-Policy | CSP with Trusted Types
Must remove: X-XSS-Protection | Expect-CT | Server | X-Powered-By

## CSP Template

```
default-src 'self'; script-src 'self' 'nonce-{NONCE}' 'strict-dynamic';
style-src 'self' 'unsafe-inline' fonts.googleapis.com;
font-src 'self' fonts.gstatic.com;
img-src 'self' data:;
connect-src 'self';
report-uri /api/csp-report;
```

## Testing (TDD)

Failing test FIRST → implement → pass. Playwright 6 breakpoints: 375, 390, 768, 1024, 1280, 1920. axe-core 0 violations. No sleeps — `waitFor`/`toBeVisible()`. Selectors: `data-testid` > role > text. `PROD_URL` env var.

## Quality Bar

Lighthouse: a11y ≥95, perf ≥75 | WCAG 2.2 AA | LCP ≤2.5s, CLS ≤0.1, INP ≤200ms | JS ≤200KB gz, CSS ≤50KB gz | Functions ≤50 lines

## Deploy

```bash
npx wrangler deploy
curl -sX POST "https://api.cloudflare.com/client/v4/zones/${ZONE_ID}/purge_cache" \
  -H "Authorization: Bearer ${CF_API_TOKEN}" -H "Content-Type: application/json" \
  -d '{"purge_everything":true}'
```

## Bun 1.3 Native Clients

`Bun.sql` — unified tagged template API for PostgreSQL/MySQL/SQLite. Zero deps, auto SQL injection prevention.
`Bun.redis` — 7.9x faster than ioredis. 66 commands. Auto-reconnect.
`Bun.s3` — built-in S3 client with backpressure handling. Note: CF Workers use R2 binding directly.

## Common Failures

`binding not found` → add to wrangler.toml | Turnstile invalid-input → check TURNSTILE_SECRET | ERR_BLOCKED_BY_CSP → add domain to CSP | Playwright flaky → replace sleep with waitFor | D1 "no such table" → `wrangler d1 migrations apply` | Hono RPC type loss → use method chaining not separate controllers | CPU exceeded → split into `ctx.waitUntil()`

## Git Convention

`type(scope): description` — Types: feat | fix | refactor | test | docs | style | perf | ci | chore
