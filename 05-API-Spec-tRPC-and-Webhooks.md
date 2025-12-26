_# 05. API Specification: tRPC and Webhooks

**Owner:** Manus AI
**Last Updated:** 2025-12-26
**Version:** 2.0

**Purpose:** This document provides the definitive technical specification for the ReminderSend API. It details the tRPC procedures and incoming webhooks that power the platform, defining all inputs, outputs, validation schemas (Zod), authorization rules, and error handling.

---

## 1. API Guiding Principles

*   **Type-Safety First:** The API is built with tRPC to provide compile-time, end-to-end type safety. This is a non-negotiable principle that minimizes a vast class of frontend-backend integration bugs.
*   **Explicit Validation:** All data entering the system from an external source (user input, webhooks) MUST be rigorously validated using Zod schemas. Trust nothing.
*   **Statelessness:** All API endpoints and serverless functions are stateless. State is managed exclusively in the Supabase database.
*   **Secure by Default:** All tRPC procedures require authentication unless explicitly marked as `public`. Authorization is enforced via tRPC middleware and database RLS policies.

## 2. tRPC API Structure

The API is organized into routers, where each router corresponds to a logical resource (e.g., `reminders`).

### 2.1. Authentication (`auth` Router)

Handles user signup, login, and session management.

| Procedure | Auth Level | Input (Zod Schema) | Output (Zod Schema) | Error Codes |
| :--- | :--- | :--- | :--- | :--- |
| `signup` | Public | `z.object({ email: z.string().email(), password: z.string().min(8), orgName: z.string() })` | `z.object({ user: z.object({ id: z.string(), email: z.string() }) })` | `CONFLICT` (Email exists) |
| `login` | Public | `z.object({ email: z.string().email(), password: z.string() })` | `z.object({ session: z.any(), user: z.any() })` | `UNAUTHORIZED` |
| `logout` | **Authenticated** | `z.void()` | `z.object({ error: z.null() })` | `UNAUTHORIZED` |
| `getMe` | **Authenticated** | `z.void()` | `z.object({ id: z.string(), email: z.string(), organization_id: z.string() })` | `UNAUTHORIZED` |

### 2.2. Reminders (`reminders` Router)

Manages the full lifecycle of reminders.

| Procedure | Auth Level | Input (Zod Schema) | Output (Zod Schema) | Error Codes |
| :--- | :--- | :--- | :--- | :--- |
| `create` | **Authenticated** | `ReminderCreateInput` (see below) | `Reminder` (see below) | `BAD_REQUEST`, `UNAUTHORIZED` |
| `list` | **Authenticated** | `z.object({ limit: z.number().optional(), cursor: z.string().optional() })` | `z.object({ items: z.array(Reminder), nextCursor: z.string().nullish() })` | `UNAUTHORIZED` |
| `getById` | **Authenticated** | `z.object({ id: z.string().uuid() })` | `Reminder` | `NOT_FOUND`, `UNAUTHORIZED` |
| `update` | **Authenticated** | `ReminderUpdateInput` | `Reminder` | `NOT_FOUND`, `UNAUTHORIZED` |
| `delete` | **Authenticated** | `z.object({ id: z.string().uuid() })` | `z.object({ success: z.boolean() })` | `NOT_FOUND`, `UNAUTHORIZED` |

### 2.3. Responses (`responses` Router)

Handles incoming responses from recipients.

| Procedure | Auth Level | Input (Zod Schema) | Output (Zod Schema) | Error Codes |
| :--- | :--- | :--- | :--- | :--- |
| `submit` | Public | `z.object({ token: z.string(), action: z.enum(['done', 'snooze']), note: z.string().optional() })` | `z.object({ success: z.boolean(), message: z.string() })` | `BAD_REQUEST`, `NOT_FOUND` |

## 3. Zod Schema Definitions

```typescript
// Example Zod schemas for the 'reminders' router

const RecipientSchema = z.object({
  channel: z.enum(['email', 'whatsapp', 'telegram']),
  address: z.string(),
});

export const ReminderCreateInput = z.object({
  title: z.string().min(1),
  description: z.string().optional(),
  recipients: z.array(RecipientSchema).min(1),
  schedule: z.any(), // To be replaced with a detailed schedule schema
});

export const Reminder = z.object({
  id: z.string().uuid(),
  title: z.string(),
  status: z.string(),
  createdAt: z.date(),
  // ... other fields
});
```

## 4. Incoming Webhooks

Webhooks are used for real-time data synchronization from external services.

| Service | Event | Endpoint | Purpose |
| :--- | :--- | :--- | :--- |
| **Zoho Billing** | `subscription.renewed` | `/api/webhooks/zoho/billing` | Updates subscription status in the local database. |
| **Zoho Books** | `invoice.paid` | `/api/webhooks/zoho/books` | Automatically marks the corresponding reminder as complete. |

**Security:** All webhook endpoints MUST be secured. Handlers must validate a cryptographic signature (e.g., `X-Zoho-Signature`) passed in the request headers. The secret key used for signing must be unique per webhook and stored securely.

## 5. Idempotency and Error Handling

*   **Idempotency:**
    *   **`response.submit`:** This procedure MUST be idempotent. If a recipient submits a response multiple times, only the first submission should be processed.
    *   **Webhook Handlers:** All webhook handlers MUST be idempotent. They should check if an event has already been processed (e.g., by checking the event ID against a log) before taking action.
*   **Error Handling:** The API will use standard tRPC error codes. Custom error messages should be clear and actionable. For example, a `BAD_REQUEST` error on `reminder.create` should return a detailed message explaining which validation rule failed.

## Implementation Notes for AI Agents

*   **Shared Zod Schemas:** Define Zod schemas in a shared location that can be imported by both the frontend and backend to ensure maximum type safety.
*   **tRPC Middleware:**
    *   **Authentication:** Implement a tRPC middleware that runs on all protected procedures. It should verify the JWT from the `Authorization` header and attach the user's ID and organization ID to the context (`ctx`).
    *   **Logging:** Create a middleware to log all incoming requests and their outcomes (success or failure), excluding any PII.
*   **Webhook Signature Validation:** Create a reusable utility function or middleware to validate the signatures of incoming webhooks. This is a critical security measure.
*   **Acceptance Criteria:**
    *   All tRPC procedures are implemented with the exact Zod schemas specified.
    *   The authentication middleware correctly protects all non-public routes and provides user/organization context.
    *   Webhook handlers successfully validate the signature of all incoming requests and process the event data idempotently.
    *   The API returns the correct tRPC error codes for all specified error conditions.
