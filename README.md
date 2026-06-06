# Mini Microservices App

A hands-on learning project exploring microservices architecture patterns — including inter-service communication, an event bus, and query-side aggregation. This project is intentionally kept simple and is **not production-ready**; its purpose is to build intuition for how distributed systems are structured.

---

## Architecture Overview

The application is split into independent services that communicate asynchronously through a central event bus.

```
┌─────────────┐     HTTP      ┌─────────────────┐
│   Client    │◄─────────────►│   Posts Service  │
│  (React)    │               └────────┬────────┘
│             │                        │ events
│             │               ┌────────▼────────┐
│             │◄──────────────│   Query Service  │
└─────────────┘               └────────▲────────┘
                                        │ events
                               ┌────────┴────────┐
                               │    Event Bus     │
                               └──┬──────────┬───┘
                                  │          │
                        ┌─────────▼──┐  ┌────▼──────────┐
                        │  Comments  │  │  Moderation   │
                        │  Service   │  │  Service      │
                        └────────────┘  └───────────────┘
```

### Services

| Service | Port | Responsibility |
|---|---|---|
| **Posts** | 4000 | Create and store posts |
| **Comments** | 4001 | Create and store comments per post |
| **Event Bus** | 4005 | Receive and fan-out events to all services |
| **Moderation** | 4003 | Approve or reject comments based on content |
| **Query** | 4002 | Aggregate posts + comments into a single read model |
| **Client** | 3000 | React frontend that talks to Posts and Query services |

---

## Key Concepts Practiced

- **Microservices decomposition** — each service owns its own data and logic
- **Event-driven communication** — services emit events (e.g. `PostCreated`, `CommentCreated`, `CommentModerated`) rather than calling each other directly
- **Query / Read model** — the Query service listens to all events and builds a denormalized view, avoiding cross-service queries at read time
- **Content moderation pipeline** — the Comments service emits a `CommentCreated` event; the Moderation service processes it and emits back a `CommentModerated` event that updates the comment status

---

## Tech Stack

| Layer | Technology |
|---|---|
| Services | Node.js, Express |
| Frontend | React 18, Axios |
| Dev tooling | Nodemon |
| Inter-service | Axios (HTTP-based event bus) |

---

## Getting Started

Each service is started independently. Open a terminal for each:

```bash
# Posts service
cd posts && npm install && npm start

# Comments service
cd comments && npm install && npm start

# Event Bus
cd event-bus && npm install && npm start

# Moderation service
cd moderation && npm install && npm start

# Query service
cd query && npm install && npm start

# React client
cd client && npm install && npm start
```

The React app will be available at `http://localhost:3000`.

---

## Project Status

This is a **learning project**. The following are known limitations by design:

- No persistent storage — data lives in memory and resets on restart
- No authentication or authorization
- No Docker / orchestration setup
- No tests

---

## Learning Goals

- [ ] Understand how services communicate without direct coupling
- [ ] Implement an event bus from scratch
- [ ] Practice the CQRS pattern (separate write and read models)
- [ ] Build intuition for eventual consistency tradeoffs
