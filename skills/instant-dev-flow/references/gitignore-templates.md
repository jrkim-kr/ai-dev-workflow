# .gitignore Templates

## Next.js Project (Default)

```gitignore
# dependencies
node_modules/
.pnp
.pnp.js
.pnp.cjs

# testing
coverage/

# next.js
.next/
out/

# production
build/
dist/

# misc
.DS_Store
*.pem
Thumbs.db

# debug
npm-debug.log*
yarn-debug.log*
yarn-error.log*
.pnpm-debug.log*

# env files
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# vercel
.vercel

# typescript
*.tsbuildinfo
next-env.d.ts

# IDE
.vscode/
.idea/
*.swp
*.swo
*~

# OS
.DS_Store
.DS_Store?
._*
.Spotlight-V100
.Trashes
ehthumbs.db
```

## Additional Rules by Context

### Supabase
```gitignore
# supabase
supabase/.temp/
supabase/.branches/
```

### Prisma
```gitignore
# prisma
prisma/generated/
```

### Turborepo / Monorepo
```gitignore
# turbo
.turbo/
```

### Python (if mixed project)
```gitignore
# python
__pycache__/
*.py[cod]
*$py.class
.venv/
venv/
*.egg-info/
```

## What to NEVER Commit

| File/Pattern | Reason |
|-------------|--------|
| `.env`, `.env.local` | Contains secrets |
| `node_modules/` | Reproducible from package.json |
| `.next/` | Build output |
| `*.pem`, `*.key` | Private keys |
| `credentials.json` | API credentials |
| `*.sqlite`, `*.db` | Local database files |
| `.DS_Store` | OS metadata |

## What to ALWAYS Commit

| File/Pattern | Reason |
|-------------|--------|
| `.env.example` | Documents required env vars |
| `package-lock.json` / `pnpm-lock.yaml` | Reproducible installs |
| `.gitignore` | Repo configuration |
| `docs/` | Project documentation |
| Config files (`next.config.*`, `tsconfig.json`) | Project setup |
