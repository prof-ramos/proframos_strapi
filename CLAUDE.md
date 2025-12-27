# CLAUDE.md - AI Assistant Guide

**Last Updated:** December 25, 2025
**Repository:** Strapi v5 + Next.js v15 Monorepo Starter
**Purpose:** Comprehensive guide for AI assistants working with this codebase

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Architecture & Tech Stack](#architecture--tech-stack)
3. [Repository Structure](#repository-structure)
4. [Development Workflows](#development-workflows)
5. [Key Conventions](#key-conventions)
6. [Critical Patterns](#critical-patterns)
7. [Common Tasks](#common-tasks)
8. [Gotchas & Important Notes](#gotchas--important-notes)
9. [File Paths Reference](#file-paths-reference)

---

## Project Overview

This is a **production-ready, enterprise-grade monorepo** that combines Strapi CMS with Next.js frontend. It's designed as a **page builder** for enterprise applications with full TypeScript support, internationalization, authentication, and SEO.

**Key Features:**
- Strapi v5 headless CMS with custom page builder
- Next.js v15 App Router with SSR/SSG
- Turborepo monorepo management
- Shared design system ensuring WYSIWYG consistency
- JWT authentication with NextAuth.js
- Multi-language support (i18n)
- Docker-ready for production deployment

---

## Architecture & Tech Stack

### Backend: Strapi v5
- **Location:** `apps/strapi/`
- **Database:** PostgreSQL 16 (Docker) or SQLite
- **Plugins:** i18n, SEO, Users & Permissions, CKEditor v5, Config Sync, Sentry
- **Key Features:** TypeScript, hierarchical pages, draft/publish workflow, API tokens

### Frontend: Next.js v15
- **Location:** `apps/ui/`
- **Framework:** React 18 with App Router
- **UI Library:** Shadcn/ui (Radix UI + Tailwind CSS)
- **Styling:** TailwindCSS v4 (CSS-based config)
- **State:** React Query, Context API
- **Auth:** NextAuth.js with JWT strategy
- **i18n:** next-intl

### Shared Packages
- **@repo/design-system** - Shared styles and design tokens
- **@repo/shared-data** - Common constants and utilities
- **@repo/eslint-config** - ESLint configurations
- **@repo/prettier-config** - Prettier configuration
- **@repo/typescript-config** - TypeScript configs

### Build Tools
- **Turborepo** - Monorepo orchestration
- **Yarn 1.22.x** - Package manager (Workspaces)
- **Node 22.x.x** - Runtime
- **Husky** - Git hooks
- **Commitizen** - Conventional commits

---

## Repository Structure

```plaintext
/home/user/proframos_strapi/
├── apps/
│   ├── strapi/              # Strapi v5 CMS Backend
│   │   ├── config/          # Strapi configuration
│   │   │   ├── admin.ts     # Admin panel config
│   │   │   ├── api.ts       # API settings
│   │   │   ├── database.ts  # Database connection
│   │   │   ├── middlewares.ts
│   │   │   ├── plugins.ts   # Plugin enable/config
│   │   │   ├── server.ts
│   │   │   └── sync/        # Config sync data (version controlled)
│   │   ├── src/
│   │   │   ├── api/         # Content types (collections/singles)
│   │   │   │   ├── page/    # Hierarchical pages
│   │   │   │   ├── navbar/  # Navigation (single type)
│   │   │   │   ├── footer/  # Footer (single type)
│   │   │   │   ├── subscriber/
│   │   │   │   ├── redirect/
│   │   │   │   └── internal-job/
│   │   │   ├── components/  # Reusable Strapi components
│   │   │   │   ├── sections/     # Page sections
│   │   │   │   ├── forms/        # Form components
│   │   │   │   ├── utilities/    # Utility components
│   │   │   │   └── seo-utilities/
│   │   │   ├── admin/       # Admin customizations
│   │   │   ├── lifeCycles/  # Lifecycle hooks
│   │   │   ├── documentMiddlewares/ # Document middleware
│   │   │   └── utils/       # Helper utilities
│   │   ├── types/generated/ # AUTO-GENERATED (do not edit!)
│   │   └── docker-compose.yml # Local PostgreSQL
│   │
│   └── ui/                  # Next.js v15 Frontend
│       ├── src/
│       │   ├── app/         # App Router
│       │   │   ├── [locale]/
│       │   │   │   ├── [[...rest]]/ # Page builder catch-all
│       │   │   │   └── auth/       # Auth pages
│       │   │   └── api/     # API routes (proxies, NextAuth)
│       │   ├── components/
│       │   │   ├── elementary/  # Standalone reusable components
│       │   │   ├── forms/
│       │   │   ├── page-builder/ # Strapi component renderers
│       │   │   │   ├── components/
│       │   │   │   ├── single-types/
│       │   │   │   └── index.tsx # ⚠️ COMPONENT MAPPING (critical!)
│       │   │   ├── providers/
│       │   │   ├── typography/
│       │   │   └── ui/      # Shadcn/ui (managed, do not rename!)
│       │   ├── hooks/       # Custom React hooks
│       │   ├── lib/
│       │   │   ├── strapi-api/  # API clients
│       │   │   │   ├── base.ts  # Base client
│       │   │   │   ├── public.ts
│       │   │   │   ├── private.ts
│       │   │   │   └── content/ # Content fetchers
│       │   │   ├── auth.ts      # NextAuth config
│       │   │   ├── i18n.ts      # i18n config
│       │   │   ├── navigation.ts # Localized navigation
│       │   │   └── styles.ts    # cn() utility
│       │   ├── styles/
│       │   │   └── globals.css  # Tailwind + theme imports
│       │   ├── types/
│       │   └── locales/     # Translation files
│       ├── middleware.ts    # Auth + i18n middleware
│       ├── next.config.mjs
│       ├── components.json  # Shadcn config
│       └── env.mjs          # Environment validation
│
├── packages/
│   ├── design-system/       # Shared styles (Tailwind theme)
│   ├── eslint-config/       # ESLint presets
│   ├── prettier-config/     # Prettier preset
│   ├── shared-data/         # Shared constants
│   └── typescript-config/   # TypeScript configs
│
├── scripts/
│   ├── heroku/              # Heroku deployment scripts
│   └── utils/               # Utility scripts
│
├── docs/                    # Documentation
├── .github/workflows/       # CI/CD
├── .husky/                  # Git hooks
├── turbo.json              # Turborepo config
└── package.json            # Root package.json
```

---

## Development Workflows

### Initial Setup

1. **Prerequisites:**

   ```bash
   node 22.x.x
   yarn 1.22.x
   Docker (for PostgreSQL)
   ```

2. **Install dependencies:**

   ```bash
   nvm use  # Switch to Node 22
   yarn     # Install all dependencies
   ```

   - The `postinstall` script runs `setup:apps` to copy `.example` files

3. **Start database:**

   ```bash
   cd apps/strapi
   docker compose up -d
   ```

4. **Start Strapi (first time):**

   ```bash
   yarn dev:strapi
   ```

   - Create admin user
   - Navigate to Settings > API Tokens
   - Create read-only token
   - Copy to `apps/ui/.env.local` as `STRAPI_REST_READONLY_API_KEY`

5. **Start all apps:**

   ```bash
   yarn dev  # Runs both Strapi and UI
   ```

### Daily Development

**Run both apps:**

```bash
yarn dev
```

**Run individual apps:**

```bash
yarn dev:strapi  # Strapi only (localhost:1337)
yarn dev:ui      # Next.js only (localhost:3000)
```

**Build commands:**

```bash
yarn build         # Build all apps
yarn build:strapi  # Build Strapi admin panel
yarn build:ui      # Build Next.js for production
```

**Code quality:**

```bash
yarn format        # Format all code with Prettier
yarn lint          # Run ESLint on all apps
yarn commit        # Interactive commit (Commitizen)
```

**Type generation:**

```bash
# In apps/strapi/
yarn generate:types  # Generate TypeScript types from schemas
```

**Database management:**

```bash
# In apps/strapi/
yarn run:db        # Start Docker database
yarn export:all    # Export all Strapi data
yarn import        # Import data
```

### Git Workflow

**Commit process:**

1. Stage files: `git add .`
2. Run: `yarn commit` (or `git commit`)
3. Husky runs:
   - **pre-commit:** Prettier formatting on staged files
   - **commit-msg:** Validates conventional commit format

**Conventional Commit Format:**

```plaintext
type(scope?): subject

Example:
feat(ui): add dark mode toggle
fix(strapi): resolve hierarchy calculation bug
docs: update CLAUDE.md with new patterns
```

**Types:** `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `perf`, `ci`, `build`, `revert`

---

## Key Conventions

### 1. TypeScript Patterns

#### Strapi Backend

**Generated Types (DO NOT EDIT MANUALLY):**

- Location: `apps/strapi/types/generated/`
- Auto-generated from schemas
- Regenerate: `yarn generate:types`

**Custom Types:**

```typescript
// types/internals.ts or individual type files
import type { UID } from "@strapi/strapi"

export interface CustomType {
  // ...
}
```

#### Next.js Frontend

**Import Strapi Types:**

```typescript
import type { Result, UID } from "@repo/strapi"

// Typed API response
const data: APIResponse<Result<"api::page.page", TParams>>
```

**Environment Variables:**

```typescript
// Validated via env.mjs
import { env } from "@/env.mjs"

const apiUrl = env.STRAPI_URL  // Type-safe
```

**Content Type UIDs:**

```typescript
"api::page.page"
"api::navbar.navbar"
"plugin::users-permissions.user"
```

### 2. Component Patterns

#### Naming Conventions

**Strapi-rendered components:** Prefix with `Strapi`

```typescript
// components/page-builder/components/sections/StrapiHero.tsx
export function StrapiHero({ component, pageParams, page }) { }
```

**Reusable UI components:** Descriptive names

```typescript
// components/elementary/AppLink.tsx
export function AppLink({ href, children }) { }
```

**Shadcn components:** Lowercase with hyphens (DO NOT RENAME)

```typescript
// components/ui/button.tsx
// components/ui/dropdown-menu.tsx
```

#### File Organization

**Page-specific components:**

```plaintext
app/[locale]/some-page/
  _components/
    SpecificComponent.tsx
  page.tsx
```

**Shared components:**

```plaintext
components/
  elementary/      # Standalone reusable
  forms/           # Form-related
  page-builder/    # Strapi renderers
  providers/       # Context providers
  typography/      # Text components
  ui/              # Shadcn (managed)
```

#### Component Composition

**Always use `cn()` for class names:**

```typescript
import { cn } from "@/lib/styles"  // NOT from utils!

export function Container({ className, children }: Props) {
  return (
    <div className={cn("container mx-auto px-4", className)}>
      {children}
    </div>
  )
}
```

**Error boundaries:**

```typescript
<ErrorBoundary customErrorTitle="Failed to load" showErrorMessage>
  <StrapiComponent component={component} />
</ErrorBoundary>
```

**Server vs Client components:**

```typescript
// Server component (default)
export async function ServerComponent() {
  const data = await fetchData()  // Can use async/await
  return <div>{data}</div>
}

// Client component (opt-in)
"use client"

import { useState } from "react"

export function ClientComponent() {
  const [state, setState] = useState()
  return <button onClick={() => setState(...)}>Click</button>
}
```

### 3. API Calling Conventions

#### Server Context (SSR, Server Components, Server Actions)

**Direct calls (no proxy):**

```typescript
import { PublicStrapiClient } from "@/lib/strapi-api"

// In Server Component or lib/strapi-api/content/server.ts
export async function fetchNavbar(locale: Locale) {
  return await PublicStrapiClient.fetchOne(
    "api::navbar.navbar",
    undefined,
    {
      locale,
      populate: { links: true, logoImage: { populate: { image: true } } }
    }
    // No useProxy flag - direct call
  )
}

// In page.tsx
const navbar = await fetchNavbar(locale)
```

#### Client Context (Client Components, React Hooks)

**Use proxy (required for security):**

```typescript
"use client"

import { PublicStrapiClient } from "@/lib/strapi-api"
import { useQuery } from "@tanstack/react-query"

export function ClientComponent() {
  const { data, isLoading } = useQuery({
    queryKey: ['pages', locale],
    queryFn: () => PublicStrapiClient.fetchMany(
      "api::page.page",
      { locale },
      undefined,
      { useProxy: true }  // ⚠️ REQUIRED in client context
    )
  })

  if (isLoading) return <Spinner />
  return <div>{data}</div>
}
```

#### API Client Methods

**BaseStrapiClient methods:**

```typescript
// Fetch single by ID
await client.fetchOne(uid, id, params)

// Fetch by slug
await client.fetchOneBySlug(uid, slug, params)

// Fetch by fullPath (for pages)
await client.fetchOneByFullPath(fullPath, params)

// Fetch many
await client.fetchMany(uid, filters, params, options)

// Fetch all (auto-pagination)
await client.fetchAll(uid, filters, params, options)
```

**Populate patterns:**

```typescript
// Basic populate
{ populate: "*" }

// Selective populate
{ populate: { links: true, image: true } }

// Nested populate
{
  populate: {
    logoImage: {
      populate: { image: true }
    }
  }
}

// Deep populate (for complex structures)
{
  populate: {
    content: {
      populate: {
        image: { populate: "*" },
        items: { populate: "*" }
      }
    }
  }
}
```

#### Endpoint Mapping

**Maintain API_ENDPOINTS in `lib/strapi-api/base.ts`:**

```typescript
export const API_ENDPOINTS: { [key in UID.ContentType]?: string } = {
  "api::page.page": "/pages",
  "api::navbar.navbar": "/navbar",
  "api::footer.footer": "/footer",
  "api::subscriber.subscriber": "/subscribers",
  // Add new content types here!
}
```

### 4. Styling Conventions

#### MUST Use cn() Utility

**Correct:**

```typescript
import { cn } from "@/lib/styles"

<div className={cn("base-classes", conditional && "variant", props.className)} />
```

**Incorrect:**

```typescript
// ❌ DO NOT DO THIS
<div className={`base-classes ${conditional ? 'variant' : ''} ${props.className}`} />
```

#### Tailwind CSS v4 Patterns

**Responsive design:**

```typescript
className="text-sm md:text-base lg:text-lg"
```

**Dark mode:**

```typescript
className="bg-white dark:bg-gray-900"
```

**Custom utilities (in globals.css):**

```css
@theme {
  --color-brand: #ff6b6b;
}

@layer utilities {
  .text-balance {
    text-wrap: balance;
  }
}
```

#### Component Variants (CVA)

```typescript
import { cva, type VariantProps } from "class-variance-authority"

const buttonVariants = cva(
  "inline-flex items-center justify-center rounded-md transition-colors",
  {
    variants: {
      variant: {
        default: "bg-primary text-primary-foreground hover:bg-primary/90",
        outline: "border border-input bg-background hover:bg-accent",
      },
      size: {
        default: "h-10 px-4 py-2",
        sm: "h-9 rounded-md px-3",
        lg: "h-11 rounded-md px-8",
      },
    },
    defaultVariants: {
      variant: "default",
      size: "default",
    },
  }
)

export interface ButtonProps extends VariantProps<typeof buttonVariants> {
  // ...
}
```

### 5. File Naming Patterns

| Type | Pattern | Example |
|------|---------|---------|
| React Components | PascalCase | `AppLink.tsx`, `ErrorBoundary.tsx` |
| Shadcn Components | kebab-case | `button.tsx`, `dropdown-menu.tsx` |
| Utilities | camelCase | `strapi-helpers.ts`, `general-helpers.ts` |
| Types | PascalCase/camelCase | `api.ts`, `general.ts` |
| Folders | kebab-case | `page-builder/`, `strapi-api/` |
| Page-specific folders | `_components/` | `app/auth/_components/` |
| Strapi folders | kebab-case | `page/`, `navbar/` |

---

## Critical Patterns

### 1. Page Builder Pattern

**How it works:**
1. Pages are defined in Strapi with a dynamic zone called `content`
2. Each component in `content` is rendered by the page builder
3. Component mapping is in `apps/ui/src/components/page-builder/index.tsx`

**Component Mapping (CRITICAL):**

```typescript
// apps/ui/src/components/page-builder/index.tsx

import { StrapiHero } from "./components/sections/StrapiHero"
import { StrapiCarousel } from "./components/sections/StrapiCarousel"
// ... other imports

export const componentsMap = {
  // Sections
  "sections.hero": StrapiHero,
  "sections.carousel": StrapiCarousel,
  // ... other mappings
}
```

**⚠️ IMPORTANT:** When adding a new Strapi component:

1. Create the component in Strapi
2. Create the React component in `apps/ui/src/components/page-builder/components/`
3. **MUST UPDATE** the mapping in `index.tsx`
4. Component receives: `component` (data), `pageParams` (route params), `page` (full page data)

**Example component:**

```typescript
// components/page-builder/components/sections/StrapiHero.tsx
import type { Strapi } from "@/types/strapi"

interface Props {
  component: Strapi.Component<"sections.hero">
  pageParams: { locale: string; rest?: string[] }
  page: Strapi.Page
}

export function StrapiHero({ component, pageParams, page }: Props) {
  return (
    <section>
      <h1>{component.title}</h1>
      <p>{component.description}</p>
    </section>
  )
}
```

### 2. Page Hierarchy & Redirects

**Hierarchical Pages:**

- Pages have parent/child relationships
- `fullPath` is auto-generated (e.g., `/parent/child/grandchild`)
- **DO NOT** edit `fullPath` manually
- Managed by lifecycle hooks in `apps/strapi/src/lifeCycles/`

**Workflow for URL Changes:**

When changing a page's slug or parent:

1. Change is saved → `fullPath` needs recalculation
2. System creates "internal jobs"
3. **Admin must manually trigger jobs:**
   - Go to Internal Jobs in Strapi admin
   - Run "Recalculate fullPath" job
   - Run "Create redirects" job
4. Redirects are auto-created from old URLs to new URLs

**Implementation Details:**

- Lifecycle hooks: `apps/strapi/src/lifeCycles/`
- Hierarchy utilities: `apps/strapi/src/utils/hierarchy/`
- Breadcrumb generation: `apps/strapi/src/utils/breadcrumbs.ts`
- Documentation: `docs/pages-hierarchy.md`

**⚠️ CRITICAL:** Always run jobs in this order:

1. Recalculate fullPath
2. Create redirects

### 3. Proxy Pattern (Security)

**Why proxies?**

- Hide API tokens from client
- Hide backend URL
- Prevent CORS issues
- Centralize API error handling

**Architecture:**

```plaintext
Client Component → Next.js API Route → Strapi
                   (with API token)
```

**Proxy Routes:**

- `/api/public-proxy/[...slug]` - Public data (read-only token)
- `/api/private-proxy/[...slug]` - User data (JWT token)
- `/api/asset/[...slug]` - Asset proxy for images

**Endpoint Whitelist:**

```typescript
// apps/ui/src/app/api/public-proxy/[...slug]/route.ts

const allowedEndpoints = [
  "/pages",
  "/navbar",
  "/footer",
  // Add new endpoints here!
]
```

**⚠️ SECURITY:** When adding a new content type:

1. Add endpoint to `allowedEndpoints` in proxy route
2. Update `API_ENDPOINTS` in `lib/strapi-api/base.ts`

**Server vs Client Context:**

| Context | Method | Token Source | Proxy? |
|---------|--------|--------------|--------|
| Server Component | Direct call | Server env var | No |
| Server Action | Direct call | Server env var | No |
| Client Component | useQuery | Hidden (proxy) | Yes |
| Client Hook | fetch | Hidden (proxy) | Yes |

### 4. Type Safety & Code Generation

**Strapi Type Generation:**

```bash
# In apps/strapi/
yarn generate:types
```

- Generates: `types/generated/contentTypes.d.ts` and `components.d.ts`
- **AUTO-GENERATED** - do not edit manually
- Triggered automatically on schema changes in dev mode
- Configuration: `config/typescript.ts`

**Frontend Type Imports:**

```typescript
// Import from Strapi package
import type { Result, UID } from "@repo/strapi"

// Use in API calls
const data: Result<"api::page.page", { populate: "*" }>
```

**Type-Safe API Calls:**

```typescript
// Client infers response type from content type UID
const page = await PublicStrapiClient.fetchOne(
  "api::page.page",  // ← TypeScript knows the shape
  id,
  { populate: "*" }
)

// `page` is fully typed!
page.data.attributes.title  // ✓ Type-safe
```

### 5. Middleware Population

**Problem:** Strapi's populate depth is limited, makes queries complex

**Solution:** Document middleware intercepts queries and applies custom population

**Implementation:**

```typescript
// apps/strapi/src/documentMiddlewares/page.ts

export default factory(({ strapi }) => ({
  beforeFindMany: async (params) => {
    // Check for trigger param
    if (!params.args?.middlewarePopulate) return params
    if (!params.args?.pagination) return params

    // Apply custom deep population
    params.args.populate = {
      content: {
        populate: { /* deep populate here */ }
      }
    }

    return params
  }
}))
```

**Usage:**

```typescript
// Trigger middleware with special params
const pages = await strapi.documents("api::page.page").findMany({
  middlewarePopulate: true,  // ⚠️ Required
  pagination: { limit: 10 },  // ⚠️ Required
})
```

**⚠️ MAINTENANCE:** Update middleware when adding new components to dynamic zones

### 6. Localization (i18n)

**Strapi:**

- Plugin: `@strapi/plugin-i18n`
- Enabled on: Pages, Navbar, Footer
- Locales configured in admin panel

**Next.js:**

- Package: `next-intl`
- Routing: `/[locale]/...`
- Supported locales: `lib/i18n.ts`

**Navigation (CRITICAL):**

```typescript
// ❌ DO NOT use next/navigation directly
import { Link } from "next/navigation"

// ✓ USE localized navigation
import { Link } from "@/lib/navigation"
import { useRouter } from "@/lib/navigation"
```

**Translation files:**

```plaintext
src/locales/
  en.json
  cs.json
  de.json
```

**Usage in components:**

```typescript
import { useTranslations } from "next-intl"

export function Component() {
  const t = useTranslations("namespace")
  return <p>{t("key")}</p>
}
```

**Server components:**

```typescript
import { getTranslations } from "next-intl/server"

export async function Component() {
  const t = await getTranslations("namespace")
  return <p>{t("key")}</p>
}
```

### 7. Authentication Flow

**Stack:** NextAuth.js + Strapi Users & Permissions

**Strategy:** JWT (30-day sessions)

**Providers:**

- Credentials (email/password)
- Google (optional, configured)
- Facebook (optional, configured)

**Protected Routes:**

```typescript
// middleware.ts
export default withAuth({
  pages: {
    signIn: "/auth/signin",
  },
})

export const config = {
  matcher: ["/protected/:path*"],
}
```

**Session Access:**

```typescript
// Server Component
import { getAuth } from "@/lib/auth"

export async function ServerComponent() {
  const session = await getAuth()
  if (!session) redirect("/auth/signin")
  return <div>Hello {session.user.email}</div>
}

// Client Component
"use client"
import { useSession } from "next-auth/react"

export function ClientComponent() {
  const { data: session, status } = useSession()
  if (status === "loading") return <Spinner />
  if (!session) return <div>Not authenticated</div>
  return <div>Hello {session.user.email}</div>
}
```

**API Routes:**

```typescript
// Server Action
import { getServerSession } from "next-auth"
import { authOptions } from "@/lib/auth"

export async function serverAction() {
  const session = await getServerSession(authOptions)
  if (!session) throw new Error("Unauthorized")
  // ...
}
```

### 8. Design System (WYSIWYG Consistency)

**Problem:** Tailwind v4 uses CSS-based config; Strapi can't process Tailwind directives

**Solution:** `@repo/design-system` package

**How it works:**

1. Define theme in `theme.css` with Tailwind directives
2. Build process compiles to standard CSS
3. Strapi imports compiled CSS
4. CKEditor configs (colors, fonts) exported as JSON
5. Both apps use identical styles

**Files:**

- `theme.css` - Source (with @theme, @layer)
- `styles.css` - Compiled output
- `styles-strapi.json` - Serialized for Strapi injection
- `ck-color-config.json` - CKEditor color palette
- `ck-fontSize-config.json` - CKEditor font sizes

**Usage in Strapi:**

```typescript
// apps/strapi/src/admin/app.tsx
import styles from "@repo/design-system/styles-strapi.json"

export default {
  config: {
    head: ({ strapi }) => ({
      style: [{ tagName: "style", innerHTML: styles.css }],
    }),
  },
}
```

**⚠️ IMPORTANT:** After changing design tokens:

```bash
cd packages/design-system
yarn build  # Rebuild CSS and JSON exports
```

### 9. Environment Management

**Strapi (.env):**

```bash
# Required
HOST=0.0.0.0
PORT=1337
APP_KEYS=...
API_TOKEN_SALT=...
ADMIN_JWT_SECRET=...
JWT_SECRET=...

# Database
DATABASE_CLIENT=postgres
DATABASE_URL=postgresql://user:pass@host:5432/db

# Optional
AWS_ACCESS_KEY_ID=...
MAILGUN_API_KEY=...
SENTRY_DSN=...

# Preview
CLIENT_URL=http://localhost:3000
STRAPI_PREVIEW_ENABLED=true
STRAPI_PREVIEW_SECRET=shared-secret
```

**Next.js (.env.local):**

```bash
# Required
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=...
STRAPI_URL=http://127.0.0.1:1337
STRAPI_REST_READONLY_API_KEY=...
APP_PUBLIC_URL=http://localhost:3000

# Optional
NEXT_PUBLIC_RECAPTCHA_SITE_KEY=...
RECAPTCHA_SECRET_KEY=...
NEXT_PUBLIC_SENTRY_DSN=...
STRAPI_PREVIEW_SECRET=shared-secret
```

**Validation:**

```typescript
// apps/ui/env.mjs
import { createEnv } from "@t3-oss/env-nextjs"

export const env = createEnv({
  server: {
    STRAPI_URL: z.string().url(),
    STRAPI_REST_READONLY_API_KEY: z.string().min(1),
    // ...
  },
  client: {
    NEXT_PUBLIC_RECAPTCHA_SITE_KEY: z.string().optional(),
    // ...
  },
  runtimeEnv: {
    STRAPI_URL: process.env.STRAPI_URL,
    // ...
  },
})
```

**⚠️ Turbo Requirement:**

Add new env vars to `turbo.json` > `globalEnv`:

```json
{
  "globalEnv": [
    "STRAPI_URL",
    "NEW_ENV_VAR"  // Add here!
  ]
}
```

### 10. Monorepo Workflow (Turborepo)

**Task Pipeline:**

```plaintext
build: dependencies^build → app:build
dev: shared-data#build, design-system#build → app:dev
```

**Key Commands:**

```bash
# Development
yarn dev              # All apps
yarn dev:ui           # Only UI
yarn dev:strapi       # Only Strapi

# Build
yarn build            # All apps (respects dependencies)
yarn build:ui         # Only UI (still builds deps)
yarn build:strapi     # Only Strapi

# Caching
rm -rf .turbo         # Clear Turbo cache
```

**Build Order:**

1. Shared packages: `shared-data`, `design-system`
2. Apps: `strapi`, `ui` (parallel)

**Cache:**

- Location: `.turbo/`
- Speeds up rebuilds
- Cleared on `yarn clean` or manually

**Filters:**

```bash
# Run command in specific workspace
turbo build --filter=@repo/ui
turbo dev --filter=@repo/strapi

# Include dependencies
turbo build --filter=...@repo/ui  # ui + its deps
```

---

## Common Tasks

### Add a New Strapi Component

1. **Create component in Strapi admin:**

   - Content-Type Builder > Create Component
   - Add fields
   - Save

2. **Generate types:**

   ```bash
   cd apps/strapi
   yarn generate:types
   ```

3. **Create React component:**

   ```bash
   # apps/ui/src/components/page-builder/components/sections/StrapiNewComponent.tsx
   ```

   ```typescript
   import type { Strapi } from "@/types/strapi"

   interface Props {
     component: Strapi.Component<"sections.new-component">
     pageParams: { locale: string }
     page: Strapi.Page
   }

   export function StrapiNewComponent({ component }: Props) {
     return (
       <section>
         {/* Render component */}
       </section>
     )
   }
   ```

4. **⚠️ CRITICAL: Update component mapping:**

   ```typescript
   // apps/ui/src/components/page-builder/index.tsx
   import { StrapiNewComponent } from "./components/sections/StrapiNewComponent"

   export const componentsMap = {
     // ... existing
     "sections.new-component": StrapiNewComponent,
   }
   ```

5. **Update middleware population (if needed):**

   ```typescript
   // apps/strapi/src/documentMiddlewares/page.ts
   // Add deep population for new component
   ```

### Add a New Content Type

1. **Create in Strapi admin:**

   - Content-Type Builder > Create Collection/Single Type
   - Configure fields
   - Save

2. **Generate types:**

   ```bash
   cd apps/strapi
   yarn generate:types
   ```

3. **Add API endpoint:**

   ```typescript
   // apps/ui/src/lib/strapi-api/base.ts
   export const API_ENDPOINTS = {
     // ... existing
     "api::new-type.new-type": "/new-types",
   }
   ```

4. **Add to proxy whitelist:**

   ```typescript
   // apps/ui/src/app/api/public-proxy/[...slug]/route.ts
   const allowedEndpoints = [
     // ... existing
     "/new-types",
   ]
   ```

5. **Create content fetcher (optional):**

   ```typescript
   // apps/ui/src/lib/strapi-api/content/server.ts
   export async function fetchNewType(id: string) {
     return await PublicStrapiClient.fetchOne(
       "api::new-type.new-type",
       id,
       { populate: "*" }
     )
   }
   ```

### Add a New Shadcn Component

```bash
cd apps/ui
npx shadcn-ui@latest add [component-name]

# Example:
npx shadcn-ui@latest add tabs
```

**⚠️ DO NOT:**

- Rename the `components/ui/` folder
- Rename individual component files
- Manually edit Shadcn components (they may be overwritten)

**✓ DO:**

- Extend components in separate files
- Use composition to customize

### Add a New Environment Variable

1. **Add to .env files:**

   ```bash
   # apps/strapi/.env
   NEW_STRAPI_VAR=value

   # apps/ui/.env.local
   NEW_UI_VAR=value
   ```

2. **Add to validation (UI only):**

   ```typescript
   // apps/ui/env.mjs
   export const env = createEnv({
     server: {
       NEW_UI_VAR: z.string(),
     },
     runtimeEnv: {
       NEW_UI_VAR: process.env.NEW_UI_VAR,
     },
   })
   ```

3. **Add to Turbo config:**

   ```json
   // turbo.json
   {
     "globalEnv": [
       "NEW_UI_VAR"
     ]
   }
   ```

4. **Update .env.example files:**

   ```bash
   # apps/strapi/.env.example
   NEW_STRAPI_VAR=

   # apps/ui/.env.local.example
   NEW_UI_VAR=
   ```

### Add a New Translation

1. **Add key to locale files:**

   ```json
   // apps/ui/src/locales/en.json
   {
     "common": {
       "newKey": "New text"
     }
   }

   // apps/ui/src/locales/cs.json
   {
     "common": {
       "newKey": "Nový text"
     }
   }
   ```

2. **Use in component:**

   ```typescript
   import { useTranslations } from "next-intl"

   export function Component() {
     const t = useTranslations("common")
     return <p>{t("newKey")}</p>
   }
   ```

### Run Database Migrations

**Strapi auto-migration:**

- Schema changes are auto-migrated in development
- No manual migration files needed

**Production deployment:**

1. Build Strapi: `yarn build:strapi`
2. Start Strapi: `yarn start:strapi`
3. Strapi auto-migrates database on first run

**Manual database management:**

```bash
# Export all data
cd apps/strapi
yarn export:all

# Import data
yarn import

# Reset database (DEV ONLY!)
# Delete database and restart Strapi
```

### Deploy to Production

**Docker (recommended):**

```bash
# Build Strapi
docker build -t strapi:latest \
  -f apps/strapi/Dockerfile \
  --build-arg APP_URL=https://api.domain.com \
  .

# Build UI
docker build -t ui:latest \
  -f apps/ui/Dockerfile \
  .

# Run with docker-compose or Kubernetes
```

**Heroku:**

```bash
# Configure environment variables in Heroku dashboard
# Push to Heroku git remote
git push heroku main

# Heroku detects root package.json
# Runs: yarn heroku-postbuild (builds appropriate app)
```

**Vercel (UI only):**

- Root directory: `apps/ui`
- Build command: `cd ../.. && yarn build:ui`
- Output directory: `apps/ui/.next`
- Environment variables: Add in Vercel dashboard

---

## Gotchas & Important Notes

### ⚠️ CRITICAL GOTCHAS

1. **Component Mapping Must Be Manual**
   - Adding a Strapi component doesn't auto-update the mapping
   - **MUST** update `apps/ui/src/components/page-builder/index.tsx`
   - Missing mapping = component won't render (fails silently)

2. **DO NOT Edit Generated Files**
   - `apps/strapi/types/generated/*` - Auto-generated
   - `apps/ui/components/ui/*` - Shadcn managed
   - Edits will be overwritten

3. **Proxy Flag Required in Client Context**
   - Client components **MUST** use `{ useProxy: true }`
   - Server components **MUST NOT** use proxy
   - Wrong context = CORS errors or exposed tokens

4. **Localized Navigation Only**
   - **DO NOT** import from `next/navigation`
   - **ALWAYS** import from `@/lib/navigation`
   - Wrong import = broken locale switching

5. **cn() from lib/styles, Not utils**
   - Import path: `@/lib/styles`
   - NOT from `@/lib/utils` or anywhere else
   - Wrong import = runtime errors

6. **Page Hierarchy Jobs Must Run in Order**
   - First: Recalculate fullPath
   - Second: Create redirects
   - Wrong order = broken links

7. **Turbo globalEnv Must Include All Env Vars**
   - Missing from `globalEnv` = cache invalidation issues
   - Add new vars to `turbo.json`

8. **Shared Packages Must Build Before Apps**
   - `design-system` and `shared-data` must build first
   - Turbo handles this automatically
   - Manual builds: build packages first

### 🔒 Security Notes

1. **API Tokens:**
   - Never commit `.env` files
   - Use read-only tokens for public data
   - Use JWT tokens for user data
   - Rotate tokens regularly

2. **Proxy Whitelist:**
   - Only allowed endpoints are accessible
   - Add new endpoints to whitelist explicitly
   - Review whitelist periodically

3. **Strapi Admin:**
   - Use strong passwords
   - Enable 2FA in production
   - Limit API token permissions

4. **Environment Variables:**
   - Validate all env vars (use env.mjs in UI)
   - Never expose secrets to client
   - Use `NEXT_PUBLIC_` prefix only when necessary

### 🐛 Common Issues

#### "Component not found" in page builder

- **Cause:** Missing component mapping
- **Fix:** Add to `apps/ui/src/components/page-builder/index.tsx`

#### CORS errors in client components

- **Cause:** Missing `{ useProxy: true }` flag
- **Fix:** Add flag to API client call

#### Types not updating after Strapi schema change

- **Cause:** Types not regenerated
- **Fix:** `cd apps/strapi && yarn generate:types`

#### Turbo cache serving stale builds

- **Cause:** Env var not in `globalEnv`
- **Fix:** Add to `turbo.json` > `globalEnv`, then `rm -rf .turbo`

#### Locale switching breaks

- **Cause:** Using `next/navigation` instead of `@/lib/navigation`
- **Fix:** Import from correct location

#### Styles mismatch between Strapi CKEditor and frontend

- **Cause:** Design system not rebuilt
- **Fix:** `cd packages/design-system && yarn build`

#### "Module not found" for shared packages

- **Cause:** Shared packages not built
- **Fix:** `yarn build` (or build specific package)

### 📝 Best Practices

1. **Type Safety:**

   - Always use TypeScript
   - Leverage generated types
   - Avoid `any`

2. **Component Structure:**

   - Keep components small and focused
   - Use composition over inheritance
   - Server components by default, client when needed

3. **API Calls:**

   - Centralize in `lib/strapi-api/content/`
   - Use React Query for client-side data
   - Enable ISR for static pages

4. **Styling:**

   - Use design tokens from theme
   - Always use `cn()` for dynamic classes
   - Prefer Tailwind utilities over custom CSS

5. **Git Commits:**

   - Use `yarn commit` for conventional commits
   - Write clear, descriptive messages
   - Keep commits atomic

6. **Error Handling:**

   - Use ErrorBoundary for component errors
   - Handle API errors gracefully
   - Log errors to Sentry in production

7. **Performance:**

   - Use Server Components for static content
   - Lazy load client components
   - Optimize images (next/image)
   - Enable ISR for semi-static pages

---

## File Paths Reference

### Frequently Modified Files

**Component Mapping (CRITICAL):**

```plaintext
apps/ui/src/components/page-builder/index.tsx
```

**API Endpoints:**

```plaintext
apps/ui/src/lib/strapi-api/base.ts (API_ENDPOINTS)
apps/ui/src/app/api/public-proxy/[...slug]/route.ts (allowedEndpoints)
apps/ui/src/app/api/private-proxy/[...slug]/route.ts (allowedEndpoints)
```

**Environment Files:**

```plaintext
apps/strapi/.env
apps/strapi/.env.example
apps/ui/.env.local
apps/ui/.env.local.example
```

**Configuration:**

```plaintext
turbo.json
package.json
apps/strapi/config/plugins.ts
apps/ui/next.config.mjs
apps/ui/middleware.ts
```

**Types:**

```plaintext
apps/strapi/types/generated/contentTypes.d.ts (AUTO-GENERATED)
apps/strapi/types/generated/components.d.ts (AUTO-GENERATED)
apps/ui/src/types/
```

**Styling:**

```plaintext
packages/design-system/theme.css
apps/ui/src/styles/globals.css
apps/ui/src/lib/styles.ts (cn utility)
```

**Localization:**

```plaintext
apps/ui/src/locales/en.json
apps/ui/src/locales/cs.json
apps/ui/src/lib/i18n.ts
apps/ui/src/lib/navigation.ts
```

**Authentication:**

```plaintext
apps/ui/src/lib/auth.ts
apps/ui/src/app/api/auth/[...nextauth]/route.ts
apps/ui/middleware.ts
```

### Strapi Structure

**Content Types:**

```plaintext
apps/strapi/src/api/
  page/
    content-types/page/schema.json
    controllers/page.ts
    services/page.ts
    routes/page.ts
  navbar/
  footer/
  subscriber/
  redirect/
  internal-job/
```

**Components:**

```plaintext
apps/strapi/src/components/
  sections/
  forms/
  utilities/
  seo-utilities/
  elements/
```

**Configuration:**

```plaintext
apps/strapi/config/
  admin.ts
  api.ts
  database.ts
  middlewares.ts
  plugins.ts
  server.ts
  typescript.ts
  cron-tasks.ts
  env/production/
```

**Lifecycle Hooks:**

```plaintext
apps/strapi/src/lifeCycles/
  adminUser.ts
  user.ts
```

**Document Middleware:**

```plaintext
apps/strapi/src/documentMiddlewares/
  page.ts
```

**Utilities:**

```plaintext
apps/strapi/src/utils/
  breadcrumbs.ts
  hierarchy/
```

### Next.js Structure

**App Router:**

```plaintext
apps/ui/src/app/
  [locale]/
    [[...rest]]/page.tsx  # Page builder
    auth/                 # Auth pages
    client-page/          # Example page
  api/
    auth/[...nextauth]/route.ts
    public-proxy/[...slug]/route.ts
    private-proxy/[...slug]/route.ts
    preview/route.ts
    asset/[...slug]/route.ts
  layout.tsx
```

**Components:**

```plaintext
apps/ui/src/components/
  elementary/        # Reusable components
  forms/             # Form components
  page-builder/      # Strapi renderers
    components/
      sections/
      forms/
      utilities/
    single-types/
      navbar/
      footer/
    index.tsx        # ⚠️ COMPONENT MAPPING
  providers/         # Context providers
  typography/        # Text components
  ui/                # Shadcn (managed)
```

**API Clients:**

```plaintext
apps/ui/src/lib/strapi-api/
  base.ts            # Base client + API_ENDPOINTS
  public.ts          # Public client
  private.ts         # Private client
  request-auth.ts    # Auth helpers
  content/
    server.ts        # Server-side fetchers
```

**Utilities:**

```plaintext
apps/ui/src/lib/
  auth.ts            # NextAuth config
  i18n.ts            # i18n config
  navigation.ts      # Localized navigation
  styles.ts          # cn() utility
  metadata/          # SEO helpers
```

---

## Quick Reference

### Commands Cheat Sheet

```bash
# Development
yarn dev              # All apps
yarn dev:strapi       # Strapi only
yarn dev:ui           # Next.js only

# Build
yarn build            # All apps
yarn build:strapi     # Strapi only
yarn build:ui         # Next.js only

# Code Quality
yarn format           # Format all
yarn lint             # Lint all
yarn commit           # Interactive commit

# Strapi Specific
cd apps/strapi
yarn generate:types   # Generate TypeScript types
yarn run:db           # Start database
yarn export:all       # Export data
yarn import           # Import data

# UI Specific
cd apps/ui
npx shadcn-ui@latest add [component]  # Add Shadcn component
yarn typecheck        # TypeScript check

# Utilities
bash ./scripts/utils/rm-modules.sh  # Remove node_modules
bash ./scripts/utils/rm-all.sh      # Remove all build artifacts
```

### Import Paths Cheat Sheet

```typescript
// ✓ CORRECT
import { cn } from "@/lib/styles"
import { Link } from "@/lib/navigation"
import type { Result } from "@repo/strapi"
import { PublicStrapiClient } from "@/lib/strapi-api"

// ❌ INCORRECT
import { cn } from "@/lib/utils"  // Wrong path
import { Link } from "next/navigation"  // Breaks i18n
import { StrapiClient } from "@/lib/strapi"  // Doesn't exist
```

### TypeScript Patterns Cheat Sheet

```typescript
// Content Type UID
type PageUID = "api::page.page"

// API Response
const data: Result<"api::page.page", { populate: "*" }>

// Component Type
import type { Strapi } from "@/types/strapi"
type HeroComponent = Strapi.Component<"sections.hero">

// Populate Params
type PopulateParams = {
  populate: {
    content: { populate: "*" }
  }
}
```

---

## Conclusion

This guide covers the essential patterns, conventions, and workflows for working with this Strapi + Next.js monorepo. Key takeaways:

1. **Always update component mappings** when adding Strapi components
2. **Use the correct API client context** (server vs client, proxy vs direct)
3. **Maintain type safety** by regenerating types after schema changes
4. **Follow naming and styling conventions** consistently
5. **Respect the page hierarchy workflow** for URL changes
6. **Keep security in mind** with API tokens and proxy whitelists
7. **Leverage Turborepo** for efficient monorepo management

For more detailed documentation, see:
- `apps/strapi/README.md` - Strapi-specific docs
- `apps/ui/README.md` - Next.js-specific docs
- `docs/` - Additional documentation
- Individual package README files

**Remember:** This is a living document. Update it as patterns evolve and new conventions are established.
