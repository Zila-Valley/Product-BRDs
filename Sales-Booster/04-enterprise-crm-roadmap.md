# Enterprise CRM Roadmap

This roadmap outlines the strategic phases to evolve the Sales Booster CRM from its current state into a fully-featured, enterprise-ready SaaS CRM product.

## Phase 1: Stabilization & Foundation (Months 1-2)
**Objective**: Solidify the existing codebase, add missing automated tests, and improve data integrity.

- **Automated Testing Suite**: Introduce xUnit for the backend and Jest for the frontend. Implement core unit tests for Services and Repositories.
- **Global Query Filters**: Implement EF Core Global Query Filters to enforce multi-tenancy based on `BusinessUnitId` or `ClientId` at the DB level, preventing cross-tenant data leaks.
- **Audit Logging**: Implement entity-level audit logging (Field A changed from X to Y by User Z) using EF Core Interceptors.
- **Data Validation & Deduplication**: Add strict unique constraints and logic to prevent duplicate Leads (by Phone/Email).
- **CI/CD Pipeline**: Setup automated build and deployment pipelines (e.g., GitHub Actions to Docker Hub).

## Phase 2: Core Sales Workflows (Months 3-4)
**Objective**: Fill the functional gaps in lead processing and task management.

- **Lead Assignment Engine**: Implement round-robin and rules-based automatic lead assignment to sales reps based on zip code or business unit.
- **Follow-up & Reminder System**: Dedicated Follow-up module. Sales reps can log calls/meetings and schedule next contact. Hangfire jobs send automated email/SMS reminders 1 hour before the follow-up.
- **Mobile Offline Sync**: Introduce SQLite caching and a sync queue to the React Native app, enabling agents to capture leads without internet.
- **Lead Timeline View**: Enhance the frontend Lead Details page to show a chronological timeline of all activities, chats, and status changes related to the lead.
- **CSV Data Import**: Build a robust, mapped CSV upload feature for importing bulk lead lists.

## Phase 3: The Deal Pipeline (Months 5-6)
**Objective**: Upgrade the system from a basic "Lead Tracker" to a "Revenue Forecaster".

- **Opportunity/Deal Management**: Introduce the concept of Deals/Opportunities attached to Clients/Leads.
- **Customizable Pipeline Stages**: Allow admins to define custom Deal Stages (e.g., Discovery -> Proposal -> Negotiation -> Closed Won).
- **Kanban Board**: Implement a drag-and-drop Kanban view for managing Deals across stages.
- **Quotation Generator**: Built-in tool using QuestPDF to generate customized, branded PDF quotes/proposals directly from the Deal record.

## Phase 4: Automation & Communication Integration (Months 7-8)
**Objective**: Automate repetitive tasks and centralize all customer communication inside the CRM.

- **Email Sync (IMAP/SMTP)**: Integrate with O365/GSuite so emails sent to/from leads automatically appear in the Lead Timeline.
- **WhatsApp API Integration**: Deep integration with WhatsApp Business API. Trigger automated WhatsApp templates on status changes.
- **Automated Workflows / Triggers**: Allow admins to create IF/THEN rules (e.g., IF Lead Status = 'Lost', THEN send survey email).

## Phase 5: Analytics and AI (Months 9-10)
**Objective**: Provide intelligent insights and predictive analytics.

- **Dynamic Report Builder**: Allow users to drag-and-drop columns to build custom reports and export them.
- **Lead Scoring**: Simple rule-based scoring (e.g., +10 points if email opened) to help reps prioritize hot leads.
- **Sales Forecasting Dashboards**: Predict monthly revenue based on weighted Deal pipeline probability.
- **Mobile Route Optimization**: Calculate and display the most optimal daily route mapping in the mobile app for scheduled client visits.

## Phase 6: Enterprise SaaS Readiness (Months 11-12)
**Objective**: Prepare the platform for multi-tenant SaaS commercialization.

- **Subscription & Billing**: Integrate Stripe/Razorpay for automated SaaS subscription billing.
- **Tenant Provisioning**: Fully automated tenant onboarding scripts.
- **Custom Fields (EAV)**: Allow tenants to create their own custom fields for Leads and Customers without altering the core database schema.
- **Advanced API Rate Limiting**: Protect the API with user-based rate limiting.
