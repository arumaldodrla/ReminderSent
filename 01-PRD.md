# 01. Product Requirements Document (PRD)

**Owner:** Manus AI
**Last Updated:** 2025-12-26
**Version:** 2.0

**Purpose:** This document provides the detailed product requirements for ReminderSend. It translates the strategic goals from the *00-Product-Vision-and-Business-Context.md* into actionable features, user stories, and technical specifications. This PRD is the primary guide for AI agents to build the product and for human stakeholders to understand its functionality.

---

## 1. The Core Objective: Automating Accountability

As defined in our product vision, ReminderSend exists to eliminate the mental overhead of following up with people. This document details the "what" and "how" of building our automated accountability engine.

### 1.1. The Problem in Detail

Professionals are burdened by the need to track and chase pending actions from clients, colleagues, and partners. This manual follow-up is a significant drain on productivity and a source of professional friction. Existing tools fail to address this specific pain point, as they are built for either personal task management or internal team collaboration, not for the nuanced task of external reminder delegation.

### 1.2. The Solution: A "Set It and Forget It" Delegation Platform

ReminderSend solves this problem by providing a simple, reliable, and intelligent platform for delegating reminders. Users can define a reminder once and trust the system to handle the entire follow-up process, from multi-channel delivery to escalation.

## 2. Target User Personas and Scenarios

| User Persona | Scenario |
| :--- | :--- |
| **Anna, the Freelance Designer** | Anna needs to get feedback from five different clients on her latest designs by Friday. She creates a single reminder in ReminderSend, adds all five clients as recipients, and schedules it to be sent via email. The system will automatically follow up with anyone who hasn't responded by Thursday. |
| **David, the Small Agency Owner** | David's team needs to submit their weekly timesheets every Friday. He sets up a recurring weekly reminder in ReminderSend that sends a WhatsApp message to each team member every Friday morning. He can see at a glance who has and hasn't confirmed submission. |
| **Maria, the B2B SaaS Account Manager** | Maria is onboarding a new enterprise customer. She creates a series of reminders linked to their Zoho CRM record to nudge the customer's admin to complete key onboarding steps, ensuring a smooth and timely setup process. |

## 3. Core Product Workflows

### 3.1. The Creator's Journey: Delegating a Reminder

1.  **Authentication:** The Creator logs into the ReminderSend mobile app (iOS or Android) or the secondary web dashboard.
2.  **Creation:** The Creator clicks "New Reminder" and fills in the required fields:
    *   **Title:** A clear, concise summary of the task (e.g., "Please Sign the Q4 Contract").
    *   **Description (Optional):** More detailed instructions or context.
    *   **Recipients:** One or more individuals, who can be added manually or synced from Zoho CRM.
3.  **Channel Selection:** For each recipient, the Creator selects the desired delivery channels (Email, WhatsApp, Telegram).
4.  **Scheduling:** The Creator defines the sending logic:
    *   **One-Time:** At a specific date and time.
    *   **Recurring:** Daily, weekly, or monthly with advanced options.
    *   **Escalation:** A multi-step sequence (e.g., "If no response in 3 days, send a follow-up via WhatsApp").
5.  **Confirmation:** The Creator saves the reminder, and it becomes active in the system.

### 3.2. The Recipient's Journey: The "One-Tap" Experience

1.  **Receives Notification:** The Recipient gets a personalized message on their chosen channel.
2.  **Takes Action:** The message contains a secure, unique link. Clicking it opens a clean, mobile-first webpage that requires **no login**.
3.  **Responds:** The Recipient has two primary options:
    *   **"Mark as Done":** Confirms completion of the task.
    *   **"Remind Me Later":** Snoozes the reminder for a predefined period (e.g., 24 hours).

### 3.3. The System's Role: Orchestration and Tracking

