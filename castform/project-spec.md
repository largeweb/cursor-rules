
**Architecture Overview:**
```markdown
# Project Architecture Specification

## Tech Stack
- **Framework**: Next.js 15 with App Router
- **Runtime**: Cloudflare Pages Edge Runtime
- **Database**: Cloudflare D1 (SQLite)
- **Storage**: Cloudflare R2 (Object Storage)
- **Cache**: Cloudflare KV (Key-Value)
- **Language**: TypeScript

## Core Integrations
- **Authentication**: [Auth system used]
- **Payments**: Stripe webhooks
- **Voice Processing**: Vapi API callbacks
- **External APIs**: [List key integrations]

## Environment Configuration
- Bindings defined in `wrangler.jsonc`
- Types declared in `env.d.ts`
- No `.dev.vars` - use wrangler.jsonc vars section
```

Directory & Route Structure:
```markdown
## Project Organization

### Route Groups
- `(auth)`: Authentication pages - /login, /register
- `(dashboard)`: Protected dashboard routes - /dashboard/*
- `(public)`: Public marketing pages - /about, /pricing

### API Architecture
- `api/auth/`: Authentication endpoints
- `api/users/`: User management
- `api/files/`: File upload/download via R2
- `api/webhooks/`: External service callbacks
- `api/jobs/`: Background job management

### Component Structure
- `components/ui/`: Reusable UI components
- `components/layout/`: Layout-specific components
- `app/[route]/components/`: Route-specific components
```

Data Flow Documentation:
```markdown
## Key Data Flows

### File Upload Process
1. Client uploads via form → `/api/files`
2. Server validates file type/size
3. File stored in R2 with generated key
4. Metadata saved to D1 database
5. File ID returned to client

### Authentication Flow
1. User login → `/api/auth/login`
2. Credentials validated against D1
3. Session data cached in KV
4. JWT/session token returned
5. Protected routes check KV for session

### Webhook Processing
1. External service → `/api/webhooks/[service]`
2. Signature verification
3. Event processing and D1 updates
4. KV cache invalidation if needed
5. Response confirmation

### Background Jobs
1. Job creation via API
2. Status tracking in D1
3. Progress updates cached in KV
4. Real-time status via polling
```

API Reference:
```markdown
## API Endpoints

### Authentication
- `POST /api/auth/login` - User authentication
- `POST /api/auth/logout` - Session termination
- `GET /api/auth/me` - Current user info

### File Management
- `POST /api/files` - Upload file to R2
- `GET /api/files/[id]` - Download/stream file
- `DELETE /api/files/[id]` - Remove file

### User Management
- `GET /api/users` - List users (admin)
- `POST /api/users` - Create user
- `PUT /api/users/[id]` - Update user
- `DELETE /api/users/[id]` - Delete user

### Webhooks
- `POST /api/webhooks/stripe` - Payment events
- `POST /api/webhooks/vapi` - Voice processing events
```

Database Schema:
```markdown
## Database Tables (D1)

### users
- id (TEXT PRIMARY KEY)
- email (TEXT UNIQUE)
- name (TEXT)
- role (TEXT)
- created_at (INTEGER)

### files
- id (TEXT PRIMARY KEY)
- original_name (TEXT)
- r2_key (TEXT)
- size (INTEGER)
- type (TEXT)
- user_id (TEXT)

### jobs
- id (TEXT PRIMARY KEY)
- type (TEXT)
- status (TEXT)
- data (TEXT) -- JSON
- created_at (INTEGER)
```