


<img width="1440" height="4540" alt="image" src="https://github.com/user-attachments/assets/298a2c96-8dbd-462b-b7ec-d83262b24568" />


# 🧠 Backend Architecture

This document outlines the full backend structure of the project, including services, routes, models, and system design.

---

## 📁 Project Structure

```bash
backend/
│
├── server.js                # Entry point — starts HTTP + Socket server
├── app.js                   # Express setup: middleware, routes, error handler
├── .env                     # Secrets — never commit
├── .env.example             # Safe template for teammates
├── .gitignore
├── package.json
│
└── src/
    │
    ├── config/              # App-level configurations
    │   ├── db.js
    │   ├── socket.js
    │   ├── env.js
    │   └── constants.js
    │
    ├── models/              # Database schemas (Mongoose)
    │   ├── Team.model.js
    │   ├── User.model.js
    │   ├── Incident.model.js
    │   ├── TimelineEvent.model.js
    │   ├── ApiKey.model.js
    │   ├── Service.model.js
    │   └── WebhookLog.model.js
    │
    ├── routes/              # API route definitions
    │   ├── auth.routes.js
    │   ├── team.routes.js
    │   ├── incident.routes.js
    │   ├── timeline.routes.js
    │   ├── webhook.routes.js
    │   ├── apikey.routes.js
    │   ├── status.routes.js
    │   ├── ai.routes.js
    │   ├── analytics.routes.js
    │   └── simulate.routes.js
    │
    ├── controllers/         # Request handlers (business logic layer)
    │   ├── webhook/
    │   │   ├── uptimerobot.controller.js
    │   │   └── generic.controller.js
    │   ├── incident.controller.js
    │   ├── timeline.controller.js
    │   ├── team.controller.js
    │   ├── auth.controller.js
    │   ├── apikey.controller.js
    │   ├── status.controller.js
    │   ├── ai.controller.js
    │   └── analytics.controller.js
    │
    ├── services/            # Core business logic
    │   ├── incident/
    │   ├── webhook/
    │   ├── severity/
    │   ├── notification/
    │   ├── ai/
    │   ├── socket/
    │   └── analytics/
    │
    ├── middleware/          # Express middlewares
    │   ├── auth.middleware.js
    │   ├── apiKey.middleware.js
    │   ├── webhookToken.middleware.js
    │   ├── rateLimiter.middleware.js
    │   ├── roleCheck.middleware.js
    │   └── errorHandler.middleware.js
    │
    ├── validators/          # Request validation schemas
    │   ├── incident.validator.js
    │   ├── auth.validator.js
    │   ├── apikey.validator.js
    │   └── report.validator.js
    │
    ├── utils/               # Helper functions
    │   ├── generateToken.js
    │   ├── generateApiKey.js
    │   ├── generateWebhookSecret.js
    │   ├── hashKey.js
    │   ├── formatDuration.js
    │   ├── asyncHandler.js
    │   └── logger.js
    │
    ├── sdk/                 # Client SDK (Node.js)
    │   ├── IncidentReporter.js
    │   ├── index.js
    │   └── README.md
    │
    └── tests/               # Test suite
        ├── webhook.test.js
        ├── report.test.js
        ├── incident.test.js
        ├── auth.test.js
        └── analytics.test.js


        # Frontend Architecture — 4-Layer System (React Version)

**Stack:** React (Vite or CRA) + Redux Toolkit + TypeScript + Tailwind + Socket.io Client + React Router
```
---


## 🧱 Architecture Overview


┌─────────────────────────────────────────────────────┐
│  LAYER 1 — PRESENTATION                             │
│  Pages, Layouts, UI Components, Views               │
├─────────────────────────────────────────────────────┤
│  LAYER 2 — STATE MANAGEMENT                         │
│  Redux Store, Slices, Selectors, Thunks             │
├─────────────────────────────────────────────────────┤
│  LAYER 3 — SERVICE                                  │
│  API Calls, Socket Client, Auth, AI Requests        │
├─────────────────────────────────────────────────────┤
│  LAYER 4 — INFRASTRUCTURE                           │
│  Config, Utils, Hooks, Constants, Types, Validators │
└─────────────────────────────────────────────────────┘


---

# 📁 Project Structure

