Perfect — this is your **“Pattern B: Self-Reporting Integration”** doc. I’ll format it like a clean, production-style `.md` with **Mermaid diagrams + architecture clarity** so you can drop it into GitHub or pitch slides.

---

# 📄 Self-Reporting Integration (Client SDK + API)

## 🧠 1. What This Feature Is

This is your **advanced integration layer**.

Instead of relying only on external monitors (like UptimeRobot), clients can:

> 📢 **Report internal failures directly from their backend**

Examples:

* Database connection pool exhausted
* Payment service throwing errors
* Auth service failing JWT validation

---

## 💡 Core Idea

Client adds **~10 lines of code** (or uses your SDK):

```js
await fetch('https://yourapp.com/api/incidents/report', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'X-API-Key': 'ip_abc123'
  },
  body: JSON.stringify({
    service: 'Payment API',
    errorRate: 0.72,
    affectedUsers: 340
  })
});
```

---

## 📊 Mermaid: Full System Architecture

```mermaid
flowchart TD
    A[Client Backend Services]
    A1[Auth Service]
    A2[Payment Service]
    A3[Database Layer]

    A --> A1
    A --> A2
    A --> A3

    A2 --> B
    B[SDK or fetch call]

    B --> C
    C[POST incidents report API]

    C --> D
    D[API Key Auth]

    C --> E
    E[Rate Limiter]

    D --> F
    E --> F
    F[Incident Logic]

    F --> G
    G[Auto Severity Detection]

    F --> H
    H[(MongoDB)]

    H --> I
    I[(Incidents)]

    H --> J
    J[(Teams)]

    H --> K
    K[(API Keys)]

    F --> L
    L[WebSocket Broadcast]

    F --> M
    M[Notifications]

    L --> N
    N[Dashboard]

    M --> O
    O[Email and Slack]
```

---

## 🔄 2. End-to-End Flow

### Step 1: Client backend detects error

Example:

* Payment API throws 500s
* Error rate spikes

---

### Step 2: Client reports incident

Via SDK or `fetch()`

---

### Step 3: Your backend receives request

```http
POST /api/incidents/report
```

Headers:

```
X-API-Key: ip_abc123
```

---

### Step 4: Middleware pipeline

```txt
API Key Auth → Rate Limiter → Incident Logic
```

---

### Step 5: Incident handling

* Create / update / resolve incident
* Auto-detect severity
* Store in DB
* Broadcast via WebSocket
* Send notifications

---

## 📊 Mermaid: Request Lifecycle

```mermaid
sequenceDiagram
    participant Client
    participant API
    participant DB
    participant WS
    participant Notify

    Client->>API: POST /api/incidents/report
    API->>API: Validate API Key
    API->>API: Apply Rate Limit

    API->>API: Detect Severity

    API->>DB: Create / Update Incident
    DB-->>API: Saved

    API->>WS: Emit event (incident:new)
    API->>Notify: Send email/slack

    API-->>Client: Response (incidentId)
```

---

## 🗂️ 3. Folder Structure

```txt
incident-platform/
├── models/
├── routes/
├── middleware/
├── services/
├── socket/
└── sdk/
```

### Key idea:

