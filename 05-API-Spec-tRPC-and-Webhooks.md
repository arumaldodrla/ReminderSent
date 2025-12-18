# 05. API Specification: tRPC and Webhooks

**Owner:** Manus AI
**Last Updated:** 2025-12-18
**Version:** 1.0

**Purpose:** This document provides the complete technical specification for the ReminderSend API, including all tRPC procedures and incoming webhooks. It defines the inputs, outputs, validation schemas (Zod), and authorization rules for each endpoint.

---

## 1. API Philosophy

*   **Type-Safe:** The API is built with tRPC to provide end-to-end type safety, eliminating a common class of bugs between the frontend and backend.
*   **Zod Validation:** All inputs are rigorously validated using Zod schemas to ensure data integrity and security.
*   **Stateless:** The API is stateless, with all state managed in the Supabase database.
*   **Secure by Default:** All procedures require authentication unless explicitly marked as public.

## 2. tRPC Procedures

The API is organized into routers based on resources (e.g., `auth`, `reminders`, `responses`).

### 2.1. `auth` Router

Handles user authentication and session management.

| Procedure | Auth | Input (Zod Schema) | Output (Zod Schema) |
| :--- | :--- | :--- | :--- |
| `signup` | Public | `z.object({ email: z.string().email(), password: z.string().min(8) })` | `z.object({ user: z.object({ id: z.string(), email: z.string() }) })` |
| `login` | Public | `z.object({ email: z.string().email(), password: z.string() })` | `z.object({ session: z.any(), user: z.any() })` |
| `logout` | Required | `z.void()` | `z.object({ error: z.null() })` |
| `me` | Required | `z.void()` | `z.object({ id: z.string(), email: z.string(), organization_id: z.string() })` |

### 2.2. `reminders` Router

Manages the creation, retrieval, and modification of reminders.

| Procedure | Auth | Input (Zod Schema) | Output (Zod Schema) |
| :--- | :--- | :--- | :--- |
| `create` | Required | `z.object({ title: z.string(), description: z.string().optional(), recipients: z.array(z.object({ channel: z.enum(['email', 'whatsapp', 'telegram']), address: z.string() })), schedule: z.string() /* ISO 8601 or cron */ })` | `z.object({ id: z.string(), title: z.string(), status: z.string() })` |
| `list` | Required | `z.object({ limit: z.number().optional(), offset: z.number().optional() })` | `z.array(z.object({ id: z.string(), title: z.string(), status: z.string(), recipient_count: z.number() }))` |
| `getById` | Required | `z.object({ id: z.string() })` | `z.object({ id: z.string(), title: z.string(), description: z.string().optional(), ..., responses: z.array(...) })` |
| `update` | Required | `z.object({ id: z.string(), ... /* fields to update */ })` | `z.object({ id: z.string(), ... /* updated fields */ })` |
| `delete` | Required | `z.object({ id: z.string() })` | `z.object({ success: z.boolean() })` |

### 2.3. `responses` Router

Handles responses from recipients.

| Procedure | Auth | Input (Zod Schema) | Output (Zod Schema) |
| :--- | :--- | :--- | :--- |
| `submit` | Public | `z.object({ token: z.string(), action: z.enum(['done', 'later']), note: z.string().optional() })` | `z.object({ success: z.boolean(), message: z.string() })` |

## 3. Webhooks (Incoming)

The platform will consume webhooks from external services to enable real-time data synchronization.

### 3.1. Zoho Webhooks

| Service | Event | Endpoint | Purpose |
| :--- | :--- | :--- | :--- |
| **Zoho Billing** | `subscription.renewed` | `/api/webhooks/zoho/billing` | To update the subscription status in the ReminderSend database. |
| **Zoho Books** | `invoice.paid` | `/api/webhooks/zoho/books` | To automatically mark the corresponding reminder as complete. |

**Security:** All incoming webhooks MUST be secured. This will be achieved by validating a signature passed in the webhook request headers. The secret used for signing will be unique for each configured webhook.

## 4. Idempotency

*   **`reminder.create`:** This procedure is NOT idempotent. Calling it multiple times will create multiple reminders.
*   **`response.submit`:** This procedure MUST be idempotent. If a recipient clicks the response link multiple times, the system should record the first response and ignore subsequent attempts.
*   **Webhook Handlers:** All webhook handlers MUST be idempotent. It is common for webhook providers to send the same event more than once. The handler logic should check if the event has already been processed before taking action.

## 5. Rate Limiting

To prevent abuse and ensure platform stability, rate limiting will be applied to all public-facing endpoints.

| Endpoint/Procedure | Limit | Burst | Notes |
| :--- | :--- | :--- | :--- |
| `auth.login` | 10 requests/minute | 5 | Per IP address. |
| `response.submit` | 20 requests/minute | 10 | Per IP address. |
| Webhook Endpoints | 100 requests/minute | 50 | Per service (e.g., per Zoho organization). |

## Implementation Notes for AI Agents

*   **Zod Schemas First:** Define all Zod schemas before implementing the tRPC procedures. This ensures that the data structures are well-defined from the start.
*   **Auth Middleware:** Implement a tRPC middleware to handle authentication. The middleware should verify the JWT from the `Authorization` header and attach the user object to the request context.
*   **Webhook Signature Validation:** Create a reusable function or middleware to validate the signatures of incoming webhooks. This is critical for security.
*   **Acceptance Criteria:**
    *   All tRPC procedures are implemented with the specified Zod schemas for input and output.
    *   The auth middleware correctly protects authenticated routes and makes user data available in the context.
    *   Webhook handlers successfully validate incoming requests and process the event data idempotently.
    *   Rate limiting is implemented and effectively blocks requests that exceed the defined limits.
