# Telegram Sender SaaS

## Technical Specification & Product Requirements Document

**Version:** 1.0  
**Project Type:** Multi-Tenant SaaS  
**Development Strategy:** Local First, Production VPS Later  
**Primary Goal:** Build a commercial Telegram customer communication platform.

---

# 1. Project Overview

Telegram Sender is a multi-tenant SaaS platform that enables businesses to manage their Telegram bots, organize customer contacts, create communication campaigns, automate customer interactions, and analyze campaign performance through a centralized dashboard.

The platform must support multiple independent customers. Each customer must have an isolated workspace, independent bot configurations, contacts, campaigns, and subscription information.

The product will initially be developed and tested locally, then deployed to a VPS for production.

## 1.1 Product Goals

- Provide a centralized Telegram bot management platform.
- Allow customers to connect their own Telegram bots using bot tokens.
- Manage contacts who have interacted with connected bots.
- Create and manage permission-based communication campaigns.
- Support scheduled messages and automated replies.
- Provide campaign analytics.
- Introduce subscription plans and usage limits.
- Support commercial operation as a subscription-based SaaS.

## 1.2 Target Customers

- Online stores.
- Telegram channel and group administrators.
- Small and medium-sized businesses.
- Digital marketing agencies.
- Customer support teams.
- Businesses using Telegram for customer communication.

## 1.3 Product Constraints

The platform must use the official Telegram Bot API.

It must not implement unsolicited bulk private messaging, unauthorized account automation, restriction evasion, or mechanisms designed to bypass Telegram anti-spam protections.

Messages must be sent only where Telegram permits them, with appropriate user interaction and consent.

---

# 2. Technology Stack

## Frontend

- Vue 3
- Vite
- TypeScript
- Vue Router
- Pinia
- SCSS or Tailwind CSS
- Lucide Vue Next
- Vue Sonner for notifications

## Backend

- Node.js
- Express.js
- TypeScript
- REST API
- Zod for request validation
- JWT-based authentication
- bcrypt or Argon2 for password hashing

## Database

- PostgreSQL
- Prisma ORM

## Background Processing

- Redis
- BullMQ

## Telegram Integration

- Official Telegram Bot API
- Webhook-based updates
- Telegram Bot Token authentication

## Development and Deployment

- Docker
- Docker Compose
- Git
- Environment-based configuration
- VPS deployment for production

Use stable, mutually compatible package versions and commit the lockfiles.

---

# 3. Architecture

Use a Modular Monolith architecture.

Do not start with microservices.

The backend must have independently organized modules with clear responsibilities.

Main architecture:

1. Vue Frontend
2. Express REST API
3. Authentication and Authorization
4. PostgreSQL Database
5. Redis Queue
6. Background Workers
7. Telegram Bot API
8. Admin Management System

The frontend must never communicate directly with PostgreSQL, Redis, or private Telegram credentials.

All business operations must pass through the backend.

---

# 4. Multi-Tenant Architecture

Multi-tenancy is a fundamental requirement.

Each customer must have an isolated workspace.

A workspace owns its:

- Telegram bots
- Contacts
- Campaigns
- Templates
- Automations
- Subscription
- Usage records
- Analytics

Every tenant-owned database entity must include a workspaceId directly or through a rigorously enforced parent relationship.

Backend authorization must validate workspace membership on every relevant request.

Never rely exclusively on frontend route protection.

Prevent cross-tenant data access in all CRUD operations, queries, background jobs, and analytics.

Use database constraints and indexes to reinforce tenant isolation.

---

# 5. User Roles

Implement the following roles:

## Platform Admin

Can:

- View registered users.
- Manage workspaces.
- Manage subscription plans.
- View platform usage.
- Suspend or reactivate accounts.
- View operational logs.
- Manage system-wide settings.

## Workspace Owner

Can:

- Manage workspace settings.
- Connect and remove Telegram bots.
- Manage contacts.
- Create campaigns.
- Manage subscriptions.
- Invite team members.
- Assign workspace permissions.

## Workspace Member

Permissions depend on assigned access level.

Initial permission categories:

