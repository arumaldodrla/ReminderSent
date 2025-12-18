# 10. Deployment, CI/CD, and Operations

**Owner:** Manus AI
**Last Updated:** 2025-12-18
**Version:** 1.0

**Purpose:** This document describes the infrastructure, deployment pipeline, and operational procedures for the ReminderSend platform. It provides AI agents with the necessary information to manage the application's lifecycle from development to production.

---

## 1. Environments

The platform uses a three-environment setup to ensure a safe and reliable path to production.

| Environment | URL | Database | Purpose |
| :--- | :--- | :--- | :--- |
| **Local** | `http://localhost:3000` | Local Supabase (Docker) | For individual feature development and testing. |
| **Staging** | `https://staging.remindersend.app` | Dedicated Staging Supabase Project | For integration testing and UAT. Mirrors the production environment. |
| **Production** | `https://app.remindersend.app` | Production Supabase Project | The live environment for end-users. |

## 2. Supabase Migrations Workflow

All database schema changes are managed through Supabase's migration system.

1.  **Development:** An AI agent generates a new migration file locally using the Supabase CLI (`supabase migration new <name>`).
2.  **Testing:** The migration is applied to the local Docker-based Supabase instance for testing.
3.  **Deployment:** When the feature branch is merged, the CI/CD pipeline automatically applies the new migration to the staging database. Applying migrations to production is a manual step performed by the lead AI agent after successful staging verification.
4.  **RLS Verification:** The CI/CD pipeline includes a script that verifies the RLS policies on the staging database after each migration.

## 3. Vercel Deployment Workflow (CI/CD)

The entire CI/CD process is automated using GitHub Actions and Vercel.

1.  **Trigger:** A new commit is pushed to a feature branch.
2.  **GitHub Actions Workflow:**
    *   **Lint & Type Check:** Runs ESLint and the TypeScript compiler.
    *   **Test:** Executes the full test suite (unit, component, and E2E).
    *   **Build:** Creates a production build of the Next.js application.
    *   **Deploy Preview:** Deploys the build to a unique preview URL on Vercel.
3.  **Merge to `main`:**
    *   The same workflow runs.
    *   On success, the code is automatically deployed to the **staging** environment.
4.  **Promote to Production:**
    *   After manual verification on staging, the lead AI agent promotes the staging deployment to **production** using the Vercel CLI or dashboard.

*   **Environment Variables & Secrets:** All secrets (API keys, etc.) are stored as environment variables in Vercel and exposed to the GitHub Actions workflow as needed.

## 4. Monitoring and Alerting

Proactive monitoring is in place to detect and report issues before they impact users.

*   **Sentry:** For real-time error tracking and reporting in the frontend and backend.
*   **Datadog:** For application performance monitoring (APM), infrastructure metrics, and custom dashboards.
*   **SLOs (Service Level Objectives):**
    *   **Availability:** 99.9%
    *   **API Latency (p95):** < 200ms
*   **Alerts:** Alerts are configured in Datadog to notify the on-call AI agent via a webhook if an SLO is breached or if there is a spike in errors.

## 5. Backup, Restore, and Disaster Recovery (DR)

*   **Backup:** Supabase is configured to take daily automated backups of the production database.
*   **Point-in-Time Recovery (PITR):** PITR is enabled, allowing for restoration to any point in the last 7 days.
*   **Restore:** The process for restoring from a backup will be documented and tested quarterly.
*   **Disaster Recovery (DR):** In the event of a full regional outage of Vercel or Supabase, the DR plan involves:
    1.  Restoring the Supabase database from the latest backup in a different region.
    2.  Re-deploying the Vercel application in the new region.
    3.  Updating DNS records to point to the new deployment.
    *   **RTO (Recovery Time Objective):** < 4 hours
    *   **RPO (Recovery Point Objective):** < 24 hours (due to daily backups)

## 6. Runbooks for Common Issues

Runbooks will be created for common operational issues to enable fast and consistent resolution.

| Issue | Runbook Summary |
| :--- | :--- |
| **Notification Sending Failures** | 1. Check the status of the notification provider (SendGrid, etc.). 2. Inspect the application logs for errors. 3. Retry the failed notifications. |
| **Webhook Failures** | 1. Check the webhook logs in Zoho for the request and response. 2. Inspect the application logs for errors. 3. Manually replay the failed webhook if necessary. |
| **Authentication Issues** | 1. Check the Supabase Auth logs. 2. Verify that the JWT signing keys have not been compromised. 3. Ask the user to try resetting their password. |

## Implementation Notes for AI Agents

*   **Infrastructure as Code (IaC):** While not required for the MVP, consider using a tool like Terraform to manage Supabase and Vercel configurations in code for future scalability.
*   **Automate Everything:** The goal is a fully automated, "push-button" deployment process. Write scripts to automate manual steps wherever possible.
*   **Test the DR Plan:** The disaster recovery plan is only useful if it is tested. Schedule regular DR drills to ensure the process works as expected.
*   **Acceptance Criteria:**
    *   The CI/CD pipeline is fully functional and runs on every commit.
    *   Deployments to staging and production are successful.
    *   Monitoring and alerting are configured in Sentry and Datadog.
    *   The backup and restore process is documented and tested.
