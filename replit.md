# VIT-C Hackathon Lost & Found App

## Overview

A university laundry lost-and-found platform with AI-powered item matching. Students can report lost clothing, staff can submit found items with photos, and Gemini AI automatically matches them using image analysis.

## Stack

- **Backend**: Express 5 + TypeScript (tsx for development)
- **Frontend**: React + Vite + TanStack Query + Tailwind CSS + shadcn/ui
- **Database**: PostgreSQL + Drizzle ORM (Drizzle-Kit for migrations)
- **AI Matching**: Google Gemini 2.5 Flash via Replit AI Integrations proxy
- **Auth**: Passport.js local strategy + express-session
- **File Uploads**: Multer (5MB limit, stored in /uploads)

## Key Commands

- `pnpm run dev` — start development server (Express + Vite) on port 5000
- `pnpm run db:push` — push schema changes to database
- `pnpm run build` — build for production
- `pnpm run check` — TypeScript type checking

## Project Structure

```
server/         Express backend
  index.ts      Server entry point
  routes.ts     All API routes
  matching.ts   AI lost/found matching orchestration
  gemini.ts     Gemini AI vision integration
  storage.ts    Database access layer
  auth.ts       Password hashing utilities

client/         React frontend
  src/
    pages/
      admin/    Admin dashboard (LostFound, Students, etc.)
      student/  Student portal (ReportItem, FoundItems, etc.)

shared/
  schema.ts     Drizzle ORM schema + Zod types (source of truth)
```

## Database Tables

- `users` — students, staff, admins
- `machines` — washers/dryers
- `laundry_sessions` — wash sessions
- `lost_items` — student lost item reports (searching/matched/resolved)
- `found_items` — staff found item submissions (unclaimed/claimed/resolved)
- `item_matches` — AI match scores between lost/found pairs (≥60% threshold triggers notification)
- `laundry_workflow` — bag hand-in/wash tracking
- `notifications` — in-app notifications

## AI Matching

**Trigger points:**
1. Student reports a lost item → matched against all unclaimed found items with images
2. Staff submits a found item with a photo → matched against all active lost items

**Flow:** `routes.ts` → `matching.ts` → `gemini.ts` (Gemini Vision) → `storage.saveMatch()` → notification if ≥ 60%

**Integration:** Uses Replit AI Integrations proxy via `AI_INTEGRATIONS_GEMINI_BASE_URL` / `AI_INTEGRATIONS_GEMINI_API_KEY` env vars (no external API key needed).

## Environment Variables

- `DATABASE_URL` — PostgreSQL connection string (runtime-managed by Replit)
- `SESSION_SECRET` — express-session secret
- `AI_INTEGRATIONS_GEMINI_BASE_URL` — Replit AI proxy URL (auto-configured)
- `AI_INTEGRATIONS_GEMINI_API_KEY` — Replit AI proxy key (auto-configured)
- `GEMINI_API_KEY` — (optional) direct Google AI key, falls back if proxy not available
- `PORT` — server port (defaults to 5000)
