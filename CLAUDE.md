# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

MERN Real Estate (RESS) is a full-stack real estate listing application built with the MERN stack (MongoDB, Express, React, Node.js). The application features user authentication (including OAuth via Firebase), property listing management, and a search/filter system.

## Architecture

### Monorepo Structure

The project is organized as a monorepo with two main directories:

- **`api/`** - Express.js backend server
  - `controllers/` - Business logic (auth, listing, user)
  - `models/` - Mongoose schemas (User, Listing)
  - `routes/` - API endpoint definitions
  - `utils/` - Middleware utilities (JWT verification, error handling)
  - `index.js` - Server entry point and Express configuration

- **`client/`** - React (Vite) frontend application
  - `src/pages/` - Page components (Home, SignIn, Profile, CreateListing, etc.)
  - `src/components/` - Reusable UI components (Header, OAuth, PrivateRoute, etc.)
  - `src/redux/` - Redux Toolkit state management
  - Built with Vite + React + TailwindCSS

### Key Architectural Patterns

**Authentication Flow:**
- JWT tokens stored in HTTP-only cookies (set by backend)
- Frontend Redux state persists user data via `redux-persist`
- Firebase OAuth integration for Google sign-in
- JWT verification middleware (`api/utils/verifyUser.js`) protects routes

**API Communication:**
- Development: Vite dev server proxies `/api` requests to Express backend (port 3000)
- Production: Express serves static React build from `client/dist/`
- All API routes prefixed with `/api` (e.g., `/api/auth`, `/api/user`, `/api/listing`)

**State Management:**
- Redux Toolkit with persistence (localStorage)
- User slice handles auth state (signIn, update, delete, signOut)
- Loading/error states managed in Redux

**Routing:**
- React Router v6 for client-side routing
- Protected routes use `<PrivateRoute>` wrapper component
- Express catch-all serves React app for non-API routes

## Development Commands

### Full Stack Development

```bash
# Install all dependencies (root + client)
npm install
cd client && npm install

# Run backend server with hot reload (uses nodemon)
npm run dev

# Run frontend dev server (Vite)
cd client && npm run dev
```

The backend runs on port 3000, frontend dev server typically on port 5173.

### Client-Only Commands

```bash
cd client

# Development server
npm run dev

# Production build
npm run build

# Preview production build
npm run preview

# Lint code
npm run lint
```

### Backend-Only Commands

```bash
# Start backend in production mode
npm start

# Development mode with nodemon
npm run dev
```

### Production Build

```bash
# Build entire application (installs deps and builds client)
npm run build
```

This builds the React app to `client/dist/`, which Express serves in production.

## Environment Variables

Two `.env` files are required:

**Root `.env` (Backend):**
```
PORT=5000
MONGO_URL=mongodb://localhost:27017
JWT_SECRET=<your-secret>
JWT_EXPIRE=30d
```

**Client `.env` (Frontend - create as `client/.env`):**
```
VITE_FIREBASE_API_KEY=<your-firebase-api-key>
```

Note: `.env` files are gitignored. Both root and `api/` directory have `.env` files.

## Data Models

**User Model** (`api/models/user.model.js`):
- `username` (unique, required)
- `email` (unique, required)
- `password` (required, hashed with bcryptjs)
- `avatar` (optional, default provided)
- Timestamps (createdAt, updatedAt)

**Listing Model** (`api/models/listing.model.js`):
- Property details: `name`, `description`, `address`
- Pricing: `regularPrice`, `discountPrice`
- Features: `bedrooms`, `bathrooms`, `furnished`, `parking`
- `type` (rent/sale)
- `offer` (boolean, duplicated field in schema - bug to be aware of)
- `imageUrls` (array)
- `userRef` (creator's user ID)
- Timestamps (createdAt, updatedAt)

## API Structure

All API routes follow RESTful conventions:

- **`/api/auth`** - Authentication endpoints (signup, signin, google OAuth)
- **`/api/user`** - User management (update, delete, get listings)
- **`/api/listing`** - CRUD operations for property listings

Routes use `verifyToken` middleware for protected endpoints.

## Important Implementation Details

- **Error Handling:** Custom error handler middleware in `api/utils/error.js` and global Express error middleware in `api/index.js:41-49`
- **Cookie-based Auth:** Access tokens stored in cookies, not localStorage
- **Image Handling:** Listing images stored as URL arrays (likely using external service like Firebase Storage)
- **Private Routes:** Frontend `PrivateRoute` component checks Redux user state and redirects to `/sign-in` if unauthenticated
- **SPA Routing:** Express catch-all (`app.get("*")`) ensures React Router handles all non-API routes in production

## Current Branch

Working on branch `feature/Russian_Lang` - likely implementing Russian language support/localization.