```bash
frontend/
│
├── src/
│   │
│   ├── app/                           # App entry + routing setup
│   │   ├── App.jsx                    # Main router config (React Router)
│   │   ├── routes.jsx                 # Route definitions (optional separation)
│   │   └── providers.jsx              # Redux + Socket providers
│   │
│   ├── pages/                         # Replaces Next.js app/ routes
│   │
│   │   ├── auth/
│   │   │   ├── Login.jsx
│   │   │   ├── Register.jsx
│   │   │   └── AuthLayout.jsx
│   │
│   │   ├── dashboard/
│   │   │   ├── DashboardLayout.jsx
│   │   │   │
│   │   │   ├── overview/
│   │   │   │   └── Overview.jsx
│   │   │   │
│   │   │   ├── incidents/
│   │   │   │   ├── Incidents.jsx
│   │   │   │   ├── NewIncident.jsx
│   │   │   │   ├── IncidentDetails.jsx
│   │   │   │   └── Postmortem.jsx
│   │   │   │
│   │   │   ├── analytics/
│   │   │   │   └── Analytics.jsx
│   │   │   │
│   │   │   ├── services/
│   │   │   │   └── Services.jsx
│   │   │   │
│   │   │   ├── integrations/
│   │   │   │   ├── Integrations.jsx
│   │   │   │   ├── UptimeRobot.jsx
│   │   │   │   └── ApiKeys.jsx
│   │   │   │
│   │   │   ├── team/
│   │   │   │   └── Team.jsx
│   │   │   │
│   │   │   └── settings/
│   │   │       └── Settings.jsx
│   │
│   │   ├── status/
│   │   │   └── StatusPage.jsx         # /status/:slug
│   │
│   │   ├── Landing.jsx               # /
│   │   ├── NotFound.jsx
│   │
│   │
│   │──────────────────────────────────────────────────
│   │  LAYER 1 — PRESENTATION
│   │──────────────────────────────────────────────────
│
├── components/
│   │
│   ├── ui/
│   │   ├── Button.jsx
│   │   ├── Badge.jsx
│   │   ├── Card.jsx
│   │   ├── Modal.jsx
│   │   ├── Drawer.jsx
│   │   ├── Tooltip.jsx
│   │   ├── Spinner.jsx
│   │   ├── Avatar.jsx
│   │   ├── Dropdown.jsx
│   │   ├── Input.jsx
│   │   ├── Textarea.jsx
│   │   ├── Select.jsx
│   │   ├── Toggle.jsx
│   │   ├── Tabs.jsx
│   │   ├── Toast.jsx
│   │   ├── EmptyState.jsx
│   │   └── index.js
│   │
│   ├── layout/
│   │   ├── Sidebar.jsx
│   │   ├── Topbar.jsx
│   │   ├── MobileNav.jsx
│   │   └── PageWrapper.jsx
│   │
│   ├── incidents/
│   │   ├── IncidentCard.jsx
│   │   ├── IncidentBadge.jsx
│   │   ├── IncidentFilters.jsx
│   │   ├── IncidentTable.jsx
│   │   ├── CreateIncidentForm.jsx
│   │   └── SeveritySelector.jsx
│   │
│   ├── warroom/
│   │   ├── WarRoomHeader.jsx
│   │   ├── LiveTimer.jsx
│   │   ├── StatusStepper.jsx
│   │   ├── ResponderList.jsx
│   │   ├── AssignResponder.jsx
│   │   ├── UpdateFeed.jsx
│   │   ├── UpdateComposer.jsx
│   │   └── ResolveModal.jsx
│   │
│   ├── ai/
│   │   ├── RootCausePanel.jsx
│   │   ├── PostmortemViewer.jsx
│   │   ├── PostmortemEditor.jsx
│   │   └── AIGeneratingLoader.jsx
│   │
│   ├── status/
│   │   ├── StatusHeader.jsx
│   │   ├── ServiceRow.jsx
│   │   ├── PublicTimeline.jsx
│   │   ├── IncidentHistory.jsx
│   │   └── SubscribeForm.jsx
│   │
│   ├── analytics/
│   │   ├── MTTRChart.jsx
│   │   ├── IncidentFrequency.jsx
│   │   ├── SeverityBreakdown.jsx
│   │   ├── ResponderLeaderboard.jsx
│   │   └── StatCard.jsx
│   │
│   ├── integrations/
│   │   ├── WebhookURLBox.jsx
│   │   ├── UptimeRobotGuide.jsx
│   │   ├── ApiKeyCard.jsx
│   │   └── CreateApiKeyModal.jsx
│   │
│   └── shared/
│       ├── ConfirmDialog.jsx
│       ├── CopyButton.jsx
│       ├── LiveDot.jsx
│       ├── TimeAgo.jsx
│       └── ProtectedRoute.jsx
│
│
│──────────────────────────────────────────────────
│  LAYER 2 — STATE MANAGEMENT
│──────────────────────────────────────────────────
│
├── store/
│   ├── index.js
│   ├── rootReducer.js
│   ├── slices/ (same as original — unchanged)
│   └── middleware/
│       └── socketMiddleware.js
│
│
│──────────────────────────────────────────────────
│  LAYER 3 — SERVICE
│──────────────────────────────────────────────────
│
├── services/
│   ├── api/ (unchanged)
│   ├── socket/ (unchanged)
│   └── auth/ (unchanged)
│
│
│──────────────────────────────────────────────────
│  LAYER 4 — INFRASTRUCTURE
│──────────────────────────────────────────────────
│
├── hooks/ (unchanged)
├── lib/
│   ├── axios.js
│   ├── redux-provider.jsx
│   ├── socket-provider.jsx
│
├── types/
├── constants/
├── validators/
└── utils/
```

---


### Providers (Redux + Socket)

```jsx
// src/app/providers.jsx
import { Provider } from "react-redux";
import { store } from "@/store";
import SocketProvider from "@/lib/socket-provider";

export default function Providers({ children }) {
  return (
    <Provider store={store}>
      <SocketProvider>
        {children}
      </SocketProvider>
    </Provider>
  );
}
```

---

# 🔥 Everything Else (UNCHANGED)

* Redux slices ✅
* Thunks ✅
* Services layer ✅
* Socket architecture ✅
* Hooks ✅
* Constants/types/validators ✅
* Dependency rules ✅

---

# 🧠 Architecture Flow (UNCHANGED)

```
USER ACTION
↓
[L1] Component
↓
[L4] Hook
↓
[L2] Redux Thunk
↓
[L3] API Call
↓
Backend → Socket Event
↓
[L3] Socket Handler
↓
[L2] Slice Update
↓
[L1] UI Re-render
```

---
# 📏 Dependency Rules (STRICT)

```
Layer 1 → hooks, utils, constants, types, selectors
Layer 2 → services, utils, constants, types
Layer 3 → lib, utils, constants, types
Layer 4 → imports NOTHING from above layers
```

---
