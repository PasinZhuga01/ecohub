# EcoHub

A service for organizing virtual economies. Create projects, manage currencies and markets, and automatically calculate item prices in any of your currencies.

> **Status:** Pet project developed for learning purposes.

---

## Repositories

| Part | Repository |
|------|------------|
| Shared Library | [ecohub-shared](https://github.com/PasinZhuga01/ecohub-shared) |
| Backend | [ecohub-backend](https://github.com/PasinZhuga01/ecohub-backend) |
| Frontend | [ecohub-frontend](https://github.com/PasinZhuga01/ecohub-frontend) |

---

## Tech Stack

#### Shared Library
- TypeScript
- Zod

#### Backend
- Node.js + Express.js
- TypeScript
- MySQL (mysql2)
- JWT (jsonwebtoken) + bcrypt
- Multer
- Zod

#### Frontend
- Angular 20 (Standalone Components, Signals, Zoneless)
- TypeScript
- Zod

---

## Running the Project

### Requirements

- Node.js 22+
- MySQL 8+

### 1. Clone the Repositories

```bash
# Shared library (FROM THE COMMON PROJECTS FOLDER)
git clone https://github.com/PasinZhuga01/ecohub-shared
cd ecohub-shared
npm install

# Backend (FROM THE COMMON PROJECTS FOLDER)
git clone https://github.com/PasinZhuga01/ecohub-backend
cd ecohub-backend
npm install

# Frontend (FROM THE COMMON PROJECTS FOLDER)
git clone https://github.com/PasinZhuga01/ecohub-frontend
cd ecohub-frontend
npm install
```

### 2. Set Up Environment Variables

**Backend** - create `env.ts` in `ecohub-backend/src/config`

```typescript
import path from 'path';

import { getRootPath } from '@utils/path';

export default {
	database: {
		host: 'localhost',
		port: 3306,
		name: 'DATABASE_NAME',
		user: 'DATABASE_USER',
		password: 'DATABASE_PASSWORD'
	},
	jwt: {
		secretKey: 'JWT_SECRET_KEY',
		expiresIn: 'JWT_EXPIRES_IN'
	},
	port: 3000,
	saltRounds: 12,
	clientUrl: 'http://localhost:4200',
	uploadsPath: path.join(getRootPath(), 'uploads')
} as const;
```

**Frontend** - create `env.ts` in `ecohub-frontend/src`

```typescript
export default {
	serverUrl: 'http://localhost:3000',
	maxCountNavItems: 5
} as const;
```

### 3. Run

```bash
# Backend (FROM THE ecohub-backend FOLDER)
npm run dev

# Frontend (FROM THE ecohub-frontend FOLDER)
npm start
```

The app will be available at `http://localhost:4200`.

---

## Architectural Highlights

#### Shared Library

The `ecohub-shared` library serves as the single source of truth for the entire stack. It defines:

- **Zod entity schemas** - used on the backend to validate data from MySQL and as a base for API schemas
- **API endpoint contracts** - each endpoint is described declaratively: method, request schema, response schema. Frontend and backend automatically get correct types without duplication
- **`rawRequest`** - a separate schema for GET/DELETE requests where params arrive as strings from query and require transformation before validation

Changing a schema in one place propagates across the entire stack after running `npm update` in dependent projects.

#### Backend

- **Layered architecture**: routes → middlewares → controllers → services → models
- **`ModelsUtility`** - a generic class for database operations supporting automatic camelCase → snake_case conversion, typed filters, and CRUD operations via generics
- **`safePayload`** - a unified controller wrapper: any `PayloadError` thrown in the service layer is automatically converted into a proper HTTP response with the correct status code
- **Declarative route registration** via `createRouter` - routes are described as objects, HTTP method and path are pulled from the API contract automatically

#### Frontend

- **Zoneless Angular** with `provideZonelessChangeDetection()` - dropping Zone.js in favor of signals
- **Signals everywhere** - state is managed via `signal`, `computed`, `effect`, mostly without RxJS in components
- **`createParamsSignal`** - a utility for reactive reading and validation of URL params via Zod, automatically reacts to navigation without recreating the component
- **Reactive localStorage sync** - `StorageService` uses signals to automatically transfer data to/from storage
- **Typed routing** - the `Route` type enumerates all valid app routes, eliminating typos during navigation

---

## Potential Improvements

> ⚠️ These features are not yet implemented and are planned for future iterations.

- Refresh token support
- Mobile version
- Lazy loading for lists (items, projects, etc.)