1.  **Delivery:** The system dispatches notifications at the scheduled times.
2.  **Tracking:** It tracks the status of each notification (Sent, Delivered, Failed) and each reminder (Pending, Completed).
3.  **Dashboard Updates:** The Creator's dashboard is updated in real-time to reflect the latest status of all reminders.
4.  **Escalation:** The system automatically executes the defined escalation rules for unanswered reminders.
5.  **Integration:** It syncs data with Zoho, such as marking a reminder complete when a linked invoice is paid in Zoho Books.

## 4. Feature Roadmap

| Feature Category | MVP (v0.1) | v1.0 (Post-Launch) | v2.0 (Future) |
| :--- | :--- | :--- | :--- |
| **Reminder Engine** | Create, schedule (one-time, recurring), and track reminders. Basic escalation rules. | Customizable reminder templates. Advanced recurrence rules (e.g., "every third Tuesday"). | AI-powered scheduling optimization (suggesting the best time to send). |
| **Delivery Channels** | Email, WhatsApp, Telegram. | SMS/iMessage for US customers (evaluation required). | Interactive voice response (IVR) calls for critical reminders. |
| **Recipient Experience** | Simple, link-based, no-login response page with "Done" and "Snooze" options. | Ability for recipients to add a note to their response. | Secure, two-way chat between the Creator and Recipient via the platform. |
| **Integrations** | Zoho Billing & Books (read-only sync for subscriptions and invoices). | Zoho CRM (contact sync) & Zoho Desk (ticket creation for escalations). | Deeper integration with payment gateways (Stripe, PayPal) and other business tools. |

## 5. Non-Functional Requirements

| Requirement | Target | Justification & Measurement |
| :--- | :--- | :--- |
| **UX Simplicity** | First reminder sent in < 60s | The core design philosophy is "Simple Surface, Complex Core." The UI must be intuitive. Measured by user testing. |
| **Internationalization (i18n)** | English & Spanish at launch | The frontend must be built using an i18n framework (e.g., `next-intl`) to support multiple languages. All UI strings must be externalized. |
| **Autonomous Maintenance** | Weekly self-revision | The system must include a scheduled weekly job to perform self-updates (dependencies, minor patches). See *16-Autonomous-Maintenance.md*. |
| **API Latency** | p95 < 200ms | Ensures a snappy and responsive user experience. Measured by Datadog APM. |
| **UI Load Time** | LCP < 2 seconds | Critical for user retention and satisfaction. Measured by Vercel Analytics and Lighthouse. |
| **Reliability / Uptime** | > 99.9% | Users must trust the system to send critical reminders. Measured by Datadog Synthetics. |
| **Security** | OWASP Top 10 Mitigated | The platform handles sensitive business communication and must be secure. Verified by security audits and automated scans. |
| **Accessibility** | WCAG 2.1 AA | Ensures the platform is usable by everyone. Verified by automated tools (`axe-core`) and manual testing. |

## 6. Out of Scope for MVP

*   Personal (self-reminding) reminders.
*   Advanced AI features beyond the defined escalation rules.
*   Advanced AI features beyond the defined escalation rules.
*   Direct payment processing within the platform.
*   Direct payment processing within the platform.

## Implementation Notes for AI Agents

*   **Reference the Vision:** Constantly refer back to the *00-Product-Vision-and-Business-Context.md* to ensure your implementation aligns with the core pillars of Simplicity, Reliability, Intelligence, and Integration.
*   **Prioritize the Core Loop:** The absolute priority is the end-to-end workflow: Create → Send → Respond → Track. All other features are secondary.
*   **Recipient Experience is Paramount:** The recipient-facing page must be exceptionally simple, fast, and reliable. It is a key differentiator.
*   **Acceptance Criteria for MVP:**
    *   A user can successfully create a reminder with at least one recipient and schedule it for a future date.
    *   The reminder is successfully delivered via Email, WhatsApp, and Telegram.
    *   A recipient can open the unique link from the notification and mark the reminder as "Done" without needing to log in.
    *   The Creator's dashboard accurately reflects the updated status of the reminder.
    *   The system correctly handles timezone differences for scheduling, based on the Creator's settings.
