# Ghost: The Professional Publishing Platform

## Project Architecture

Ghost follows a monorepo structure with a modular architecture, dividing functionality into specialized components. The project consists of several key parts:

### Core Structure

```
ghost/core/                - Main Ghost application
  ├── core/                - Core functionality
  │   ├── server/          - Backend Node.js server
  │   │   ├── api/         - API endpoints and controllers
  │   │   ├── models/      - Data models and database interactions
  │   │   ├── services/    - Business logic services
  │   │   └── web/         - Web server and request handling
  │   ├── frontend/        - Theme handling and frontend rendering
  │   └── shared/          - Shared utilities and helpers
  └── content/             - Content storage (themes, images, etc.)
```

### Admin Interface

Ghost has a separate admin application built with Ember.js, providing a rich user interface for content management:

```
ghost/admin/               - Admin application
  ├── app/                 - Ember.js application
  └── lib/                 - Admin-specific libraries
```

### Additional Components

Ghost consists of multiple specialized services in a microservices-like architecture:

```
ghost/                     - Core components directory
  ├── admin-x-*            - Modern admin interfaces (React-based)
  ├── api-framework/       - API abstraction layer
  ├── core/                - Main Ghost application
  ├── email-*/             - Email delivery and analytics services
  ├── members-*/           - Membership and subscription services
  ├── stripe/              - Payment processing integration
  └── webhook/             - External integrations
```

### Client Applications

```
apps/                      - Client-side applications
  ├── portal/              - Member subscription portal
  ├── comments-ui/         - Comments interface
  ├── admin-x-*/           - New admin interfaces
  └── signup-form/         - Membership signup components
```

## Technology Stack with Directory Paths

### Backend Technologies
- **Node.js** (`ghost/core/core/server/`): Server-side JavaScript runtime
  - **Express** (`ghost/core/core/server/web/`): Web framework for API and routing
  - **Bookshelf.js** (`ghost/core/core/server/models/`): ORM for database interactions
  - **Knex.js** (`ghost/core/core/server/data/`): SQL query builder
  - **Nconf** (`ghost/core/core/shared/config/`): Configuration management

### Database
- **MySQL/SQLite** (`ghost/core/core/server/data/schema/`): Primary data storage
  - Schema definitions: `ghost/core/core/server/data/schema/`
  - Migrations: `ghost/core/core/server/data/migrations/`

### Admin UI
- **Ember.js** (`ghost/admin/`): Traditional admin interface
  - Components: `ghost/admin/app/components/`
  - Templates: `ghost/admin/app/templates/`
  - Routes: `ghost/admin/app/routes/`
- **React** (`ghost/admin-x-settings/`, `ghost/admin-x-framework/`): Modern admin components
  - Components: `ghost/admin-x-settings/src/components/`
  - Hooks: `ghost/admin-x-framework/src/hooks/`
  - State management: `ghost/admin-x-framework/src/providers/`

### Frontend Rendering
- **Handlebars** (`ghost/core/core/frontend/helpers/`): Templating engine
  - Theme helpers: `ghost/core/core/frontend/helpers/`
  - Theme engine: `ghost/core/core/frontend/services/themes/`

### API
- **RESTful JSON API** (`ghost/core/core/server/api/`): Core API implementation
  - v2 API: `ghost/core/core/server/api/v2/`
  - v3 API: `ghost/core/core/server/api/v3/`
  - v4 API: `ghost/core/core/server/api/v4/`
  - Canary API: `ghost/core/core/server/api/canary/`

### Client Applications
- **React** (`apps/`): Modern client-side applications
  - Portal: `apps/portal/src/`
  - Comments UI: `apps/comments-ui/src/`
  - Signup Form: `apps/signup-form/src/`

### Testing
- **Jest** (`ghost/*/test/`): JavaScript testing framework
- **Mocha** (`ghost/core/test/`): Testing for core functionality
- **Playwright** (`ghost/core/test/e2e-browser/`): End-to-end testing

### Build Tools
- **Webpack** (`ghost/*/webpack.config.js`): Module bundling
- **Babel** (`.babelrc` in various directories): JavaScript transpilation
- **Vite** (`apps/*/vite.config.ts`): Modern build tool for client apps

### Utilities and Libraries
- **Lodash** (used throughout): Utility library
- **Moment.js** (`ghost/core/core/server/lib/`): Date manipulation
- **Bson-objectid** (`ghost/core/core/server/lib/`): ID generation
- **Gscan** (`ghost/core/core/server/services/themes/`): Theme validation

## Extensibility

Ghost provides several extension mechanisms:
- **Themes**: Customize the presentation and layout
- **Custom integrations**: Via the API and webhooks
- **Content filters and helpers**: Extend content processing
- **Admin apps**: Add functionality to the admin area

