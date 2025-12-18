# 01. Product Requirements Document (PRD)

**Owner:** Manus AI
**Last Updated:** 2025-12-18
**Version:** 1.0

**Purpose:** This document outlines the product requirements for ReminderSend, a SaaS platform for reminder delegation. It defines the problem, target users, features, and non-functional requirements to guide the development process.

---

## 1. Problem Statement

Professionals and businesses waste significant time and mental energy manually following up with clients, colleagues, and partners about pending tasks, payments, and deadlines. This manual process is inefficient, prone to error, and lacks a systematic way to track and escalate reminders. Existing task management tools are designed for personal productivity or internal team collaboration, not for the specific use case of delegating reminders to external parties.

## 2. Target Users & Jobs-to-be-Done (JTBD)

| Target User Persona | Job-to-be-Done (JTBD) |
| :--- | :--- |
| **Freelancers & Consultants** | Remind clients about invoices, contract signatures, and feedback deadlines. |
| **Small Business Owners** | Ensure employees submit reports, and clients pay on time. |
| **Account Managers (B2B SaaS)** | Nudge customers to complete onboarding steps or renew subscriptions. |
| **Real Estate Agents** | Coordinate document submissions and appointment confirmations with clients. |

## 3. Core Workflows

### 3.1. Reminder Creation and Scheduling

1.  A **Creator** logs into the ReminderSend platform.
2.  They create a new reminder, specifying a title, description, and one or more **Recipients**.
3.  The Creator selects the delivery channels (Email, WhatsApp, Telegram) for each recipient.
4.  They set the reminder schedule: one-time, recurring (daily, weekly, monthly), or based on a specific event (e.g., 3 days before an invoice is due).
5.  The Creator defines escalation rules (e.g., if no response after 2 days, send a follow-up reminder).

### 3.2. Reminder Delivery and Recipient Experience

1.  The system sends the reminder to the Recipient via the selected channels at the scheduled time.
2.  The Recipient receives a message with a secure, unique link.
3.  Clicking the link opens a mobile-first web page that requires no login.
4.  The Recipient can perform a primary action, such as "Mark as Done," or a secondary action like "Remind Me Later."

### 3.3. Tracking and Escalation

1.  The Creator tracks the status of all reminders on their dashboard (Sent, Delivered, Opened, Responded, Completed).
2.  When a Recipient marks a reminder as done, the status is updated in real-time.
3.  If a reminder is not completed by its due date, the system automatically triggers the predefined escalation rules.

## 4. Feature List

| Feature Category | MVP (v0.1) | v1.0 | v2.0 |
| :--- | :--- | :--- | :--- |
| **Reminders** | Create, schedule (one-time, recurring), and track reminders. | Customizable reminder templates. | AI-powered scheduling optimization. |
| **Channels** | Email, WhatsApp, Telegram. | SMS/iMessage for US customers. | Interactive voice response (IVR) reminders. |
| **Recipient UX** | Link-based, no-login response page. | Ability to add notes to responses. | Two-way chat with the Creator via the platform. |
| **Integrations** | Zoho Billing & Books (read-only). | Zoho CRM & Desk (read/write). | Deeper integration with payment gateways (e.g., Stripe, PayPal). |
| **AI** | Basic escalation rules. | AI-powered completion detection (e.g., analyzing email replies). | Predictive analytics on response likelihood. |

## 5. Non-Functional Requirements

| Requirement | Target | Notes |
| :--- | :--- | :--- |
| **API Latency** | p95 < 200ms | For all core API endpoints. |
| **UI Load Time** | < 2 seconds | For the main dashboard and reminder creation pages. |
| **Uptime** | > 99.9% | For all user-facing services. |
| **Accessibility** | WCAG 2.1 AA | Ensuring the platform is usable by people with disabilities. |
| **Localization** | Timezone-aware scheduling. | UI text in English only for MVP. |
| **Auditability** | All actions logged. | For compliance and security purposes. |

## 6. Pricing & Plan Assumptions

| Plan | Price (USD/month) | Key Features |
| :--- | :--- | :--- |
| **Free** | $0 | 10 reminders/month, Email channel only. |
| **Pro** | $29 | 100 reminders/month, all channels, basic integrations. |
| **Business** | $99 | Unlimited reminders, advanced integrations, priority support. |

## 7. Data Retention & Deletion

*   **Retention:** User data will be retained as long as the account is active. Inactive accounts (no login for 12 months) will be marked for deletion.
*   **Deletion:** Users can request to delete their account and all associated data at any time. The deletion process will be completed within 30 days, in compliance with GDPR.

## 8. Out of Scope for MVP

*   Personal (self-reminding) reminders.
*   A native mobile application for Creators.
*   Advanced AI features beyond basic escalation.
*   Direct payment processing within the platform.
*   UI localization beyond English.

## Implementation Notes for AI Agents

*   **Focus on the Core Loop:** The highest priority is the end-to-end flow of creating a reminder, sending it, and tracking the response. All other features are secondary.
*   **Recipient Experience is Key:** The recipient-facing page must be extremely simple and fast. The goal is a "one-tap done" experience.
*   **Acceptance Criteria:**
    *   A user can successfully create and send a reminder via Email, WhatsApp, and Telegram.
    *   A recipient can open the reminder link and mark it as complete without logging in.
    *   The creator can see the updated status on their dashboard.
    *   The system correctly handles timezone differences for scheduling.