* **models/** → DB schema
* **routes/** → API endpoints
* **middleware/** → auth, limits
* **services/** → business logic
* **sdk/** → client integration

---

## 🧩 4. Database Design

### Teams

```json
{
  "name": "Acme Corp",
  "services": ["Payment API", "Auth"]
}
```

---

### API Keys

```json
{
  "teamId": "...",
  "keyHash": "sha256...",
  "label": "Production"
}
```

---

### Incidents

```json
{
  "title": "Payment API failure",
  "severity": "P1",
  "status": "investigating",
  "source": "api"
}
```

---

## 🔐 5. API Key System

### Key Principles

* Never store raw API keys
* Always hash (SHA-256)
* Return raw key **only once**

---

### Flow

```mermaid
flowchart LR
    A[Generate Raw Key] --> B[Hash Key]
    B --> C[Store Hash in DB]
    A --> D[Return Raw Key to User]

    E[Client Request] --> F[Hash Incoming Key]
    F --> G[Compare with DB]
    G -->|Valid| H[Allow Request]
    G -->|Invalid| I[Reject]
```

---

## 🚀 6. Report Endpoint Behavior

### Modes

| Action  | Behavior           |
| ------- | ------------------ |
| create  | New incident       |
| update  | Add timeline event |
| resolve | Close incident     |

---

## 📊 Mermaid: Incident State Flow

```mermaid
stateDiagram-v2
    [*] --> Investigating

    Investigating --> Updating : update event
    Updating --> Investigating

    Investigating --> Monitoring : resolve triggered
    Monitoring --> Resolved

    Resolved --> [*]
```

---

## 🧠 7. Auto Severity Detection

### Logic

* P1 → critical
* P2 → major
* P3 → minor

---

### Rules Visualization

```mermaid
flowchart TD
    A[Incoming Metrics]

    A --> B{Error Rate >= 50%?}
    B -->|Yes| P1
    B -->|No| C{Response Time >= 10s?}
    C -->|Yes| P1
    C -->|No| D{Affected Users >= 1000?}
    D -->|Yes| P1
    D -->|No| E{Moderate thresholds?}

    E -->|Yes| P2
    E -->|No| P3
```

---

## ⛔ 8. Rate Limiting (Per Team)

### Why?

* Prevent spam
* Prevent accidental loops
* Protect infrastructure

---

### Design

* Limit: 60 req/hour
* Key: **teamId (NOT IP)**

---

### Flow

```mermaid
flowchart LR
    A[Incoming Request] --> B{Team Requests < Limit?}
    B -->|Yes| C[Process Request]
    B -->|No| D[Return 429 Error]
```

---

## 📦 9. Client SDK (Your Secret Weapon)

### Why it matters

This is exactly how tools like:

* Sentry
* Datadog

win developers.

---

### Client Usage

```js
const reporter = new IncidentReporter({ apiKey: 'ip_abc123' });

await reporter.report('Payment API', 'Stripe failing', {
  errorRate: 0.6
});

await reporter.resolve('Payment API');
```

---

### SDK Behavior

```mermaid
flowchart TD
    A[Client Code]
    B[SDK]

    A --> B

    B --> C
    C[report]

    B --> D
    D[update]

    B --> E
    E[resolve]

    C --> F
    D --> F
    E --> F

    F[POST API] --> G
    G[Backend]
```

---

## 🔔 10. Notifications System

### Channels

* Email (Nodemailer)
* Slack (Webhook)

---

### Flow

```mermaid
flowchart TD
    A[Incident Created] --> B[Notification Service]

    B --> C[Send Email]
    B --> D[Send Slack Message]

    C --> E[Team Inbox]
    D --> F[Slack Channel]
```

---

## 🖥️ 11. Team Dashboard

### Features

* Incident list
* Timeline view
* Analytics (MTTR)
* Public status page

---

### Public Status API

```http
GET /api/status/:teamId
```

Returns:

* Active incidents
* Recent history
* Overall status

---

### Dashboard Data Flow

```mermaid
flowchart TD
    A[Frontend Dashboard] -->|GET /incidents| B[Backend]

    B --> C[MongoDB]

    B --> D[WebSocket Updates]

    D --> A
```

---

## 🧠 12. How This Differs from UptimeRobot

| Feature                | UptimeRobot | Your System |
| ---------------------- | ----------- | ----------- |
| Detect downtime        | ✅           | ❌           |
| Detect internal errors | ❌           | ✅           |
| Root cause hints       | ❌           | ✅           |
| Team collaboration     | ❌           | ✅           |
| SDK integration        | ❌           | ✅           |

---

## 🔥 13. Pitch Line (Use This)

> “We support both external monitoring (via webhooks) and internal self-reporting via a lightweight SDK, allowing teams to capture outages that traditional uptime tools cannot detect.”

---

## ⚡ 14. Mental Model

* **UptimeRobot** → detects “site is down”
* **Your SDK** → detects “why it’s down”

Together:

> Full observability loop 🔥

---

## ✅ Why This Is Powerful

* Works for **internal failures**
* Minimal client effort (3–10 lines)
* Scales across services
* Industry-grade pattern
* Huge differentiator in demos

---

## 👍 If you want next level

I can extend this into:

* 🔐 HMAC webhook verification (production security)
* 🧱 Full DB schema diagrams
* ⚡ Redis-based rate limiting (real-world scale)
* 🧠 AI root-cause pipeline design
* 🎯 Pitch deck version (slides)

Just tell me what you want next.
