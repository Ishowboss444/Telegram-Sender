# AGENTS.md
# Telegram Sender SaaS — AI Agent Development Rules

## 1. General Rules

- Read `README.md`, `PRD.md`, and `ARCHITECTURE.md` before starting development, if available.
- Treat project documentation as the primary source of truth.
- Understand the existing codebase before modifying anything.
- Do not assume that a feature is missing without inspecting the existing implementation.
- Never rewrite working code unnecessarily.
- Do not introduce architectural changes without explaining their necessity.
- Work incrementally and keep the application runnable.
- Prioritize maintainability, readability, security, and simplicity.
- Avoid unnecessary abstractions and overengineering.
- Never implement multiple unrelated features in one task.

## 2. Technology Stack

### Frontend
- Vue 3
- Vite
- TypeScript
- Composition API
- Pinia
- Vue Router
- Vue I18n
- SCSS
- Lucide Vue Next

### Backend
- Node.js
- Express.js
- TypeScript
- Prisma ORM
- PostgreSQL
- Zod
- Redis
- BullMQ

Do not replace these technologies without explicit approval.

## 3. Code Quality

- Use TypeScript strictly.
- Avoid `any` whenever possible.
- Prefer explicit types and interfaces.
- Use meaningful variable, function, and component names.
- Keep functions small and focused.
- Avoid duplicated business logic.
- Avoid deeply nested conditional statements.
- Use early returns when appropriate.
- Remove unused imports, variables, and dependencies.
- Do not leave debugging code in production files.
- Do not use `console.log` as a permanent logging solution.
- Add comments only when they explain non-obvious behavior.

## 4. Frontend Rules

- Use Vue 3 Composition API with `<script setup lang="ts">`.
- Keep components focused on a single responsibility.
- Separate business logic from presentation.
- Use Pinia for shared application state.
- Use Vue Router for navigation.
- Use reusable components for common UI elements.
- Avoid duplicating API requests across components.
- Centralize API communication in service modules.
- Handle loading, success, empty, and error states.
- Avoid unnecessary watchers.
- Do not manipulate the DOM directly unless necessary.
- Use semantic HTML and accessible controls.

### Design Rules

The application's default appearance must be Dark Mode.

- Use a consistent design system.
- Keep the interface minimal and professional.
- Follow a mobile-first responsive approach.
- Use consistent spacing, typography, and colors.
- Avoid excessive gradients, shadows, and decorative effects.
- Prefer reusable UI components.
- Maintain visual consistency across all dashboard pages.
- Do not introduce a new visual style for individual pages.

### Internationalization

The application must support Persian and English.

- Use Vue I18n.
- Never hardcode user-facing text inside components.
- Store translations in dedicated locale files.
- Support RTL for Persian.
- Support LTR for English.
- Avoid CSS assumptions that break when direction changes.
- Prefer logical CSS properties such as `margin-inline-start` and `padding-inline-end`.
- Ensure layouts work in both languages.
- Format dates and numbers according to the selected locale.

## 5. Backend Rules

- Keep business logic outside route handlers.
- Use a modular architecture.
- Separate routes, controllers, services, schemas, and types.
- Validate all external input using Zod.
- Use centralized error handling.
- Use appropriate HTTP status codes.
- Never expose internal exceptions to API clients.
- Keep API responses consistent.
- Use async/await consistently.
- Avoid unnecessary database queries.
- Use transactions when multiple related database operations must succeed or fail together.
- Implement pagination for potentially large datasets.
- Never trust client-provided identifiers without authorization checks.

## 6. Multi-Tenant Security

Multi-tenancy is a core architectural requirement.

- Every tenant-owned resource must belong to a workspace.
- Verify workspace membership before accessing workspace resources.
- Never trust a workspace ID supplied by the frontend.
- Derive authorized workspace access from the authenticated user and database relationships.
- Enforce tenant isolation in every service and query.
- Prevent users from reading or modifying another workspace's resources.
- Apply tenant isolation to background jobs and analytics.
- Test cross-workspace access explicitly.
- Never expose another tenant's data through error messages or API responses.

Security takes priority over convenience.

## 7. Database and Prisma Rules

- Prisma is the only ORM used in the application.
- Keep the Prisma schema organized.
- Use appropriate relations and foreign keys.
- Add indexes for frequently queried fields.
- Use unique constraints where necessary.
- Use consistent naming conventions.
- Include `createdAt` and `updatedAt` where appropriate.
- Never modify the production database schema manually.
- Use Prisma migrations for schema changes.
- Never delete or reset existing database data without explicit approval.
- Review generated migration SQL before applying destructive changes.
- Use transactions for operations requiring data consistency.

## 8. Telegram Integration Rules

