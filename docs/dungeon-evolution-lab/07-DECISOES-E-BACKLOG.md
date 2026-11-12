# 07 — Registro de decisões e backlog

## Decisões aceitas até agora

| ID | Decisão | Motivação |
|---|---|---|
| ADR-001 | Construir **laboratório de agentes**, não somente jogo. | Aprendizagem transferível a problemas reais. |
| ADR-002 | Começar pela **masmorra**; colônia espacial é evolução futura. | Problemas menores, ações e resultados observáveis. |
| ADR-003 | Um **Arquiteto** gera desafios e **vários Exploradores** tentam resolvê-los. | Estudar geração curricular e adaptação. |
| ADR-004 | Agentes podem ter preferências distintas em puzzles, combate e compras. | Criar diversidade de comportamentos observáveis. |
| ADR-005 | Ter **tela visual, terminal e API** sobre backend compartilhado. | Experimentação exploratória e automação. |
| ADR-006 | O mundo valida ações; LLM não controla consequências. | Segurança, consistência e avaliação justa. |
| ADR-007 | MVP em **arena isolada**; cooperação em fase posterior. | Controlar variáveis nas primeiras comparações. |
| ADR-008 | Avaliador primário **determinístico**. | Não confundir relato de agente com resultado real. |
| ADR-009 | Aprendizado inicial = memória/reflexão/habilidades; RL é posterior. | Progresso modular sem grandes custos de treino. |
| ADR-010 | Criador deve passar por validador; benchmarks de teste são isolados. | Evitar desafios impossíveis e vazamento experimental. |

## Decisões propostas, ainda abertas

| Tema | Padrão recomendado até decidirmos |
|---|---|
| Visual | Pixel art top-down 2D, com salas conectadas. |
| Framework UI | React + PixiJS. |
| Framework de agentes | Agent loop próprio primeiro, adapter para Agents SDK depois. |
| Backend | TypeScript/Node, modular monorepo. |
| Banco | SQLite na etapa inicial. |
| Modelo principal | Interface `ModelProvider`; escolher com base em custo, qualidade e logs. |
| Agendamento | Turnos discretos e fila, sem inferência por frame. |
| Puzzle inicial | Chave/porta ou sequência de alavancas com regras verificáveis. |
| Economia inicial | Ouro + loja + poção + duas classes de arma. |
| Configuração de agentes | Editor visual + comandos CLI, com versionamento. |

Esses padrões não são requisitos definitivos; mudanças exigem atualizar o ADR e os contratos.

## Backlog P0 — obrigatório no primeiro slice

- [ ] Schemas tipados de observação, ação, configuração e evento.
- [ ] Motor com seed e testes sem LLM.
- [ ] Uma masmorra fixa com vitória/derrota.
- [ ] Um explorador heurístico e um com LLM.
- [ ] Guardrails de ações, budget e timeout.
- [ ] Log append-only e replay de ações registradas.
- [ ] API para executar e pausar.
- [ ] UI com mapa, HUD e eventos.
- [ ] CLI com `start`, `watch`, `pause`, `step`, `logs`.

## Backlog P1 — experimentação

- [ ] Agentes `scout`, `warrior`, `merchant`, `solver` com configurações versionadas.
- [ ] Instâncias de mapa isoladas por agente (arena).
- [ ] Memória persistente e ablação V0/V1.
- [ ] Reflexão pós-episódio e ablação V2.
- [ ] Avaliação em múltiplas seeds, relatórios e exportação.
- [ ] Editar/clonar agente pela UI e agendar para próximo episódio.

## Backlog P2 — geração adaptativa

- [ ] Arquiteto de masmorras e validador.
- [ ] Validade de puzzle com testes automatizados.
- [ ] Gerador ajustando dificuldades com base em treino.
- [ ] Biblioteca de habilidades V3.
- [ ] Modo cooperativo e comunicação controlada.
- [ ] Replay ramificado e intervenções humanas sinalizadas.

## Backlog P3 — pesquisa e aplicação

- [ ] Experimentos com RL e políticas aprendidas.
- [ ] Benchmarks de transferência e generalização.
- [ ] Cenário de colônia espacial.
- [ ] Transferência para investigação de incidentes corporativos sintéticos.
- [ ] Testes de segurança, custo e resiliência operacional.

## Critérios para dizer "aprendeu"

- [ ] Existe mecanismo definido (memória, reflexão, skill ou treino de política).
- [ ] A mudança é observável/registrada e ocorreu antes da avaliação.
- [ ] Há comparação contra baseline ou versão anterior.
- [ ] A melhora aparece em cenários não usados para ajustar o agente.
- [ ] Custos, erros e dispersão dos resultados são reportados.

## Próxima ação concreta

Criar o monorepo de M0/M1 e um **primeiro episódio end-to-end sem LLM**. Isso reduz o risco de construir interface e prompts sobre regras ainda instáveis. Só então integrar o primeiro agente LLM.