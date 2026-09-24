# Property Portal project guidance

This file records stable repository boundaries, technical constraints, and architecture. Feature requirements belong in `SPEC.md` and may evolve independently.

## Repository boundaries

The project is split into three independently installable, testable, and deployable repositories. They must not import source files directly from one another.

- `app` owns the web interface: routes, presentation, accessibility, forms, and client-side interaction. It communicates with the API through HTTP.
- `api` owns authentication, authorization, validation, business rules, listing search, persistence, seed data, and API response formats. It is the source of truth for locations and listing taxonomy.
- `mobile` owns the native client and device-specific behavior. It consumes the same API contract and must not rely on browser-only behavior.

## Chosen stack

### Web app (`app`)

- TypeScript, React, and Vite
- React Router for browser navigation
- TanStack Query for server state
- Tailwind CSS and shadcn/ui using preset `b7ClNFsdU`
- shadcn/ui is the component foundation; do not add a second UI component system.

### API (`api`)

- TypeScript on Node.js, using ESM-compatible settings
- Express
- SQLite with Prisma ORM 7 and Prisma Client 7
- `@prisma/adapter-better-sqlite3` as the SQLite driver adapter
- Prisma 7's ESM-first `prisma-client` generator with an explicit generated-client output directory
- Zod or equivalent request validation; bcryptjs for password hashing
- Prisma schema at `prisma/schema.prisma`, configuration in `prisma.config.ts`, and schema changes managed by committed Prisma Migrate files

### Mobile app (`mobile`)

- TypeScript, React Native, Expo, and Expo Router
- TanStack Query-compatible server state
- Expo SecureStore for credentials

## Cross-project architecture constraints

- The API exposes the versioned REST contract at `/api/v1`; web and mobile clients use that contract over HTTP.
- Keep locations and listing taxonomy owned by the API rather than duplicating authoritative UI-only data.
- Enforce authentication, authorization, and listing ownership on the server. Client-side visibility controls are not authorization.
- Use secure HTTP-only sessions for web authentication and a mobile-compatible credential flow with secure device storage.
- Keep secrets out of source control. Document configuration through each repository's `.env.example`; do not put secrets in public client variables.
- Validate request bodies, query parameters, and path identifiers at the API boundary. Use safe ORM queries, explicit CORS allowlists, and rate limits for sensitive actions.
- Store timestamps in UTC and include request IDs in logs where practical.
- Keep local development photo storage separate from production asset storage. Production uploads use a controlled flow with persisted asset metadata.
- Seed data and imagery must be realistic, license-safe, stable, include alt text, and record required attribution. The development seed should include at least 18 listings, at least six in each supported city, and a range of listing types and photo counts.

These decisions should change only through an intentional architecture update. Update this file when such a decision is made.
