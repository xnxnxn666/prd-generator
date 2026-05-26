# Interview Guide

## General techniques

**Start broad, narrow down.** Phase 1 asks "what problem are you solving?" not "what database should you use?" Premature technical questions produce answers the user isn't ready to give.

**Mirror the user's language.** If the user calls it a "shop," don't say "e-commerce platform." If they say "members area," don't say "authenticated user dashboard." Using their words builds trust and confirms understanding.

**Summarize at phase boundaries.** Before moving to the next phase, give a brief recap: "So to summarize, you're building X for Y users, and success means Z. Ready to dig into features?" This gives the user a natural checkpoint to correct misunderstandings.

**Don't answer for the user.** When the user hesitates, resist the urge to fill the silence with suggestions. Ask guiding questions instead: "What would happen if this feature didn't exist at launch?"

## Difficult scenarios

### The user says "I don't know" to technical questions
This is fine — the skill's job is to recommend based on the business context. Say: "No problem — based on what you've described, I'd suggest X because [reason in plain language]. Does that sound reasonable?"

### The user keeps adding features
Gently push back: "That's a great feature for the roadmap. For the MVP, does it need to be in v1, or can we put it in the 'post-launch' bucket?"

### The user describes a massive system all at once
Break it down: "This is a big vision — let's focus on one piece at a time. What's the single most important thing a user should be able to do on day one?"

### The user is very technical and short
Match their pace. Skip the explanations they obviously don't need. Ask precise questions and get to the output faster. The 5-phase structure is a tool, not a script — adapt.

### The user has no technical background at all
Replace jargon with metaphors. "Database" becomes "where information is stored." "API" becomes "how different parts of the system talk to each other." Focus heavily on user journeys — concrete steps are easier to discuss than abstract architecture.

## Mode-specific scenarios

### Retrospective mode: user doesn't know their own codebase well

Some users inherit a codebase — they didn't write it but are now responsible for it. They may struggle to answer questions about what the code does. In this case:

- Lead with your own findings: "From what I can see in the code, it looks like..."
- Let the user confirm or correct, rather than expecting them to describe from scratch
- Focus the interview on what they WANT (future state), not what IS (you already know)

### Retrospective mode: user is defensive about their code

Some users feel judged when you point out issues. Frame findings constructively:

- Instead of "You have no tests," say "I notice there aren't tests yet — is that something you'd like to address in the next phase?"
- Instead of "The code structure is messy," say "Would reorganizing the code structure be part of this PRD effort?"
- Always pair criticism with the opportunity to fix it — that's why they're here

### Iteration mode: user wants "just one small change"

Users often underestimate the impact of changes. If the user says "just add X":

- Don't blindly add it. Check dependencies: "Adding X might affect Y and Z. Let me check..."
- Surface ripple effects: a "simple" addition may need new API endpoints, database changes, new UI, new error handling
- Let the user decide whether to proceed after they understand the full scope

### Iteration mode: user can't articulate what changed

Sometimes users know they're unhappy with the current PRD but can't pinpoint why. Help them:

- "Let's walk through each module one by one. Tell me what you like and don't like about each."
- "If you could only fix one thing about the current spec, what would it be?"
- "Has anything changed in the business or the market since we wrote the original PRD?"

### Iteration mode: chaining multiple iterations

If the user has already iterated several times (v4, v5...), ask:

- "What's driving the repeated changes? Is the problem the PRD, or is the product direction itself still evolving?"
- Consider recommending fewer, larger iterations rather than many small ones
- If the user seems stuck in a loop, suggest a fresh greenfield PRD instead of continued iteration

## Phase transition checklist

Before moving from Phase N to N+1:
1. Summarize key decisions from the current phase
2. Ask: "Is there anything about [current topic] we haven't covered?"
3. Only proceed when the user confirms
