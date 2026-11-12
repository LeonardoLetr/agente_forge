# 03 — Roadmap técnico e critérios de aceite

> Plano de implementação, **não** promessa de funcionalidades existentes. Os marcos são sequenciais; sem estimativas artificiais de prazo.

## M0 — Fundação e contratos

**Entregar:** monorepo TypeScript, contratos `AgentConfig`, `DungeonSpec`, `Action`, `Observation`, `EpisodeEvent`, testes e comando simples de execução.

**Aceite:** qualquer evento pode ser validado por schema; o motor executa offline sem LLM nem interface; estrutura de pacotes está modular.

## M1 — Motor do mundo jogável sem IA

**Entregar:** mapa pequeno, navegação em grade/salas, HP, ouro, inventário, inimigo simples, puzzle de chave/alavanca, loja, condição de vitória, RNG seeded e política heurística.

**Aceite:** 100 episódios locais automáticos terminam sem corromper estado; repetir sequência de ações e seed reproduz o mesmo resultado; regras inválidas não alteram o estado.

## M2 — Primeiro explorador LLM + observabilidade

**Entregar:** adapter de modelo, observação parcial, seleção de ação tipada, loop com limite de passos e custo, eventos estruturados e visualização básica de decisões.

**Aceite:** agente consegue executar um episódio até vitória, derrota ou limite; cada ação possui input, validação e consequência; execução pode ser reproduzida a partir dos eventos gravados.

## M3 — Interface visual e operação ao vivo

**Entregar:** React + PixiJS, mapa, HUD de agentes, painel de eventos, controles play/pause/step, filtros, CLI local e WebSocket para projeção de eventos.

**Aceite:** criação de experimento pela tela ou CLI; pausa efetiva entre turnos; painel mostra ação, resultado, inventário e custo; replay consegue avançar/voltar sem novas chamadas ao modelo.

## M4 — Biblioteca de agentes + memória

**Entregar:** baseline aleatório, heurístico, explorador LLM, versões `V0`/`V1`/`V2`; registro de memórias; arena comparativa; relatórios por episódio e agente.

**Aceite:** múltiplos agentes enfrentam **cópias isoladas** do mesmo cenário; a diferença de configuração fica registrada; é possível consultar quais memórias foram recuperadas e salvas; política não muda sem versionamento.

## M5 — Arquiteto gerador e validador

**Entregar:** especificação tipada da masmorra, gerador de candidatos, regras rígidas, verificador de solucionabilidade para puzzles implementados, revisões automáticas e publicação de desafios.

**Aceite:** gerador não consegue publicar mapas inválidos; cada recusa tem causa; mesmo mapa pode ser testado por diferentes políticas com seed controlada.

## M6 — Aprendizado e experimentação rigorosa

**Entregar:** biblioteca de estratégias `V3`, ablação V0–V3, cenários holdout, testes com múltiplas seeds, análise de custo, intervalos de incerteza e exportação de relatórios.

**Aceite:** relatório separa treino/teste; compara sucesso, custo, ações e falhas; marca intervenções humanas; não confunde memória com fine-tuning.

## M7 — Coevolução e grupos cooperativos

**Entregar:** currículo adaptativo do Arquiteto, limites de dificuldade, mais de um agente no mesmo mundo, comunicação tipada entre agentes e conflitos de recursos.

**Aceite:** masmorras permanecem válidas; o criador não vê respostas de testes cegos; é possível medir se cooperação supera agente único considerando custo e budget.

## M8 — Transferência para cenário empresarial

**Entregar:** protótipo de investigação **somente leitura** de incidentes sintéticos, agentes especialistas, trilha de evidências, propostas de correção e política explícita de aprovação humana.

**Aceite:** ferramentas têm permissões mínimas; nenhuma alteração de produção ocorre automaticamente; comparação com workflow single-agent e logs auditáveis.

## Ordem prática sugerida de estudos

1. FSMs/máquinas de estado, simulação discreta, contrato de actions.
2. LLM tools + structured output + idempotência e validação.
3. Event sourcing, replay, bancos e observabilidade.
4. Memória e recuperação, reflexão e avaliação.
5. Multi-agent orchestration e resolução de conflito.
6. Currículo automático, generalização e fundamentos de RL.

## O que conscientemente não entra no MVP

- Milhares de agentes LLM ativos simultaneamente.
- Treinar/fine-tunar LLM próprio.
- Gráficos 3D, mundo aberto e física complexa.
- Sistema econômico complexo e mercado entre agentes.
- IA livre para executar comandos de shell ou código arbitrário.
- Uso de resultados da própria prova de teste para treinar o Arquiteto.

## Primeira demonstração vertical (slice)

Uma masmorra fixa com 6–10 salas, 1 inimigo, 1 loja, 1 puzzle e 1 saída. Um agente heurístico e um LLM competem em cópias idênticas. A tela mostra cada ação e seu efeito. O terminal consegue iniciar, pausar e exportar o episódio. Este slice já prova a arquitetura e reduz riscos antes da geração procedural.