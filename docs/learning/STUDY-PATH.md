# Learning Path and Reading Resources — AgentForge

> Start with fundamentals, not with a framework. Study one concept, propose a design, implement it yourself, and then review the behavior.

## Module 1 — Agents, environments and observations (START HERE)

**Learning goals:** distinguish an agent from its environment; explain *percept/observation*, *action*, *state*, *transition*, *goal*, *partial observability* and *determinism*.

1. **UC Berkeley CS188 — 1.1 Agents**
   https://inst.eecs.berkeley.edu/~cs188/textbook/search/agents.html
   - Focus: what an agent observes and how it acts; fully vs partially observable environments.
   - Reading question: why shouldn't the explorer see the hidden key just because the engine knows where it is?
2. **UC Berkeley CS188 — 1.2 State Spaces and Search Problems**
   https://inst.eecs.berkeley.edu/~cs188/textbook/search/state.html
   - Focus: state space, actions, transition model, initial state, and goal test.
   - Reading question: what must be stored to tell whether a locked door can be opened?

**After reading:** describe, in words or a diagram, `world state → observation → agent action → validation → new world state`. Do not code yet.

## Module 2 — Environment contracts and deterministic tests

3. **Gymnasium — Basic Usage**
   https://gymnasium.farama.org/introduction/basic_usage/
   - Focus: `reset()`, `step(action)`, observations, rewards, termination, reproducible episodes.
   - You can read Python examples even if our implementation uses TypeScript; learn the contract, not the syntax.
4. **Gymnasium — Create a Custom Environment**
   https://gymnasium.farama.org/introduction/create_custom_env/
   - Focus: defining observations/actions, action validation, episode completion and testing.

**Exercise:** propose a minimal interface for creating/resetting a dungeon world, observing, applying an action, and returning the outcome. Define what happens for an invalid move. Compare two runs with the same seed and actions.

## Module 3 — Agent loops, tools and autonomy (AFTER module 2)

5. **Anthropic — Building Effective Agents**
   https://www.anthropic.com/research/building-effective-agents
   - Focus: workflows vs agents, tool-using loops, and choosing the simplest viable system.
   - Question: is a rules-based explorer still an agent? When is an LLM warranted?
6. **OpenAI — Agents SDK overview**
   https://developers.openai.com/api/docs/guides/agents/sdk
   - Focus: agent runtime, tool execution, sessions, tracing and orchestration. Read conceptually before adopting the SDK.
7. **OpenAI — Agents and orchestration**
   https://developers.openai.com/api/docs/guides/agents/orchestration
   - Focus: the difference between handoffs and agents as tools; revisit for the multi-agent dungeon.

**Exercise:** draw a single agent loop without using a framework. Explain which part is deterministic and which uses model reasoning.

## Modules to revisit later

- **Planning:** Berkeley search algorithms (BFS, DFS, UCS, A*) — https://inst.eecs.berkeley.edu/~cs188/textbook/search/
- **Memory & reflection:** design episodic records, relevance, summarization and controlled ablation tests.
- **Multi-agent:** compare cooperative vs isolated agents with equal budgets and identical scenarios.
- **Reinforcement learning:** use the Gymnasium interface as a bridge to policies and reward-based training. Do not confuse a saved lesson in memory with updating model parameters.
- **Evaluation & observability:** inspect tool traces, run events, seeds, cost, failure classification and held-out challenges.

## Reading discipline

- Read at most two core articles before the first design discussion.
- Write a summary in your own words (3–5 lines) and **one unanswered question**.
- Agree on a tiny exercise and an explicit acceptance criterion.
- Do not allow an AI assistant to write the educational implementation before the owner has attempted a design.
- When stuck, ask for help level 1, 2, 3 or 4 as defined in [mentorship guidelines](MENTORSHIP-GUIDELINES.md).

## First discussion prompt

> The world knows a key is hidden in room 3, but the explorer is in room 1. Should the response to `LOOK` include the key? Where is that decision made and why?

Answer in prose first. Consider the implications for both a dungeon and a corporate incident-investigation agent.
