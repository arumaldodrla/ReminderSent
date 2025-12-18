# 13. Open Questions, Assumptions, and Decisions

**Owner:** Manus AI
**Last Updated:** 2025-12-18
**Version:** 1.0

**Purpose:** This document logs all open questions, assumptions, decisions, and conflict resolutions made during the documentation and development process. It serves as a single source of truth for clarifying ambiguities and tracking the project's trajectory.

---

## 1. Assumptions

*   **A-01: AI Agent Capability:** It is assumed that the Google Antigravity AI agents possess the full capabilities to understand and execute the instructions in this documentation pack, including complex coding, testing, and deployment tasks.
*   **A-02: Metronic License:** It is assumed that a valid and appropriate license for the Metronic UI framework has been or will be procured.
*   **A-03: Zoho and Authorize.net Accounts:** It is assumed that the necessary Zoho and Authorize.net accounts and API credentials have been or will be provisioned.

## 2. Open Questions

*   **Q-01: iMessage/SMS for US Customers:** The documentation mentions evaluating iMessage/SMS for US customers. A decision is needed on whether this is an MVP feature or a post-MVP enhancement. The initial implementation will focus on Email, WhatsApp, and Telegram.

## 3. Decisions

*   **D-01: Primary AI Model:** Claude Sonnet 4.5 will be the primary AI model for development, with GPT-4 as a secondary/backup option, as suggested in the research documentation.
*   **D-02: Initial Notification Channels:** The MVP will focus on Email, WhatsApp, and Telegram for notifications, as these are consistently mentioned as the core channels.

## 4. Conflicts & Resolutions

*   **C-01: Notification Providers:**
    *   **Conflict:** The `00-README-START-HERE.md` and `Reminder-SaaS-Research-And-Documentation.md` files mention different sets of notification providers. The former lists Firebase, SendGrid, and Twilio, while the latter also includes WhatsApp and Telegram, and the main prompt specifies Email, WhatsApp, and Telegram.
    *   **Resolution:** The MVP will prioritize Email (SendGrid), WhatsApp, and Telegram. Firebase will be used for push notifications if a mobile app is developed in the future. Twilio will be considered for SMS capabilities post-MVP.
