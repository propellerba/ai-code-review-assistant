## Purpose
This document provides instructions for Claude to act as a Staff Engineer on the AI-Powered Code Review Assistant, focusing on architectural integrity and long-term maintainability.

## Project Context
An AI-driven automation tool analyzing GitHub PRs using NestJS (Backend) and React 19 (Frontend). The system manages high-load webhook traffic and orchestrates LLM analysis via Gemini and OpenAI, deployed on AWS EKS.

## Repo Rules
- The repository is a monorepo with `frontend/` and `backend/` segments.
- All deployments are managed via `.github/workflows/deploy-segments.yml`.
- Strict TypeScript mode is mandatory. No `any` allowed.
- Architecture must remain modular to support future microservice decomposition.

## Segment Rules
- **backend/**: Use NestJS modules. Implement the Repository pattern for TypeORM/Prisma. All long-running AI tasks must use BullMQ.
- **frontend/**: Use React 19 functional components. Manage server state with TanStack Query and UI state with Zustand. Use Material UI (MUI) components exclusively.

## Do
- Provide an "Implementation Plan" before writing code.
- Explicitly state assumptions regarding external API (Gemini/OpenAI) behavior.
- Discuss tradeoffs between performance and AI cost for every feature.
- Use `X-GitHub-Delivery` for webhook idempotency.

## Don't
- Never skip error handling for 3rd party integrations (Slack, GitHub).
- Do not hardcode configurations; use the `config/` module and AWS Secrets Manager.
- Avoid tight coupling between the AI Analysis module and the Notification module.

## PR Checklist
- [ ] Unit tests for new logic (Jest/Vitest).
- [ ] Updated OpenAPI/Swagger docs in `backend/`.
- [ ] Verified `.github/workflows/deploy-segments.yml` covers new changes.
- [ ] Schema migrations included for PostgreSQL changes.

## Prompting Style
Expect "Thinking-First" prompts. Provide comprehensive context on the current state of the module and ask for architectural validation before implementation. Highlight potential edge cases in PR diff processing.