- View Dashboard
- Manage Bots
- Manage Contacts
- Manage Campaigns
- View Analytics
- Manage Settings

---

# 6. Functional Modules

## 6.1 Authentication

Required functionality:

- User registration
- User login
- Logout
- Password hashing
- Password reset
- Access token validation
- Refresh token rotation
- Protected routes

Authentication must be secure and independent from workspace authorization.

## 6.2 Workspace Management

Users must be able to:

- Create a workspace.
- Update workspace information.
- View workspace details.
- Invite members.
- Manage member permissions.
- Switch between workspaces when authorized.

## 6.3 Telegram Bot Management

Customers connect their own bots.

Connection process:

1. Customer creates a bot through Telegram BotFather.
2. Customer obtains the bot token.
3. Customer enters the token in the dashboard.
4. Backend validates the token using Telegram getMe.
5. Backend securely encrypts and stores the token.
6. Backend registers the webhook.
7. Backend starts receiving Telegram updates.
8. Dashboard displays connection status.

Bot management features:

- Connect bot.
- Disconnect bot.
- View bot information.
- View connection status.
- Revalidate bot credentials.
- Configure webhook.
- View recent bot errors.

Never expose complete bot tokens to the frontend after initial submission.

Use encrypted secret storage and a separate encryption key.

## 6.4 Contact Management

The system must automatically register users who interact with connected bots.

Contact fields:

- id
- workspaceId
- botId
- telegramUserId
- username
- firstName
- lastName
- languageCode
- status
- createdAt
- updatedAt

Contact features:

- Contact list
- Search
- Filtering
- Tags
- Segmentation
- Contact details
- Interaction history
- Consent management
- Unsubscribe functionality

Do not treat imported or discovered usernames as permission to send private messages.

## 6.5 Campaign Management

Customers must be able to create communication campaigns.

Campaign creation process:

1. Select a connected bot.
2. Enter campaign name.
3. Select an eligible audience.
4. Select a message template or create a message.
5. Preview the message.
6. Send a test message.
7. Schedule or start the campaign.
8. Monitor campaign progress.

Campaign fields:

- id
- workspaceId
- botId
- name
- message
- status
- scheduledAt
- startedAt
- completedAt
- createdAt

Campaign statuses:

- Draft
- Scheduled
- Running
- Paused
- Completed
- Failed
- Cancelled

Only eligible recipients with the required interaction and permission conditions can receive messages.

## 6.6 Message Templates

Features:

- Create templates.
- Edit templates.
- Delete templates.
- Preview templates.
- Support Telegram formatting.
- Reuse templates across campaigns.

## 6.7 Scheduled Messages

Use BullMQ for background processing.

Requirements:

- Schedule campaigns.
- Track individual delivery attempts.
- Handle temporary API failures.
- Respect Telegram rate limits.
- Prevent duplicate message execution.
- Support safe retries.
- Record delivery results.

Do not retry permanently invalid requests or messages to users who have blocked the bot.

## 6.8 Automation

Initial automation features:

- Welcome messages.
- Keyword-based responses.
- Default replies.
- Business-hours responses.
- Simple conditional workflows.

Automation must only respond to valid Telegram interactions.

## 6.9 Analytics

Dashboard metrics:

- Total contacts.
- Active contacts.
- Connected bots.
- Total campaigns.
- Successful deliveries.
- Failed deliveries.
- Blocked or unsubscribed contacts.
- Campaign activity over time.

Do not present message delivery as proof of reading or engagement.

Only display engagement metrics when supported by actual Telegram events.

## 6.10 Subscription Management

Initial subscription architecture:

Starter:

- Limited number of bots.
- Limited contacts.
- Basic campaigns.
- Basic analytics.

Pro:

- Higher resource limits.
- Advanced campaign management.
- More bots.
- Advanced analytics.

Business:

- Team management.
- Higher resource limits.
- Advanced automations.
- API access.

Plan limits must be configurable from the Admin Panel.

Initially, subscriptions can be activated manually by the platform administrator.

Payment gateway integration can be implemented later.

---

# 7. Database Entities

Initial Prisma models:

