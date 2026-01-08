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
- **places**: Stores location information with name, category, accessibility status, description
- **reviews**: User-submitted reviews linked to places
- **signatures**: Petition signature tracking for advocacy feature

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