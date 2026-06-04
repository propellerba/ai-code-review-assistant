# AI-Powered Code Review Assistant: Architectural Guide

This document serves as the authoritative architectural blueprint for the AI-Powered Code Review Assistant. It combines high-level system design with specific implementation standards for both backend and frontend development.

## 1. Architecture Overview

The system follows a **Modular Monolith** architecture using **NestJS** on the backend, designed for future microservice decomposition. The architecture is event-driven to handle high volumes of GitHub webhooks asynchronously.

- **Core Orchestration:** NestJS controllers manage incoming webhooks; Services coordinate AI analysis logic via Gemini and OpenAI APIs.
- **Asynchronous Processing:** BullMQ (Redis) manages long-running LLM requests to prevent webhook timeouts and ensure retriability.
- **Frontend:** A React 19 SPA (Single Page Application) provides a high-performance dashboard for analytics and review management.
- **Data Persistence:** PostgreSQL handles structured data, while AWS S3 stores large diffs and analysis reports.
- **Scalability:** Stateless design containerized with Docker and orchestrated via AWS EKS, allowing horizontal scaling based on PR volume.

## 2. Tech Stack Details

### Backend
- **Framework:** NestJS (Node.js)
- **API Style:** RESTful with OpenAPI (Swagger)
- **Queue Management:** BullMQ / Redis
- **ORM:** TypeORM or Prisma
- **AI Integration:** Gemini API & OpenAI API
- **External Integrations:** GitHub API, Slack API

### Frontend
- **Framework:** React 19 (TypeScript)
- **UI Library:** Material UI (MUI)
- **State Management:** TanStack Query (Server State) & Zustand (Client State)
- **Build Tool:** Vite
- **Routing:** React Router v7

### Infrastructure (AWS)
- **Compute:** EKS (Kubernetes)
- **Database:** RDS (PostgreSQL)
- **Storage:** S3
- **Observability:** CloudWatch & OpenTelemetry
- **CI/CD:** GitHub Actions

## 3. Folder Structure

### Backend (NestJS Modular Structure)
```text
src/
├── common/             # Global decorators, filters, interceptors
├── config/             # Env configuration and AWS Secrets Manager integration
├── database/           # Entities and Migrations
├── providers/          # Gemini, OpenAI, GitHub, and Slack integrations
└── modules/            # Domain-specific modules
    ├── analysis/       # AI logic and PR diff processing
    ├── reviews/        # Review persistence and history
    ├── webhooks/       # GitHub/Slack event handlers
    └── notifications/  # Dispatcher for Slack and internal alerts
```

### Frontend (Feature-Based Layout)
```text
src/
├── components/         # Shared UI components (MUI wrappers)
├── config/             # API and Environment config
├── features/           # Encapsulated feature modules
│   ├── dashboard/      # Analytics and overviews
│   ├── history/        # Past code review listings
│   └── settings/       # Organization and API configurations
├── hooks/              # Shared custom hooks
├── lib/                # API client (Axios + TanStack Query)
├── types/              # Global TypeScript interfaces
└── App.tsx
```

## 4. Design Patterns

- **Repository Pattern:** Decouples business logic from data access, ensuring NestJS services remain clean and testable.
- **Strategy Pattern:** Implemented in the Analysis module to switch between AI providers (Gemini/OpenAI) or models based on PR size or configuration.
- **Webhook Idempotency:** Implementation of `X-GitHub-Delivery` header tracking to prevent duplicate processing of the same PR event.
- **Component-Driven Development:** Frontend built using isolated, reusable MUI components.
- **Optimistic Updates:** Utilizing TanStack Query to provide immediate UI feedback on user actions (e.g., dismissing a review suggestion).

## 5. Coding Standards

### General
- **TypeScript:** Strict mode enabled. No `any` types; use `unknown` and type guards where necessary.
- **Naming:** 
    - Variables/Functions: `camelCase`
    - Classes/Interfaces/Components: `PascalCase`
    - Files: `kebab-case` (e.g., `user-profile.component.tsx`)

### Database (PostgreSQL)
- **Naming:** 
    - Tables: `snake_case` and **plural** (e.g., `audit_logs`).
    - Columns: `snake_case` (e.g., `created_at`).
- **Audit Fields:** Every table must include `created_at`, `updated_at` (TIMESTAMPTZ), and `deleted_at` for soft deletes.
- **Integrity:** Enforce uniqueness and check constraints at the DB level, not just the application layer.

### Frontend
- **Functional Components:** Use arrow functions with `React.FC` or standard function declarations.
- **Hooks:** Custom hooks must be used for any logic involving `useEffect` or complex state.
- **Environment Variables:** All API keys and endpoints must be accessed via `import.meta.env` with a validation wrapper.

## 6. API Design

### RESTful Principles
- **Versioning:** Prefixed with `/api/v1/`.
- **Naming:** Kebab-case endpoints (e.g., `/api/v1/analysis-reports`).
- **Resource Depth:** Maximum 2 levels deep (e.g., `/projects/:id/reviews`).

### Webhook Handling
- **Acknowledgment:** Return a `202 Accepted` immediately upon receiving a GitHub webhook.
- **Background Processing:** Offload diff analysis and LLM calls to BullMQ workers.

### Security
- **Auth:** GitHub OAuth2 for user login; JWT-based sessions for the dashboard.
- **Secrets:** API keys for Gemini/OpenAI must be stored in AWS Secrets Manager, never in `.env` files in production.
- **Rate Limiting:** Protect analysis endpoints to prevent LLM credit exhaustion.

## 7. Testing Strategy

- **Unit Testing:** Jest for backend services; Vitest + React Testing Library for frontend components.
- **Integration Testing:** NestJS `Supertest` to validate API endpoints and database constraints.
- **Mocking:** Systematic mocking of external providers (Gemini, OpenAI, GitHub) using `nock` or NestJS Mock Providers to control CI costs.
- **End-to-End (E2E):** Playwright for critical paths (e.g., "GitHub Webhook -> AI Analysis -> Slack Notification -> Dashboard Update").
- **Observability:** Health check endpoints at `/health` and OpenTelemetry tracing for monitoring AI provider latency.