1. User
2. Workspace
3. WorkspaceMember
4. TelegramBot
5. Contact
6. ContactTag
7. Consent
8. Campaign
9. CampaignRecipient
10. MessageTemplate
11. Automation
12. Subscription
13. Plan
14. UsageRecord
15. AuditLog

Use appropriate enums for:

- UserRole
- WorkspaceRole
- BotStatus
- ContactStatus
- CampaignStatus
- RecipientStatus
- SubscriptionStatus
- ConsentStatus

All primary entities should use consistent ID conventions.

Use timestamps, foreign keys, unique constraints, and indexes.

Telegram user IDs must be stored using a type capable of safely representing Telegram's full integer range, such as PostgreSQL BIGINT.

Prisma schema must be designed before implementing business endpoints.

---

# 8. API Structure

Use versioned REST endpoints.

Base URL:

/api/v1

Authentication:

POST /auth/register  
POST /auth/login  
POST /auth/refresh  
POST /auth/logout  
GET /auth/me

Workspaces:

GET /workspaces  
POST /workspaces  
GET /workspaces/:id  
PATCH /workspaces/:id  
GET /workspaces/:id/members  
POST /workspaces/:id/members

Telegram:

POST /workspaces/:id/bots  
GET /workspaces/:id/bots  
GET /workspaces/:id/bots/:botId  
DELETE /workspaces/:id/bots/:botId  
POST /workspaces/:id/bots/:botId/validate

Contacts:

GET /workspaces/:id/contacts  
GET /workspaces/:id/contacts/:contactId  
PATCH /workspaces/:id/contacts/:contactId  
DELETE /workspaces/:id/contacts/:contactId

Campaigns:

GET /workspaces/:id/campaigns  
POST /workspaces/:id/campaigns  
GET /workspaces/:id/campaigns/:campaignId  
PATCH /workspaces/:id/campaigns/:campaignId  
POST /workspaces/:id/campaigns/:campaignId/start  
POST /workspaces/:id/campaigns/:campaignId/pause  
POST /workspaces/:id/campaigns/:campaignId/cancel

Analytics:

GET /workspaces/:id/analytics/overview  
GET /workspaces/:id/analytics/campaigns

Subscriptions:

GET /workspaces/:id/subscription  
GET /plans

Webhook:

POST /webhooks/telegram/:botWebhookId

Every endpoint must have proper authentication, authorization, validation, error handling, and rate limiting.

---

# 9. Frontend Requirements

## Design System

The primary design direction is Dark and Modern.

Requirements:

- Dark mode as default.
- Light mode support as an optional preference.
- Minimal and professional UI.
- Responsive design.
- Consistent spacing and typography.
- Reusable components.
- Accessible forms and controls.
- Clear loading, empty, and error states.

## Internationalization

The platform must support Persian and English from the beginning.

Persian:

- RTL layout.
- Persian translations.
- Appropriate Persian typography.
- Correct alignment and navigation behavior.

English:

- LTR layout.
- English translations.
- Correct layout direction.

Do not hardcode UI strings inside components.

Use a dedicated translation system such as Vue I18n.

The selected language must be stored in the user profile or local preference.

Support locale-aware dates, numbers, and currency.

## Main Pages

Public:

- Login
- Register
- Forgot Password

Application:

- Overview
- Bots
- Contacts
- Campaigns
- Campaign Details
- Templates
- Automations
- Analytics
- Subscription
- Settings
- Team Management

Admin:

- Admin Overview
- Users
- Workspaces
- Plans
- Subscriptions
- Usage
- Audit Logs
- System Settings

---

# 10. Security Requirements

Security must be implemented from the beginning.

Mandatory requirements:

- Password hashing using Argon2id or bcrypt.
- Encrypted Telegram bot tokens.
- Secure session and refresh token management.
- Input validation using Zod.
- Authentication middleware.
- Workspace authorization middleware.
- Rate limiting.
- CORS configuration.
- Secure HTTP headers.
- Request logging without secrets.
- Webhook secret validation.
- Protection against cross-tenant access.
- Database backups.
- Audit logging for sensitive operations.

