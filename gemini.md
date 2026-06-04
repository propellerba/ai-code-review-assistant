## Purpose
This document guides Gemini to provide structured, traceable, and logically synthesized contributions to the Code Review Assistant project.

## Project Context
This project automates code reviews by integrating GitHub webhooks with Gemini/OpenAI APIs. It features a React 19 dashboard for historical analytics and an event-driven NestJS backend.

## Repo Rules
- Maintain clear separation between `frontend/` and `backend/` segments.
- CI/CD is driven by `.github/workflows/deploy-segments.yml`.
- Every database change requires a TIMESTAMPTZ audit field.
- Use K8s-ready containerization for all services.

## Segment Rules
- **backend/**: Implement the Strategy pattern for switching between Gemini and OpenAI models. Ensure all API endpoints follow the `/api/v1/` versioning.
- **frontend/**: Utilize React 19's latest features. Use Vite for builds. Wrap MUI components to maintain design consistency.

## Do
- Use explicit reasoning steps for complex logic (e.g., PR diff chunking).
- Provide traceability for why a specific AI prompt structure was chosen.
- Use Zod or class-validator for strict input validation at the API boundary.
- Implement structured logging via CloudWatch.

## Don't
- Do not perform heavy logic inside React components; use custom hooks.
- Do not ignore rate limits; implement throttling in the `providers/` layer.
- Avoid using non-plural table names in PostgreSQL.

## PR Checklist
- [ ] Documentation of decision-making for AI model parameters.
- [ ] Integration tests using NestJS Supertest.
- [ ] MUI theme compliance for frontend changes.
- [ ] Validation of Slack payload structures.

## Prompting Style
Request step-by-step synthesis. Use structured prompts that ask Gemini to analyze the `modules/` hierarchy and suggest improvements based on NestJS best practices. Require clear justifications for every suggested dependency.
