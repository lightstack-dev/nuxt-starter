# nuxt-starter Vision

> Part of the [Lightstack](https://github.com/lightstack-dev) ecosystem - development accelerators for Nuxt

## Mission Statement

**Skip the boilerplate. Start innovating.**

This starter provides a complete development-to-production stack with Docker Compose simplicity. Never rebuild auth, deployment, or infrastructure setup again.

## Project Architecture

This starter works alongside other Lightstack tools:

### 1. Lightstack CLI (Companion Tool)
- **Repository**: `lightstack-dev/cli`
- **Purpose**: Development and deployment orchestrator
- **Scope**: Works with any BaaS project, first-class Nuxt support
- **Installation**: `npm install -g @lightstack/cli`

### 2. This Starter (nuxt-starter)
- **Repository**: `lightstack-dev/nuxt-starter`
- **Purpose**: Complete Nuxt stack with auth and deployment solved
- **Stack**: Nuxt + Supabase + Docker Compose + Traefik
- **Philosophy**: Opinionated defaults with escape hatches

## Core Design Principles

### 1. Progressive Disclosure of Complexity
- **Level 1**: Just works with `npm run dev`
- **Level 2**: Customizable with flags
- **Level 3**: Full access to underlying tools

### 2. Pass-Through Architecture
```javascript
// Lightstack CLI only augments specific commands
const augmentedCommands = {
  'dev': lightStackDev,      // Our orchestration
  'deploy': lightStackDeploy,  // Our automation
  'init': lightStackInit,      // Our setup
};
// Everything else passes through to Supabase CLI
```

### 3. Best DX as Primary Goal
- Single command to start everything
- SSL/FQDN in local development for production parity
- Zero-config deployment to staging/production

## Technical Architecture

### Local Development Enhancement
```yaml
# Production parity with:
- mkcert for SSL certificates
- Traefik as reverse proxy (optional, for full parity)
- *.local.lightstack.dev → 127.0.0.1 via hosts file
- Orchestrated startup (Supabase + Nuxt)
- Automatic port conflict resolution
- Database seeding with test data
```

### Self-Hosted Deployment Architecture
```yaml
staging/production:
  infrastructure:
    - Single VPS or multi-server setup
    - Docker Compose orchestration
    - Traefik with automatic SSL (Let's Encrypt + DNS challenge)
    - Automated secret generation and rotation
    - SSH-based deployment (no agents needed)

  features:
    - Zero-downtime deployments
    - Automated backups to S3/local
    - Health checks and auto-recovery
    - Resource monitoring (optional)
```

### Cloud Deployment Options
```yaml
supabase-cloud:
  - Uses native Supabase CLI
  - Lightstack adds deployment workflows
  - Environment management
```

### Configuration Structure
```
project/
├── .lightstack/
│   ├── config.yml          # Deployment config (committed)
│   └── .env.production     # Secrets (gitignored)
├── supabase/               # Standard Supabase structure
└── nuxt.config.ts          # Standard Nuxt structure
```

## Extensibility Strategy

### Plugin/Adapter System
```typescript
// Future extensibility
interface BackendAdapter {
  start(): Promise<void>
  deploy(target: string): Promise<void>
  generateSecrets(): SecretMap
}

// Implementations
class SupabaseAdapter implements BackendAdapter { }
class PocketBaseAdapter implements BackendAdapter { }  // Future
class AppwriteAdapter implements BackendAdapter { }    // Future
```

### Backend Support Roadmap
1. **v1.0**: Supabase (full support)
2. **v2.0**: PocketBase (lightweight alternative)
3. **Future**: Appwrite, custom backends

## Deployment Workflow

### Developer Journey
```bash
# Day 0: Setup (< 5 minutes)
npx create-lightstack-app my-app
# OR for existing Supabase projects:
cd existing-project && lightstack init

# Day 1: Development
npm run dev
# ✅ Supabase starts automatically if not running
# ✅ Database migrations run
# ✅ Test data seeded
# ✅ Nuxt starts with HMR
# Ready at: https://app.local.lightstack.dev

# Day 2: Deployment
lightstack deploy init production  # One-time setup wizard
lightstack deploy production        # Deploy in seconds
```

### Production Deployment (Hetzner VPS Example)
```bash
# One-time setup
lightstack deploy init production
# Interactive wizard configures everything

# Subsequent deploys
lightstack deploy production
# Handles: Docker, Traefik, SSL, secrets, health checks
```

## Value Propositions

### For Lightstack CLI
> "The missing deployment tool for Supabase self-hosting"
- Works with ANY Supabase project (not just Nuxt)
- Bridges the gap between `supabase start` and production
- One command to deploy to any VPS (Hetzner, DigitalOcean, etc.)
- Production-ready with SSL, backups, monitoring
- CI/CD friendly (GitHub Actions, GitLab CI, etc.)

### For nuxt-starter
> "Complete stack. Zero setup. Ship today."
- Full authentication flows (Supabase or Logto)
- Docker Compose for development and production
- Deploy to any VPS with one command
- TypeScript-first with full type safety
- Lightstack ecosystem integration

## Implementation Priorities

### Phase 1: Foundation
- [x] Design CLI architecture with pass-through pattern
- [ ] Create minimal Nuxt starter template
- [ ] Implement basic `lightstack dev` command
- [ ] Integrate nuxt-final-auth module

### Phase 2: Local Development Excellence
- [ ] Add SSL/FQDN support for local development
- [ ] Implement development orchestration
- [ ] Add seed data management
- [ ] Create development environment reset capabilities

### Phase 3: Production Deployment
- [ ] Build deployment automation for self-hosted
- [ ] Add Traefik configuration with SSL
- [ ] Implement secret management
- [ ] Create backup/restore capabilities

### Phase 4: Polish & Expand
- [ ] Add PocketBase adapter (lightweight alternative)
- [ ] Create additional framework starters (Next, SvelteKit, etc.)
- [ ] Implement blue-green deployments
- [ ] Add monitoring and observability

## Success Metrics

1. **Time to First Feature**: < 5 minutes from project creation to writing business logic
2. **Deployment Simplicity**: Single command to deploy production
3. **Zero Auth Code**: Developers never write authentication logic
4. **Framework Agnostic**: CLI works with any framework

## Technical Decisions

- **Supabase First**: Start with Supabase due to best Nuxt support and enterprise readiness
- **Module Architecture**: Design for extensibility from day one
- **Standard Tooling**: Use Docker, Traefik, Let's Encrypt (no proprietary lock-in)
- **Progressive Enhancement**: Only augment where it adds value

## Key Differentiators

### Why Not Just Use Supabase CLI?
- **Local Development**: Supabase CLI doesn't handle SSL/FQDN for production parity
- **Self-Hosting**: No built-in deployment to VPS, no Traefik setup, no automated SSL
- **Orchestration**: Doesn't coordinate multiple services (Nuxt + Supabase + Redis + etc.)
- **Secrets Management**: Manual secret generation and management

### Why Not Just Use Docker Compose?
- **Complexity**: Requires deep Docker knowledge, manual Traefik configuration
- **No Automation**: Manual SSL setup, secret generation, health checks
- **No Standardization**: Every project reinvents deployment

### Why This Starter?
- **Complete Stack**: Everything from auth to deployment
- **Docker Everywhere**: Same stack in dev, staging, and production
- **VPS Ready**: Deploy to Hetzner, DigitalOcean, or any VPS
- **Lightstack Integration**: Works seamlessly with our CLI and modules

## Risk Mitigation

- **Maintenance Burden**: Pass-through pattern minimizes surface area
- **Supabase Changes**: Only augment, never replace core functionality
- **Adoption**: Start with Nuxt community (proven need), expand from there
- **Competition**: Focus on self-hosting niche that Supabase doesn't prioritize

## Implementation Starting Points

1. Create Lightstack CLI repository:
   - `lightstack-dev/cli` - Development and deployment orchestrator

2. Begin with Lightstack CLI:
   - Implement pass-through to Supabase CLI
   - Create `lightstack dev` command
   - Test with existing Supabase project

3. Then create Nuxt starter:
   - Standard Nuxt structure
   - Integrate Supabase module
   - Add nuxt-final-auth

Remember: **The goal is to eliminate repetitive setup work so developers can focus on building business value from minute one.**