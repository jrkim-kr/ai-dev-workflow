# Environment Variables Convention

## File Structure

| File | Purpose | Committed? |
|------|---------|------------|
| `.env.example` | Documents all required env vars with placeholder values | ✅ Yes |
| `.env.local` | Local development overrides (secrets) | ❌ No |
| `.env.development` | Development defaults (non-secret) | ✅ Optional |
| `.env.production` | Production defaults (non-secret) | ✅ Optional |
| `.env.test` | Test environment defaults | ✅ Optional |

## .env.example Format

```bash
# =============================================================
# [Project Name] Environment Variables
# =============================================================
# Copy this file to .env.local and fill in the values
# cp .env.example .env.local
# =============================================================

# --- App ---
NEXT_PUBLIC_APP_URL=http://localhost:3000
NEXT_PUBLIC_APP_NAME=MyApp

# --- Database (Supabase) ---
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key

# --- Authentication ---
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=generate-with-openssl-rand-base64-32

# --- OAuth Providers (optional) ---
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=

# --- External APIs (optional) ---
# OPENAI_API_KEY=
# STRIPE_SECRET_KEY=
# STRIPE_WEBHOOK_SECRET=

# --- Email (optional) ---
# RESEND_API_KEY=
```

## Naming Rules

| Rule | Good | Bad |
|------|------|----|
| SCREAMING_SNAKE_CASE | `DATABASE_URL` | `databaseUrl` |
| Prefix `NEXT_PUBLIC_` for client-side | `NEXT_PUBLIC_APP_URL` | `APP_URL` (if used in browser) |
| Group by section with comments | `# --- Database ---` | Mixed without grouping |
| No quotes for simple values | `PORT=3000` | `PORT="3000"` |
| Quotes for values with spaces | `APP_NAME="My App"` | `APP_NAME=My App` |

## Security Rules

1. **Never commit actual secrets** to `.env.example` — use placeholder values
2. **Prefix `NEXT_PUBLIC_`** only for values safe to expose to the browser
3. **Server-only secrets** (API keys, DB passwords) must NOT have `NEXT_PUBLIC_` prefix
4. **Rotate secrets** if accidentally committed — treat them as compromised

## Common Variable Categories

### Required (App)
```bash
NEXT_PUBLIC_APP_URL          # App base URL
NEXT_PUBLIC_APP_NAME         # Display name
NODE_ENV                     # auto-set by Next.js
```

### Database
```bash
DATABASE_URL                 # Direct DB connection
NEXT_PUBLIC_SUPABASE_URL     # Supabase project URL
NEXT_PUBLIC_SUPABASE_ANON_KEY # Supabase anonymous key
SUPABASE_SERVICE_ROLE_KEY    # Supabase admin key (server only)
```

### Auth
```bash
NEXTAUTH_URL                 # NextAuth base URL
NEXTAUTH_SECRET              # NextAuth encryption key
```

### Third-party APIs
```bash
OPENAI_API_KEY               # OpenAI (server only)
STRIPE_SECRET_KEY            # Stripe (server only)
STRIPE_WEBHOOK_SECRET        # Stripe webhooks (server only)
RESEND_API_KEY               # Email service (server only)
```

## Validation

Use `zod` or similar to validate env vars at startup:

```typescript
// src/lib/env.ts
import { z } from 'zod'

const envSchema = z.object({
  NEXT_PUBLIC_APP_URL: z.string().url(),
  NEXT_PUBLIC_SUPABASE_URL: z.string().url(),
  NEXT_PUBLIC_SUPABASE_ANON_KEY: z.string().min(1),
  SUPABASE_SERVICE_ROLE_KEY: z.string().min(1),
})

export const env = envSchema.parse(process.env)
```
