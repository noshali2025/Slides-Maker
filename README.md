# Slides-Maker
Slides Maker — Create &amp; Export Professional Slides, Invoices, Letters Turn a design into a downloadable PDF / PPTX / DOCX in seconds — perfect for agencies &amp; startups.
# DocuCreate - Professional Document Creation Platform

## Overview

## Arriverr - Your Digital Partner
## Whatsapp: Message Arriverr on WhatsApp. https://wa.me/message/XWEV35ABTXQIP1


DocuCreate is a comprehensive document creation platform that allows users to create beautiful, professional documents from 20+ templates across 4 categories: Slides, Invoices, Letters, and Quotations. The platform features multi-format export capabilities (PDF, PPTX, DOCX, JPG) with an intuitive editor interface.

## Project Structure

### Frontend
- **Landing Page** (`/`): Hero section, features grid, how it works, export formats showcase, and CTA sections
- **Template Gallery** (`/templates`): Browse and filter 20+ templates by category with search functionality
- **Editor** (`/editor/:templateId`): Real-time document editor with live preview and customization options

### Backend
- **API Routes**:
  - `GET /api/templates` - Get all templates
  - `GET /api/templates/:id` - Get specific template
  - `POST /api/templates` - Create new template
  - `GET /api/documents` - Get all documents
  - `GET /api/documents/:id` - Get specific document
  - `POST /api/documents` - Create new document
  - `PATCH /api/documents/:id` - Update document
  - `DELETE /api/documents/:id` - Delete document

### Data Models
- **Template**: Stores template information (name, category, description, content structure)
- **Document**: Stores user-created documents with customized content

### Categories
1. **Slides** (5 templates): Modern Pitch Deck, Dark Theme, Gradient Vision, Minimalist, Corporate Blue
2. **Invoices** (5 templates): Professional, Modern, Minimal, Detailed, Freelance
3. **Letters** (5 templates): Business Letter, Cover Letter, Recommendation, Thank You, Formal Notice
4. **Quotations** (5 templates): Standard, Detailed, Service, Product, Construction

### Export Functionality
- **PDF**: Using jsPDF and html2canvas
- **PPTX**: Using PptxGenJS
- **DOCX**: Using docx library
- **JPG**: Using html2canvas

## Design System
- **Primary Color**: Purple-blue (#8B5CF6)
- **Accent Color**: Coral pink (#F43F5E)
- **Typography**: Inter font family
- **Components**: Shadcn UI components with custom theming
- **Dark Mode**: Full dark mode support

## Key Features
- 20+ professionally designed templates
- Real-time document preview
- Intuitive drag-and-drop editor
- Multi-format export (PDF, PPTX, DOCX, JPG)
- Category filtering and search
- Responsive design
- Dark mode support
- SEO optimized

## Technology Stack
- **Frontend**: React, TypeScript, Tailwind CSS, Shadcn UI, Wouter, TanStack Query
- **Backend**: Express.js, Node.js
- **Export**: html2canvas, jsPDF, PptxGenJS, docx, file-saver
- **Storage**: In-memory storage (MemStorage)

## Recent Changes
- Initial project setup with complete schema definition
- Implemented all 20+ templates across 4 categories
- Built landing page with hero section and feature showcase
- Created template gallery with filtering and search
- Implemented document editor with live preview
- Added export functionality for all 4 formats
- Configured SEO meta tags and Open Graph tags
- Set up backend API routes and storage layer

# Deployment & Hosting

This section documents how to run, build and deploy **DocuCreate** (Vite frontend + Node/Express backend) in development and production, with Docker and common hosting options.

> **Project structure (relevant):**
> - `client/` — Vite/React frontend (served as static assets)
> - `server/` — Node/TypeScript server (entry: `server/index.ts`)
> - Root `package.json` handles dev/build/start:  
>   - `npm run dev` — development (runs server + Vite dev)  
>   - `npm run build` — production build (Vite build + esbuild bundle server)  
>   - `npm start` — start production server (`node dist/index.js`)  
> - DB tooling: `npm run db:push` (drizzle-kit)

---

## Prerequisites
- Node.js 18+ (LTS recommended)
- npm (or pnpm/yarn)  
- Docker & Docker Compose (for container deployment)
- A Postgres-compatible database for production (Neon, Supabase, AWS RDS, etc.) if you intend to persist data
- Optional: S3-compatible storage (AWS S3, DigitalOcean Spaces, GCS) for exported files

---

## Environment variables
Create `.env` from `.env.example` and set production values. Minimum recommended variables:

server

PORT=4000
NODE_ENV=production

database (Postgres)

DATABASE_URL=postgres://user:password@host:5432/dbname

storage (S3 example)

S3_BUCKET=my-bucket
S3_REGION=us-east-1
S3_ACCESS_KEY_ID=YOUR_KEY
S3_SECRET_ACCESS_KEY=YOUR_SECRET

other

JWT_SECRET=change_this
PUBLIC_URL=https://your-domain.com


> **Important:** Never commit `.env` with secrets. Keep `.env` in `.gitignore`.

---

## Local development (fast)
1. Install deps:
```bash
npm install

npm run dev

This runs Vite in dev mode and the server in dev mode. Open the frontend on the port Vite reports (typically http://localhost:5173) and the server API on the server PORT (default 4000).

Production build + run locally (preview)

Build:

npm run build


vite build creates frontend static bundle.

esbuild server/index.ts ... --outdir=dist bundles the server into dist/index.js.

Run:

# ensure env variables are set
npm start
# or
NODE_ENV=production PORT=4000 node dist/index.js


Server will serve APIs and should serve static client files if configured.

Database migrations (Drizzle)

If you are using Drizzle and Postgres/Neon, run migrations / push schema:

Ensure DATABASE_URL is set in your env.

Push migrations:

npm run db:push


(Adjust if you use drizzle-kit directly; follow drizzle docs for advanced workflows.)

Docker (recommended for reliable production)
Dockerfile (root)

Create Dockerfile at repo root:

# Stage 1: build
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
COPY tsconfig.json vite.config.ts ./
COPY client client
COPY server server
COPY shared shared
RUN npm ci
RUN npm run build

# Stage 2: run
FROM node:20-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production
# copy server + built client
COPY --from=builder /app/dist ./dist
COPY package*.json ./
RUN npm ci --omit=dev
EXPOSE 4000
CMD ["node", "dist/index.js"]

docker-compose.yml (example)
version: "3.8"
services:
  app:
    build: .
    restart: always
    environment:
      - NODE_ENV=production
      - PORT=4000
      - DATABASE_URL=${DATABASE_URL}
      - S3_BUCKET=${S3_BUCKET}
      - S3_ACCESS_KEY_ID=${S3_ACCESS_KEY_ID}
      - S3_SECRET_ACCESS_KEY=${S3_SECRET_ACCESS_KEY}
      - JWT_SECRET=${JWT_SECRET}
      - PUBLIC_URL=${PUBLIC_URL}
    ports:
      - "4000:4000"
    volumes: []
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_USER: test
      POSTGRES_PASSWORD: test
      POSTGRES_DB: docu
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data
volumes:
  pgdata:


Build & run:

docker compose up --build -d