Never commit .env files or API tokens to Git.

Provide a .env.example containing placeholder values only.

---

# 11. Local Development Environment

The first version must run locally.

Required services:

- Frontend development server.
- Backend development server.
- PostgreSQL.
- Redis.
- BullMQ Worker.

Use Docker Compose for PostgreSQL and Redis.

Development environment must support:

- Database migrations.
- Prisma Client generation.
- Seed data.
- Local webhook testing through a secure public tunnel when necessary.
- Local API testing.

Do not require a production VPS during the first development phase.

---

# 12. Deployment Strategy

After the MVP is stable:

1. Provision a VPS.
2. Install Docker and Docker Compose.
3. Configure PostgreSQL.
4. Configure Redis.
5. Deploy the backend.
6. Deploy the frontend.
7. Configure HTTPS.
8. Configure Telegram webhooks.
9. Configure backups.
10. Configure monitoring.

Production secrets must be separate from local development secrets.

---

# 13. Development Phases

Phase 1: Project Foundation

- Repository setup.
- Frontend initialization.
- Backend initialization.
- Database configuration.
- Prisma setup.
- Authentication.
- Workspace architecture.

Phase 2: Telegram Integration

- Bot token connection.
- Token encryption.
- getMe validation.
- Webhook registration.
- Update handling.
- Contact registration.

Phase 3: Campaign Management

- Campaign CRUD.
- Message templates.
- Audience selection.
- BullMQ integration.
- Scheduling.
- Delivery tracking.

Phase 4: Automation and Analytics

- Auto Reply.
- Automation rules.
- Dashboard analytics.
- Campaign reporting.

Phase 5: SaaS Monetization

- Plans.
- Subscriptions.
- Usage tracking.
- Admin dashboard.
- Manual subscription activation.

Phase 6: Production

- Testing.
- Security review.
- Docker deployment.
- VPS setup.
- Monitoring.
- Public launch.

---

# 14. MVP Acceptance Criteria

The MVP is considered functional when:

1. A user can register and log in.
2. A user can create a workspace.
3. Workspace data is isolated.
4. A user can connect a valid Telegram bot token.
5. Invalid tokens are rejected.
6. Bot updates are received through webhooks.
7. Interacting users are registered as contacts.
8. Contacts can be searched and organized.
9. A campaign can be created.
10. A campaign can send messages to eligible recipients.
11. Campaigns can be scheduled.
12. Delivery results are recorded.
13. Automation rules can be configured.
14. Analytics display actual recorded results.
15. Admin can manage subscription plans.
16. Persian and English interfaces work correctly.
17. The application runs locally using documented setup instructions.

---

# 15. Development Rules for AI Coding Agents

When implementing this project:

- Follow this specification as the primary product reference.
- Do not implement the entire application in one step.
- Work phase by phase.
- Do not skip database design.
- Use TypeScript strictly.
- Avoid unnecessary abstractions.
- Keep modules independent.
- Use reusable frontend components.
- Keep business logic out of route handlers.
- Validate all incoming data.
- Handle errors consistently.
- Never trust client-provided workspace IDs without authorization checks.
- Never expose Telegram bot tokens.
- Write tests for important business logic.
- Update README.md after architectural changes.
- Do not introduce new dependencies without a clear reason.
- Do not silently change established architecture decisions.
- Keep the application runnable after every development phase.

Before starting each phase:

1. Review the existing implementation.
2. Identify missing requirements.
3. Propose the implementation plan.
4. Implement the phase.
5. Run relevant tests.
6. Report completed work and remaining tasks.

Do not move to the next phase while critical requirements of the current phase are incomplete.

---

# 16. Initial Development Priority

The first implementation target is Phase 1.

Do not start Campaign Management, Analytics, AI features, or Payment Integration yet.

First deliver:

- Working Vue frontend.
- Working Express backend.
- PostgreSQL connection.
- Prisma schema.
- Authentication.
- Workspace creation.
- Multi-tenant authorization.

The result must be a stable foundation for all subsequent development.

**End of Specification — Telegram Sender SaaS V1.0**
