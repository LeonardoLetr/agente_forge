# 06 — Contratos técnicos e exemplos

> **Tudo aqui é especificação proposta, não APIs existentes.** O objetivo é facilitar a implementação da fase M0.

## 1. `DungeonSpec` — cenário

```json
{
  "schemaVersion": 1,
  "id": "dungeon-07",
  "seed": 1234,
  "difficulty": 2,
  "startRoom": "entry",
  "victoryCondition": { "type": "REACH_EXIT", "roomId": "exit" },
  "rooms": [
    { "id": "entry", "kind": "start", "connections": ["hall"] },
    { "id": "hall", "kind": "corridor", "connections": ["entry", "shop", "puzzle"] },
    { "id": "shop", "kind": "merchant", "connections": ["hall"] },
    { "id": "puzzle", "kind": "puzzle", "connections": ["hall", "exit"], "puzzleId": "lever-1" },
    { "id": "exit", "kind": "exit", "connections": ["puzzle"] }
  ],
  "puzzles": [{ "id": "lever-1", "type": "SEQUENCE", "sequence": ["sun", "moon"], "reward": { "unlock": "exit" } }],
  "shops": [{ "roomId": "shop", "inventory": [{ "item": "potion", "price": 10, "stock": 2 }] }],
  "enemies": [],
  "startingResources": { "hp": 100, "gold": 25, "items": [] }
}
```

O verificador precisa conhecer o significado de `sequence` e testar se o puzzle é executável com as ações e informações disponíveis. A presença de caminhos em grafo **não** basta para declarar toda dungeon solucionável.

## 2. `AgentConfig` — perfil versionado

```json
{
  "schemaVersion": 1,
  "agentId": "scout",
  "version": 2,
  "displayName": "Scout",
  "persona": "explorador cauteloso e atento a pistas",
  "policy": { "kind": "llm", "modelRef": "provider/model-id", "temperature": 0.2 },
  "learning": { "memory": "persistent", "reflection": "post_episode", "skills": false },
  "tools": ["MOVE", "INSPECT", "INTERACT", "ATTACK", "DEFEND", "BUY", "USE_ITEM", "WAIT", "EXIT"],
  "limits": { "maxActionsPerEpisode": 150, "maxModelCalls": 80, "maxTokens": 80000, "maxCostUsd": 3.0 }
}
```

Os valores dos limites acima são **exemplos configuráveis**, não uma recomendação de gasto nem uma medida real de custo.

## 3. `Observation` — percepção parcial

```json
{
  "agentId": "scout",
  "turn": 38,
  "location": "hall",
  "status": { "hp": 70, "gold": 25 },
  "visible": { "doors": [{ "id": "door-red", "locked": true }], "enemies": [], "items": [] },
  "availableActions": ["MOVE", "INSPECT", "WAIT"],
  "lastActionResult": "moved_to_hall"
}
```

Jamais incluir o mapa completo, posições ocultas, solução de puzzle ou random state em `Observation`, exceto em experimentos que explicitamente concedam essa informação.

## 4. `Action` — resposta do agente

```json
{
  "schemaVersion": 1,
  "agentId": "scout",
  "type": "INSPECT",
  "targetId": "door-red",
  "decisionSummary": "A porta está trancada; preciso procurar uma pista visível."
}
```

O resumo é uma **declaração sintética**, não chain-of-thought. Validar `type`, `targetId`, turno, range, status do mundo e permissões antes de aplicar a ação.

## 5. `EpisodeEvent` — log append-only

```json
{
  "schemaVersion": 1,
  "eventId": "event-00038",
  "runId": "arena-042",
  "episodeId": "episode-12",
  "tick": 38,
  "timestamp": "2026-11-10T23:31:00Z",
  "type": "world.action_resolved",
  "agentId": "scout",
  "agentVersion": 2,
  "worldEngineVersion": "0.2.0",
  "payload": {
    "action": { "type": "INSPECT", "targetId": "door-red" },
    "result": { "status": "OK", "observed": "sun_moon_symbol" },
    "stateDelta": { "actionsUsed": 1 }
  },
  "traceId": "trace-example-38"
}
```

O timestamp acima é **ilustrativo**. Em produção, gerar na execução. Incluir `sequence`/cursor monotônico por run para sincronização após desconexão, além de idempotência para comandos.

## 6. Contrato de memória e habilidades

```json
{
  "memoryId": "mem-91",
  "agentId": "scout",
  "scope": "cross_episode",
  "sourceEpisodeId": "episode-08",
  "content": "Inspecionar símbolos antes de testar portas pode evitar perder recursos.",
  "tags": ["puzzle", "door", "inspection"],
  "confidence": 0.6,
  "createdAt": "<registrado pelo servidor>"
}
```

```json
{
  "skillId": "skill-check-door",
  "version": 1,
  "name": "Inspect locked door",
  "preconditions": ["door_visible", "inspection_available"],
  "procedure": ["inspect_door", "collect_visible_clues", "select_next_action"],
  "evidence": { "observedSuccesses": 0, "observedFailures": 0 }
}
```

Memórias e habilidades precisam de proveniência: uma estratégia inferida pelo agente não é fato do mundo. A habilidade pode ser acionada apenas quando as pré-condições forem validadas.

## 7. Comando de laboratório e eventos WebSocket

Exemplo de request desejado:

```http
POST /experiments
Content-Type: application/json

{
  "mode": "arena",
  "dungeonId": "dungeon-07",
  "agentVersions": ["scout@v2", "warrior@v1"],
  "seed": 1234,
  "limits": { "maxEpisodeActions": 150 }
}
```

Exemplo de WebSocket emitido pelo servidor:

```json
{
  "channel": "runs/arena-042",
  "cursor": 38,
  "event": "agent.decision",
  "data": {
    "agentId": "scout",
    "action": { "type": "INSPECT", "targetId": "door-red" },
    "decisionSummary": "Vou investigar a porta antes de avançar."
  }
}
```

## 8. Pseudocódigo do agent loop

```ts
while (!world.terminal && budget.hasRoom()) {
  const observation = world.observe(agent.id);
  const memories = await memory.retrieve(agent.id, observation);
  const proposal = await policy.decide({ observation, memories, tools: allowlist });
  const validation = actionSchema.safeParse(proposal.action);
  const outcome = validation.success
    ? world.applyAction(agent.id, validation.data)
    : { status: 'INVALID_ACTION', details: validation.error };
  await eventStore.append({ observation, proposal, outcome });
  await memory.maybeStore(agent.id, observation, proposal, outcome);
  await snapshotStore.maybeSave(world);
}
```

Esse pseudocódigo simplifica o scheduler; na implementação real ele tem que lidar com limites, timeouts, cancelamento, tratamento de erro, múltiplos agentes e confirmação transacional.