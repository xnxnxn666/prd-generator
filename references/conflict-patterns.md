# Conflict Detection Patterns

## How to detect conflicts

A conflict exists when two requirements pull in opposite directions — satisfying one makes the other harder or impossible. Don't be pedantic about it (minor tensions are fine). Surface only the conflicts that genuinely affect architecture or user experience.

## Common conflict pairs

### Performance vs. Security
- **Conflict**: Deep packet inspection, encryption at rest, frequent auth revalidation → all add latency
- **Questions to ask**: "For this system, what matters more: sub-100ms response times or maximum data protection?"
- **Resolution strategies**: tiered security (strict for sensitive endpoints, relaxed for public content), caching authenticated responses

### Rich features vs. Fast MVP
- **Conflict**: Every feature adds complexity, testing burden, and maintenance
- **Questions to ask**: "Which of these features would make the product unusable if missing? Everything else can wait."
- **Resolution strategies**: plugin architecture for P2 features, feature flags

### Offline-first vs. Real-time consistency
- **Conflict**: Data modified offline becomes stale; syncing causes merge conflicts
- **Questions to ask**: "Can users tolerate seeing slightly outdated data, or is consistency critical?"
- **Resolution strategies**: CRDT for text, last-write-wins for simple types, explicit conflict resolution UI

### Customizable vs. Simple UX
- **Conflict**: Every configuration option adds cognitive load
- **Questions to ask**: "Who is the primary user — a power user who wants control, or someone who wants things to 'just work'?"
- **Resolution strategies**: sensible defaults + advanced settings panel, progressive disclosure of complexity

### Scale vs. Budget
- **Conflict**: Horizontal scaling, redundancy, managed services all cost money
- **Questions to ask**: "What's the expected scale at launch vs. 12 months out? Can we defer scaling investment?"

### Privacy compliance vs. Data-driven features
- **Conflict**: GDPR/PIPL requires data minimization; ML features want ALL the data
- **Questions to ask**: "Is the data truly essential for this feature, or are you collecting it 'just in case'?"
- **Resolution strategies**: anonymization pipeline, opt-in data collection

## How to present a conflict

Use this pattern:

1. State both requirements clearly
2. Explain why they're in tension (in plain language)
3. Describe the tradeoff — what you gain and lose on each side
4. Ask the user to choose, with a recommendation if one side is clearly right for their context

Example:
> "I notice a tension here. You want the app to work fully offline, but you also want real-time collaboration where multiple people edit the same document. The challenge: if two people edit offline, their changes can conflict when they both go online. There's no perfect fix for this — every offline-collaboration system has edge cases. Which is more important: guaranteed offline access, or guaranteed consistency between users?"