- Use the official Telegram Bot API.
- Customers connect bots using their own BotFather tokens.
- Validate tokens through Telegram's `getMe` method.
- Encrypt bot tokens before database storage.
- Never return complete bot tokens to the frontend.
- Never expose tokens in logs, errors, or API responses.
- Validate incoming webhook requests.
- Handle Telegram API errors properly.
- Respect Telegram rate limits.
- Implement safe retries for temporary failures.
- Prevent duplicate processing of webhook updates.
- Maintain bot connection status.
- Handle blocked bots and users who have stopped interacting.
- Never implement unauthorized account automation.
- Never implement unsolicited bulk private messaging or mechanisms for bypassing Telegram restrictions.

## 9. Background Jobs

Use BullMQ for asynchronous operations.

- Do not execute long-running tasks inside HTTP request handlers.
- Keep job processors separate from API routes.
- Make jobs idempotent where possible.
- Prevent duplicate campaign execution.
- Handle temporary failures safely.
- Avoid infinite retry loops.
- Record job status and errors.
- Support graceful shutdown of workers.
- Validate workspace ownership before processing tenant-specific jobs.
- Never put unencrypted secrets in job payloads.

## 10. Authentication and Authorization

- Hash passwords using Argon2id or an approved secure alternative.
- Never store plaintext passwords.
- Protect private API endpoints.
- Validate authentication tokens.
- Implement refresh token rotation if using refresh tokens.
- Revoke sessions when appropriate.
- Apply role-based permissions.
- Separate platform administration from workspace permissions.
- Do not rely on frontend route guards for security.
- Validate authorization on the backend for every sensitive operation.

## 11. Error Handling and Logging

- Use a centralized error-handling middleware.
- Create meaningful custom application errors.
- Return consistent error response structures.
- Never expose stack traces in production responses.
- Log unexpected server errors.
- Avoid logging credentials, bot tokens, passwords, or sensitive customer data.
- Include useful request and job identifiers in logs.
- Handle expected external API errors separately from internal application errors.

## 12. Testing Rules

- Write tests for important business logic.
- Test authentication and authorization.
- Test workspace isolation.
- Test Telegram token validation.
- Test webhook processing.
- Test campaign scheduling.
- Test retry behavior.
- Test subscription limits.
- Test API validation.
- Test both Persian RTL and English LTR interfaces.
- Do not claim a feature works without verifying it.
- Run relevant tests after modifying code.

## 13. Environment and Configuration

- Keep environment-specific configuration in environment variables.
- Maintain a `.env.example`.
- Never commit `.env` files.
- Validate required environment variables at startup.
- Keep development and production configurations separate.
- Never hardcode API keys, credentials, or encryption secrets.
- Use Docker Compose for local PostgreSQL and Redis services.

## 14. Git and Version Control

- Do not force-push.
- Do not delete branches.
- Do not rewrite Git history without explicit approval.
- Do not discard uncommitted user changes.
- Inspect `git status` before making changes.
- Never commit secrets or generated sensitive files.
- Use descriptive commit messages.
- Keep changes focused and reviewable.
- Do not automatically create commits unless requested.

## 15. AI Agent Workflow

Before implementing any task:

1. Read relevant project documentation.
2. Inspect the existing implementation.
3. Identify dependencies and affected modules.
4. Explain the implementation plan briefly.
5. Identify potential security and compatibility concerns.

During implementation:

1. Make small, focused changes.
2. Follow existing naming and coding conventions.
3. Avoid unnecessary dependencies.
4. Preserve existing functionality.
5. Update documentation when necessary.

After implementation:

1. Run TypeScript checks.
2. Run relevant tests.
3. Run linting when configured.
4. Review changed files.
5. Report what was implemented.
6. Report any known limitations.
7. Identify remaining work.

Never report successful tests unless they were actually executed.

## 16. Dependency Management

- Do not install dependencies without a clear technical reason.
- Prefer existing packages over introducing alternatives.
- Avoid packages that are unmaintained or unnecessary.
- Keep dependencies compatible with the existing stack.
- Do not update unrelated packages during feature development.
- Never silently replace an existing library.

## 17. Documentation Rules

Keep documentation updated when architectural or behavioral changes occur.

Important documents:

- `README.md`: Project setup and general information.
- `PRD.md`: Product requirements.
- `ARCHITECTURE.md`: Technical architecture.
- `AGENTS.md`: AI coding rules.
- `CHANGELOG.md`: Important changes.
- API documentation: Endpoints, parameters, and responses.

## 18. Final Development Principle

Build a reliable commercial SaaS product, not a temporary prototype.

Prioritize:

1. Security.
2. Correctness.
3. Maintainability.
4. Simplicity.
5. Performance.
6. User experience.
7. Scalability.

Do not sacrifice security or data integrity for faster implementation.

Every feature must be designed with multiple independent customers in mind.

**End of AGENTS.md**
