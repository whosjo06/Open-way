# Open Way - Accessibility Mapping Platform

## Overview

Open Way is a community-driven accessibility mapping platform with the slogan "Move Your Way!" The application helps disabled individuals find, review, and share information about places in Philadelphia that are accessible, partially accessible, or not accessible. The platform emphasizes lived experience, community voices, and advocacy for better accessibility through features like place listings, user reviews, and a petition system.

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Frontend Architecture
- **Framework**: React 18 with TypeScript
- **Routing**: Wouter (lightweight React router)
- **State Management**: TanStack React Query for server state, Zustand for client state (settings)
- **Styling**: Tailwind CSS with custom CSS variables for theming
- **UI Components**: shadcn/ui component library (Radix UI primitives)
- **Animations**: Framer Motion for page transitions and micro-interactions
- **Forms**: React Hook Form with Zod validation

### Backend Architecture
- **Runtime**: Node.js with Express
- **Language**: TypeScript (ESM modules)
- **API Design**: RESTful endpoints defined in shared routes file
- **Build Tool**: esbuild for server bundling, Vite for client

### Data Layer
- **Database**: PostgreSQL
- **ORM**: Drizzle ORM with drizzle-zod for schema validation
- **Schema Location**: `shared/schema.ts` contains all table definitions
- **Migrations**: Drizzle Kit with `db:push` command

### Project Structure
```
├── client/          # React frontend
│   └── src/
│       ├── components/  # UI components including shadcn/ui
│       ├── hooks/       # Custom React hooks
│       ├── pages/       # Route page components
│       └── lib/         # Utilities and query client
├── server/          # Express backend
│   ├── index.ts     # Server entry point
│   ├── routes.ts    # API route handlers
│   ├── storage.ts   # Database operations
│   └── db.ts        # Database connection
└── shared/          # Shared code between client/server
    ├── schema.ts    # Drizzle table definitions
    └── routes.ts    # API route contracts with Zod schemas
```

### Key Design Patterns
- **Shared Types**: Schema and route definitions shared between frontend and backend via `@shared/*` path alias
- **Type-Safe API**: Zod schemas validate both API inputs and responses
- **Accessibility-First Design**: High contrast colors, large fonts, keyboard navigation, color-coded status indicators (green/yellow/red)
- **Persistent Settings**: User preferences (theme, text size, reduced motion) stored in localStorage via Zustand persist middleware

### Database Schema
- **users**: User accounts with password hashes, 2FA settings, backup codes
- **user_sessions**: PostgreSQL session store (via connect-pg-simple)
- **saved_places**: User bookmarked places
- **categories**: Place categories (Transit, Museums, Libraries, Parks, etc.)
- **places**: Location information with coordinates, accessibility status, address, hours
- **accessibility_features**: Detailed accessibility features per place
- **place_media**: Photo gallery for places
- **place_tips**: User-submitted tips for places (auth required)
- **reviews**: User reviews with ratings and helpful vote counts (auth required, linked to userId)
- **signatures**: Petition signatures with location tracking
- **petition_updates**: Organizer updates for the petition
- **resources**: Accessibility resources directory
- **events**: Community events calendar
- **blog_posts**: News and advocacy articles
- **faq_entries**: Frequently asked questions
- **contact_submissions**: Contact form submissions (rate limited, sanitized)
- **partners**: Partner organizations
- **activity_log**: Recent site activity feed

### Authentication & Security
- **Password Security**: bcrypt with 12 rounds, minimum 8 chars with number+symbol, common password rejection
- **Sessions**: PostgreSQL-backed via connect-pg-simple, 24-hour expiry, HttpOnly/Secure/SameSite=strict cookies
- **Session Secret Validation**: Startup crashes if SESSION_SECRET is missing in production or less than 32 characters
- **Rate Limiting**: 10 auth attempts per 15 min, 100 API requests per min
- **Two-Factor Auth**: Optional TOTP with speakeasy, QR code setup, 10 backup codes per user, AES-256-GCM encrypted at rest
- **CSRF Protection**: All authenticated state-changing routes require X-CSRF-Token header (fetched from /api/csrf-token)
- **Security Headers**: helmet.js with production CSP, HSTS (1 year), referrer policy, noSniff, xssFilter
- **Input Sanitization**: Comprehensive XSS prevention (HTML tags, javascript: URLs, event handlers, control characters)
- **SQL Injection Prevention**: All queries use Drizzle ORM with parameterized queries
- **Protected Routes**: Reviews and tips require authentication via requireAuth middleware
- **Admin Authorization**: Admin content creation (places, events, resources, blog, FAQ, partners) requires isAdmin flag + validateCsrf
- **Admin Promotion**: To grant admin access, update user directly in database: `UPDATE users SET is_admin = true WHERE email = 'admin@example.com';`
- **Ownership Verification**: Update/delete operations check userId ownership in storage layer

### Application Pages
- **Home (/)**: Featured places carousel, stats dashboard, activity feed, map preview, partners
- **Places (/places)**: Directory with category/accessibility filters, map/list toggle, Leaflet map
- **Place Detail (/places/:id)**: Photo gallery, accessibility checklist, tips, reviews, location map
- **Community (/reviews)**: Review leaderboard, filters, helpful votes, featured reviewers
- **Petition (/petition)**: Live signature counter, milestone celebrations, sign form, updates
- **Resources (/resources)**: Categorized accessibility resources with search
- **Events (/events)**: Event calendar with category filters
- **Blog (/blog)**: News articles with modal view
- **FAQ (/faq)**: Accordion with search
- **Contact (/contact)**: Contact form with map
- **Settings (/settings)**: Theme, text size, reduced motion, high contrast toggles

### Global Features
- **Global Search**: Cmd+K shortcut, searches across places, resources, events, blog
- **Breadcrumb Navigation**: Auto-generated based on current route
- **Accessibility Settings**: Theme, text size, reduced motion, high contrast (persisted)

## External Dependencies

### Database
- PostgreSQL database (connection via `DATABASE_URL` environment variable)
- `connect-pg-simple` for session storage capability

### Frontend Libraries
- `@tanstack/react-query` for data fetching and caching
- `framer-motion` for animations
- `lucide-react` for accessible icons
- `date-fns` for date formatting
- `react-hook-form` + `@hookform/resolvers` for form handling

### Build & Development
- Vite for frontend development and bundling
- Replit-specific plugins for dev experience (`@replit/vite-plugin-*`)
- esbuild for server production bundling

### UI Framework
- Full shadcn/ui component library with Radix UI primitives
- Custom Tailwind configuration with accessibility-focused color palette
- Google Fonts: Lexend (display) and Atkinson Hyperlegible (body - designed for readability)