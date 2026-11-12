# Architecture Learning Journal

> This captures the owner's reasoning and the next learning questions. Add entries after design decisions, experiments and retrospectives; don't overwrite earlier hypotheses without documenting what changed.

## Entry 1 — World Engine and Agent Runtime

### Owner's original hypothesis

- The **World Engine** should describe the currently active environment, its entities and rules, so the same project may later support not just a dungeon but other worlds (e.g., a space colony).
- The **Agent Runtime** should encounter that environment and pursue objectives; in a dungeon, the agent reasons about rooms, moving, walls, traps and acquiring a key.

### Mentorship feedback

The separation of responsibilities is a good starting point. To keep the runtime reusable, distinguish:
1. **World state and rules:** what really exists and which actions are valid.
2. **Observation:** what the specific agent can access at a particular moment; do not leak hidden facts.
3. **Agent/policy:** interprets observations, forms hypotheses, and chooses an action toward its goal.
4. **Runtime:** coordinates the observe–decide–act cycle without having hard-coded notions of rooms or oxygen tanks.

This decomposition is a **hypothesis to examine**, not an implementation mandated by the mentor.

### Open design questions (owner to answer)

- What should `LOOK` return when a key is hidden two rooms away?
- When an agent attempts to move through a wall, which component rejects that action? What observation/feedback does the agent then receive?
- How can two strategies act on the *same rules* with different memories?
- What would the interface need to support another environment without importing dungeon concepts into the runtime?

### Next step

Read the two Berkeley CS188 introductory sections listed in [STUDY-PATH.md](STUDY-PATH.md). Sketch a proposed contract in plain language. Then discuss trade-offs **before coding**.
