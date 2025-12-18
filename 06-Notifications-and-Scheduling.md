_# 06. Notifications and Scheduling

**Owner:** Manus AI
**Last Updated:** 2025-12-18
**Version:** 1.0

**Purpose:** This document details the architecture and logic for scheduling reminders and delivering notifications across multiple channels. It covers scheduling rules, message templates, the delivery pipeline, and recipient response handling.

---

## 1. Scheduling Rules

Reminders can be scheduled with a high degree of flexibility to meet various user needs. All scheduling is timezone-aware, based on the Creator's specified timezone.

*   **One-Time:** Send a reminder once at a specific date and time.
*   **Recurring:**
    *   **Daily:** Send at the same time every day.
    *   **Weekly:** Send on specific days of the week (e.g., every Monday and Wednesday).
    *   **Monthly:** Send on a specific day of the month (e.g., the 15th) or on a relative day (e.g., the last Friday of the month).
*   **Escalation Ladder:** Creators can define a sequence of reminders. For example:
    1.  Initial reminder 3 days before the due date.
    2.  Follow-up reminder 1 day after the due date if no response.
    3.  Final reminder 3 days after the due date.

## 2. Message Templates

Message templates will be used to ensure consistency and allow for easy modification. Templates will support basic personalization using variables.

| Channel | Template | Personalization Variables |
| :--- | :--- | :--- |
| **Email** | HTML email template with a clear call-to-action button. | `{{recipient.name}}`, `{{creator.name}}`, `{{reminder.title}}`, `{{response.link}}` |
| **WhatsApp** | Plain text message with the response link. | `{{recipient.name}}`, `{{creator.name}}`, `{{reminder.title}}`, `{{response.link}}` |
| **Telegram** | Plain text message with the response link. | `{{recipient.name}}`, `{{creator.name}}`, `{{reminder.title}}`, `{{response.link}}` |

## 3. Delivery Pipeline

The delivery pipeline is a sequence of steps managed by a Vercel Cron job that runs every minute.

1.  **Build Message:** The system queries for due reminders, selects the appropriate template, and personalizes it with the recipient's and reminder's data.
2.  **Send:** The message is sent to the recipient via the chosen channel's API (SendGrid for email, etc.).
3.  **Capture Provider Status:** The system records the `message_id` from the provider and listens for status updates (e.g., delivered, bounced, failed).
4.  **Retry Policy:** If a notification fails to send, the system will retry up to 3 times with an exponential backoff (1 minute, 5 minutes, 15 minutes).
5.  **Log/Audit:** Every step of the delivery process is logged in the `notifications` and `audit_logs` tables for tracking and debugging.

## 4. Recipient Response Capture

The system must be able to capture recipient responses from various channels.

*   **Link Click:** The primary method for all channels. The unique, tokenized link in the message leads to the response page.
*   **Telegram Bot Reply:** (Post-MVP) A Telegram bot could be used to allow recipients to reply directly within the Telegram app.
*   **WhatsApp Reply:** (Post-MVP) Similar to Telegram, a WhatsApp bot could process direct replies.

## 5. "Mark Done" and "Remind Me Later" Semantics

*   **Mark Done:** This is the primary success action. When a recipient clicks "Mark Done," the reminder is considered complete, and the Creator is notified.
*   **Remind Me Later:** This action allows the recipient to snooze the reminder. The system will send another reminder after a predefined interval (e.g., 24 hours).

## 6. Failure Modes and Recovery

| Failure Mode | Detection | Recovery |
| :--- | :--- | :--- |
| **Notification Provider API Down** | API calls fail with a 5xx error. | The retry policy is activated. If the provider is down for an extended period, an alert is sent to the development team. |
| **Invalid Recipient Address** | The provider returns a "bounced" or "invalid address" status. | The recipient is marked as invalid, and the Creator is notified. |
| **Cron Job Failure** | The cron job fails to run or complete. | A monitoring alert is triggered. The job will run again on its next scheduled interval. |

## 7. Testing Strategy for Scheduling

*   **Time Travel Tests:** The most critical part of testing the scheduling logic is the ability to simulate the passage of time. Tests will be written to:
    *   Create a reminder scheduled for a future time.
    *   Artificially advance the system clock.
    *   Verify that the reminder is sent at the correct simulated time.
*   **Timezone Tests:** Tests will cover various timezone scenarios to ensure reminders are sent at the correct local time for the Creator.

## Implementation Notes for AI Agents

*   **Use a Robust Scheduling Library:** While Vercel Cron triggers the job, use a library like `node-schedule` or similar within the backend function to handle complex recurrence rules.
*   **Abstract Notification Providers:** Create a common interface for all notification providers. This will make it easy to add new channels in the future.
*   **Focus on the Happy Path First:** Implement the one-time scheduling and email delivery first, then add recurring schedules and other channels.
*   **Acceptance Criteria:**
    *   A user can schedule a one-time and a recurring reminder, and it is sent at the correct time.
    *   Notifications are successfully delivered via Email, WhatsApp, and Telegram.
    *   The system correctly handles recipient responses ("Mark Done" and "Remind Me Later").
    *   The retry policy is triggered for failed deliveries.
