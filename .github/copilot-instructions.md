# Scira AI Search Engine

Scira is a Next.js TypeScript application that provides AI-powered search capabilities with multiple AI providers, web search, academic research, entertainment data, financial tools, and location services.

**ALWAYS reference these instructions first and fallback to search or bash commands only when you encounter unexpected information that does not match the info here.**

## Working Effectively

### Bootstrap and Setup
- Install dependencies:
  - `npm install -g pnpm` (if pnpm not available)
  - `pnpm install` -- NETWORK LIMITATION: fails due to inaccessible dependency at pkg.pr.new. **NEVER CANCEL**. Takes 1-2 minutes normally but will fail in restricted networks.
  - Alternative: Temporarily modify package.json to replace `"better-auth": "https://pkg.pr.new/better-auth/better-auth@8e825ad"` with `"better-auth": "^1.3.7"` then run `pnpm install --no-frozen-lockfile`. This workaround allows development to proceed.

### Environment Setup
- Copy `.env.example` to `.env.local` and configure API keys
- **CRITICAL**: 38+ environment variables are required for build to succeed. See .env.example for complete list.
- **Essential APIs**: XAI_API_KEY, OPENAI_API_KEY, ANTHROPIC_API_KEY, GROQ_API_KEY, GOOGLE_GENERATIVE_AI_API_KEY, DATABASE_URL, BETTER_AUTH_SECRET
- For testing only: Create `.env.local` with dummy values for all required variables to enable builds

### Build Process
- `pnpm run build` -- **NETWORK LIMITATION**: fails due to Google Fonts access (fonts.googleapis.com blocked). **NEVER CANCEL**. Normally takes 45-90 seconds but fails in restricted networks.
- Alternative: Use `npx next build` (same limitation applies)
- **TIMEOUT RECOMMENDATION**: Set timeout to 120+ seconds minimum, expect failure in restricted networks
- Build requires all environment variables to be properly configured

### Development Server
- `pnpm dev` -- starts successfully in 1-2 seconds. **NEVER CANCEL**.
- Runs on `http://localhost:3000`
- Server may return 500 errors without proper API keys and database configuration
- **TIMEOUT RECOMMENDATION**: Set timeout to 30+ seconds for startup

### Code Quality
- `pnpm run fix` -- runs Prettier formatting, takes 5-10 seconds. **Works reliably**.
- `pnpm run lint` -- **FAILS** due to ESLint v9 configuration mismatch. Do not rely on this command.
- **ALWAYS run `pnpm run fix` before committing changes**

### Docker Deployment
- **NETWORK LIMITATION**: Docker builds fail due to blocked Alpine package repositories (dl-cdn.alpinelinux.org)
- Build command: `docker build -t scira.app .`
- **TIMEOUT RECOMMENDATION**: Set timeout to 180+ minutes for Docker builds, expect failure in restricted networks
- Requires `.env` file (not `.env.local`) in repository root for build context
- Alternative: Use Docker Compose: `docker compose up`

## Key Architecture Components

### Application Structure
- **app/**: Next.js 15 app router structure with route groups
  - `(auth)/`: Authentication pages (sign-in, sign-up)
  - `(search)/`: Main search interface
  - `api/`: API routes for AI integration, auth, and webhooks
  - `lookout/`: Automated research scheduling feature
- **lib/**: Core business logic
  - `tools/`: AI tools for search, analysis, charts, weather, etc.
  - `db/`: Database schema and queries (Drizzle ORM with PostgreSQL)
  - `auth.ts`: Better Auth configuration
- **components/**: React components organized by feature
  - `ui/`: Reusable UI components (Shadcn/UI)
  - Feature-specific components for search, charts, maps, etc.

### Technology Stack
- **Frontend**: Next.js 15, React 19, TypeScript, Tailwind CSS
- **AI/ML**: Vercel AI SDK with multiple providers (xAI, OpenAI, Anthropic, Google, Groq)
- **Database**: PostgreSQL with Drizzle ORM
- **Authentication**: Better Auth with multiple OAuth providers
- **Search**: Exa AI, Tavily, Firecrawl for web content
- **Additional APIs**: Google Maps, OpenWeather, TMDB, Aviation Stack, Mem0

### Important File Locations
- **Main layout**: `app/layout.tsx`
- **Search interface**: `app/(search)/page.tsx`
- **Chat component**: `components/chat-interface.tsx`
- **API actions**: `app/actions.ts`
- **Database schema**: `lib/db/schema.ts`
- **Tool definitions**: `lib/tools/` directory
- **Environment config**: `env/server.ts` and `env/client.ts`

## Validation Scenarios

### Manual Testing Requirements
**CRITICAL**: After making changes, ALWAYS test these scenarios:

1. **Development Server**:
   - Run `pnpm dev`
   - Access `http://localhost:3000`
   - Verify page loads (may show errors without API keys, this is expected)

2. **Build Validation**:
   - Run `pnpm run build` with proper environment variables
   - **Expected failure in restricted networks** due to Google Fonts

3. **Code Formatting**:
   - Run `pnpm run fix`
   - Verify no formatting changes occur if code is already formatted

### Network Limitations in Restricted Environments
- **Google Fonts**: fonts.googleapis.com blocked - builds will fail
- **pkg.pr.new**: Dependency registry blocked - installs will fail
- **Alpine repositories**: Docker builds fail due to package manager restrictions
- **External APIs**: Most API calls will fail without proper keys

## Common Development Tasks

### Adding New AI Tools
- Create tool file in `lib/tools/`
- Export tool function following existing pattern
- Add to `lib/tools/index.ts`
- Update tool groups in `app/actions.ts`

### Modifying Search Components
- Main search UI: `components/chat-interface.tsx`
- Message rendering: `components/message.tsx`
- Tool-specific components in `components/` root

### Database Changes
- Modify schema: `lib/db/schema.ts`
- Generate migration: `pnpm drizzle-kit generate`
- Update queries: `lib/db/queries.ts`

### Authentication Changes
- Auth configuration: `lib/auth.ts`
- Auth actions: `app/actions.ts`
- Middleware: `middleware.ts`

## Timing Expectations and Timeouts

- **Dependency installation**: 1-2 minutes (fails in restricted networks)
- **Build process**: 45-90 seconds (fails due to Google Fonts in restricted networks)
- **Development server startup**: 1-2 seconds
- **Code formatting**: 5-10 seconds
- **Docker build**: 10-30 minutes (fails in restricted networks)

**CRITICAL TIMEOUT SETTINGS**:
- Development commands: 30+ seconds
- Build commands: 120+ seconds minimum
- Docker operations: 180+ minutes
- **NEVER CANCEL** long-running operations as they may succeed despite appearing to hang

## Known Issues and Workarounds

1. **ESLint Configuration**: Lint command fails due to v9 configuration mismatch
2. **Network Dependencies**: Multiple external dependencies fail in restricted environments
3. **Environment Variables**: 38+ variables required for successful builds
4. **Google Fonts**: Build failures due to network restrictions on fonts.googleapis.com
5. **Better Auth Dependency**: Install fails due to pkg.pr.new accessibility

**Always work around network limitations by using dummy environment variables for testing and expect build failures in restricted environments.**