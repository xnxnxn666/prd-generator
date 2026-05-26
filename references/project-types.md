# Project Type Detection

## Detection Matrix

Use this matrix after Phase 1 to classify the project. Match the user's description against the signals. Present the top match with rationale.

| Type | Strong signals | Weak signals | Typical users say... |
|---|---|---|---|
| **Web SPA** | "website", "web app", "browser-based", "responsive", "frontend" | mentions React/Vue/Angular, "single page" | "I want a website where users can..." |
| **Web Full-Stack** | "web app" + mentions database, "platform", "SaaS" | mentions backend + frontend, "users log in and manage their..." | "I want to build a platform for..." |
| **Mobile App** | "app", "iPhone/Android", "on the phone", "camera/GPS" | mentions React Native/Flutter, "native" | "I want an app that lets people..." |
| **CS Desktop** | "desktop", "Windows/Mac/Linux", "offline", "local" | mentions Electron/WPF/Qt, "installed", "doesn't need internet" | "I need a desktop tool for..." |
| **CLI Tool** | "command line", "terminal", "script", "automation" | mentions Python/Go/Node, "run on server" | "I need a tool that takes X and outputs Y" |
| **API / Backend** | "API", "microservice", "backend only", "headless" | "no UI needed", "data processing" | "I need a service that handles..." |
| **Embedded / IoT** | "hardware", "device", "sensor", "firmware" | mentions Raspberry Pi, Arduino, RTOS | "I need software that runs on a device..." |

## Type-Specific Follow-ups

After confirming the type, layer these additional questions:

### Web SPA
- Does it need SEO? If yes, consider SSR/SSG instead
- Is there an existing backend API, or does one need to be built?
- Are there authenticated vs. public sections?

### Web Full-Stack
- Monolith or separate frontend/backend?
- What's the expected user volume?
- Multi-tenant (multiple organizations sharing the system) or single-tenant?

### Mobile App
- iOS, Android, or both?
- Native or cross-platform? (cross-platform saves 30-50% dev time)
- Does it need to work offline?
- Push notifications needed?

### CS Desktop
- Which OS(es) must be supported?
- Does it need internet access, or fully offline?
- Auto-update mechanism needed?

### CLI Tool
- Is it interactive or batch/pipe-oriented?
- Who's the audience — developers or non-technical users?
- Does it need config files?

### API / Backend
- REST, GraphQL, gRPC, or WebSocket?
- Expected request volume and latency requirements?
- Authentication: API keys, OAuth, JWT?
