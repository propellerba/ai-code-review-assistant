## Purpose
This document enables OpenAI to provide fast, patch-oriented, and execution-focused code updates for the Code Review Assistant.

## Project Context
A containerized AI review system (React 19/NestJS) utilizing PostgreSQL and AWS services. Focus is on speed, reliability, and automated feedback loops.

## Repo Rules
- Segmented structure: `frontend/` and `backend/` directories.
- Deployments are triggered by `.github/workflows/deploy-segments.yml`.
- Maintain 100% test coverage for the `analysis/` module.
- Use Docker for local environment parity.

## Segment Rules
- **backend/**: Focus on lightweight controllers and service-level logic. Use BullMQ for all async LLM orchestration.
- **frontend/**: High-performance React 19 SPA. Use TanStack Query for optimistic updates in the review history dashboard.

## Do
- Provide concise code patches and diffs.
- Include "Next Actions" for manual verification or further tasks.
- Use `import.meta.env` with validation wrappers in the frontend.
- Implement immediate `202 Accepted` responses for GitHub webhooks.

## Don't
- Do not over-engineer; prioritize working code over abstract patterns unless requested.
- Do not commit secrets; use AWS Secrets Manager integration.
- Never bypass TypeScript strict checks with `@ts-ignore`.

## PR Checklist
- [ ] Code builds locally in Docker.
- [ ] Patch addresses specific issue without side effects.
- [ ] Environment variables updated in `deploy-segments.yml` if necessary.
- [ ] Slack notifications verified for new event types.

## Prompting Style
Use direct, instruction-heavy prompts. Provide specific file snippets and ask for direct patches. Focus on fast verification steps and clear execution paths for feature additions.
