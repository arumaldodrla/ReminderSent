# 15. Glossary of Terms

**Owner:** Manus AI
**Last Updated:** 2025-12-26
**Version:** 1.0

**Purpose:** This document provides a centralized glossary of all key terms, acronyms, and concepts used throughout the ReminderSend documentation. It ensures that all stakeholders—from AI agents to human developers to marketing teams—share a common vocabulary.

---

| Term | Definition |
| :--- | :--- |
| **Creator** | A registered user of the ReminderSend platform who creates and manages reminders. This is our primary user persona. |
| **Recipient** | An external contact who receives a reminder. Recipients do not have accounts and interact with the system via unique, secure links. |
| **Reminder** | The core object in the system. It represents a task or action that a Creator wants to remind a Recipient about. |
| **Reminder Delegation** | The core concept of the product. It refers to the act of offloading the responsibility of following up to the ReminderSend platform. |
| **Channel** | The method of delivery for a notification (e.g., `Email`, `WhatsApp`, `Telegram`). |
| **Schedule** | The set of rules that determines when a reminder's notifications are sent. This can be a one-time event or a recurring pattern. |
| **Escalation** | A multi-step sequence of notifications, often with increasing urgency or different channels, that is triggered if a Recipient does not respond. |
| **Response** | The action a Recipient takes on a reminder, such as marking it as "Done" or "Snooze". |
| **Organization** | The top-level account entity in our multi-tenant system. All data, including users and reminders, belongs to an organization. |
| **Multi-Tenancy** | The architectural principle of isolating the data of different organizations so that one customer cannot access another's data. |
| **RLS (Row-Level Security)** | The PostgreSQL feature used to enforce multi-tenancy at the database level. It is a critical part of our security model. |
| **tRPC** | A framework for creating fully type-safe APIs in TypeScript. It is the foundation of our backend-frontend communication. |
| **Zod** | A TypeScript-first schema declaration and validation library. We use it to validate all incoming data to the API. |
| **Supabase** | The managed service we use for our PostgreSQL database, authentication, and other backend services. |
| **Vercel** | The cloud platform we use for hosting our Next.js frontend and serverless backend functions. |
| **Idempotency** | An important property of an API endpoint or background job, meaning that performing the same operation multiple times has the same effect as performing it once. |
| **PRD (Product Requirements Document)** | The document that defines what the product should do, its features, and its requirements. |
| **SOP (Standard Operating Procedure)** | A document that outlines the step-by-step process for a recurring task, such as our AI-first delivery process. |
| **ERD (Entity-Relationship Diagram)** | A diagram that visually represents the tables in our database and the relationships between them. |
