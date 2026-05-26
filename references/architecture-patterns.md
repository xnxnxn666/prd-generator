# Architecture Pattern Recommendations

Explain these in plain language when recommending. Don't assume the user knows the terminology.

## Pattern Selection Guide

| If the project is... | Consider... | Why |
|---|---|---|
| A simple web app with limited features | **Monolith (single deployable)** | Less complexity, faster to build. Split later if needed. |
| A platform expected to grow significantly | **Modular Monolith** first, then extract microservices | Start simple, split only when you have clear boundaries |
| A system with clearly independent domains | **Microservices** | Independent scaling and deployment, but higher ops cost |
| Real-time collaborative features | **Event-driven architecture** | WebSocket + message queue for sync across users |
| Content-heavy, mostly read | **SSG/SSR with headless CMS** | Fast page loads, good SEO, content managed separately |
| Mobile app that works offline | **Local-first with sync** | Data lives on device, syncs when online. CRDT or simple last-write-wins |
| Data processing pipeline | **Queue + worker pattern** | Jobs go into a queue, workers process them, results stored |

## Key tradeoffs to surface

- **Monolith vs Microservices**: Monolith is faster to build, easier to debug, simpler to deploy. Microservices help when teams are large and domains are independent. Most projects should start as a monolith.
- **SPA vs SSR**: SPA is smoother UX after load, simpler deployment (static files). SSR is better for SEO, faster first paint. If both matter, use a meta-framework (Next.js, Nuxt, SvelteKit).
- **SQL vs NoSQL**: If data has clear relationships and needs integrity guarantees, use SQL. If schema is fluid and data is document-shaped, NoSQL is fine. When in doubt, start with SQL.
- **Native vs Cross-platform mobile**: Native has best performance and API access. Cross-platform (React Native, Flutter) saves 30-50% development time for most apps.

## Technology suggestions

Don't prescribe a specific tech stack unless the user asks. Instead, describe the constraints that matter:

- "This needs a database that handles X writes/second and guarantees Y"
- "This needs a frontend framework that supports SSR for SEO"
- "This needs real-time communication between users — WebSocket + pub/sub"
