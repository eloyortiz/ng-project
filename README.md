# NgProject — Executive Report

## Overview

**NgProject** (`angular-auth`) is a reference Angular application that demonstrates enterprise-grade patterns for authentication, session management, authorization, and secure routing using Angular Material as the design system. It is intended as a learning resource and a production-ready template for building authenticated single-page applications.

---

## Technology Stack

| Technology | Version | Role |
|---|---|---|
| Angular | 21.2.x | Core SPA framework |
| Angular Material / CDK | 21.2.x | Component library & UI toolkit |
| TypeScript | 5.9.x | Strongly-typed language |
| RxJS | 7.8.x | Reactive state & async data |
| Angular CLI | 21.2.x | Build, serve, test, lint |
| Karma + Jasmine | 6.4 / 5.1 | Unit testing |
| ESLint + angular-eslint | 9 / 21.3 | Static code analysis |

---

## Architecture

The application follows a **feature-module architecture** with a clean separation between pages, shared components, services, guards, and interceptors.

```
src/
├── app/
│   ├── components/        # Reusable UI shell (LayoutComponent)
│   ├── pages/             # Route-level feature components
│   │   ├── login/         # Public authentication page
│   │   ├── dashboard/     # Protected landing page
│   │   ├── form/          # Address form with validation
│   │   ├── gallery/       # Lazy-loaded product gallery (standalone)
│   │   ├── pictures/      # Product grid from external API
│   │   ├── about/         # Informational page (standalone)
│   │   └── detail/        # Product detail by ID (standalone)
│   ├── services/          # Business logic & HTTP communication
│   ├── guards/            # Route protection
│   ├── interceptors/      # HTTP middleware
│   ├── models/            # TypeScript interfaces & data shapes
│   ├── material/          # Angular Material module barrel
│   ├── app.module.ts      # Root NgModule
│   ├── app.component.ts   # Application shell entry point
│   └── app-routing.module.ts  # Centralized route definitions
├── environments/          # Dev / prod environment configuration
└── styles.css             # Global styles
```

---

## Features

### Authentication & Session Management
- **JWT-based login** — `AuthService` POSTs credentials to the API, persists the returned token in `localStorage` via `TokenService`, and fetches the authenticated user profile.
- **Reactive auth state** — A `BehaviorSubject<User | null>` (`authState$`) propagates the current user to any subscribing component, enabling live UI updates without polling.
- **Automatic token injection** — `TokenInterceptor` clones every outgoing `HttpRequest` and attaches an `Authorization: Bearer <token>` header when a token is present, eliminating repetitive boilerplate across service calls.

### Route Protection
- **Functional guard (`authGuardFn`)** — A `CanActivateFn` that uses `inject()` to read the token synchronously. Any unauthenticated attempt to access `/admin/**` is redirected to `/login`.
- **Legacy class guard (`AuthGuard`)** — A `CanActivate` implementation retained for reference alongside the modern functional approach.
- The entire `/admin` subtree—including `/admin/dashboard`, `/admin/form`, `/admin/gallery`, `/admin/pictures`, `/admin/about`, and `/admin/product/:id`—is protected by the guard at the parent route level.

### Routing
| Path | Component | Guard | Notes |
|---|---|---|---|
| `/` | — | — | Redirects to `/login` |
| `/login` | `LoginComponent` | None | Public |
| `/admin` | `LayoutComponent` | `authGuardFn` | Parent layout with sidenav |
| `/admin/dashboard` | `DashboardComponent` | inherited | Stats cards |
| `/admin/form` | `FormComponent` | inherited | Reactive address form |
| `/admin/gallery` | `GalleryComponent` | inherited | Lazy-loaded standalone |
| `/admin/pictures` | `PicturesComponent` | inherited | Product grid |
| `/admin/about` | `AboutComponent` | inherited | Lazy-loaded standalone |
| `/admin/product/:id` | `DetailComponent` | inherited | Lazy-loaded standalone |

### UI / UX
- **Responsive shell** — `LayoutComponent` uses `BreakpointObserver` to toggle a collapsible Material sidenav automatically on small screens.
- **Material Design** — The `MaterialModule` barrel exposes sidenav, toolbar, grid list, card, table, paginator, input, select, radio, button, icon, menu, and expansion panel components.
- **Indigo-Pink theme** — Angular Material's prebuilt `indigo-pink.css` theme is applied globally.
- **Optimized images** — `NgOptimizedImage` is imported for lazy, correctly-sized image loading.

### Modern Angular Patterns
- **Standalone components** — `GalleryComponent`, `AboutComponent`, and `DetailComponent` are standalone, demonstrating the Angular 14+ component model.
- **Lazy loading** — Standalone components are loaded with `loadComponent()` to enable automatic code splitting and reduce the initial bundle.
- **Functional dependency injection** — `inject()` is used inside the functional guard and the `getProductWithParam()` helper in `ProductService`, aligning with Angular's modern DI approach.
- **Strongly-typed reactive forms** — `FormBuilder.nonNullable.group()` is used in `LoginComponent` to eliminate null from form value types.

### Data Models
```typescript
// Authentication
interface LoginRta { access_token: string; }
interface User    { id: string; name: string; email: string; }
```

### External API
Both the development and production environments point to the **Escuela JS** public REST API (`http://api.escuelajs.co/api/v1`), providing endpoints for authentication (`/auth/login`, `/auth/profile`) and products (`/products`, `/products/:id`).

---

## Getting Started

### Prerequisites
- Node.js ≥ 18
- npm ≥ 8

### Install Dependencies
```bash
npm install
```

### Run Development Server
```bash
npm start
# Navigate to http://localhost:4200
```

### Build for Production
```bash
npm run build
# Output: dist/angular-auth/
```

### Run Unit Tests
```bash
npm test
```

### Lint
```bash
npm run lint
```

---

## Build Configuration Highlights

| Setting | Value |
|---|---|
| Builder | `@angular-devkit/build-angular:browser-esbuild` |
| Output path | `dist/angular-auth` |
| Initial bundle budget | 500 KB warning / 1 MB error |
| Component style budget | 2 KB warning / 4 KB error |
| Production hashing | Enabled |
| Source maps (prod) | Disabled |
| Test runner | Karma + Jasmine |

---

## Version History

This project is maintained across multiple Angular versions:

- [Angular v14](https://github.com/nicobytes/ng-project/tree/angular-v14)
- [Angular v15](https://github.com/nicobytes/ng-project/tree/angular-v15)
- **Current branch** — Angular 21
