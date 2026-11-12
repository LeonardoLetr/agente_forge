# 04 — Como interagir com o Dungeon Evolution Lab

## 1. Princípio de UX: três portas para o mesmo motor

**Tela (modo visual):** assistir, selecionar agentes, configurar experimentos, pausar, dar um passo, comparar, abrir memórias, inspecionar decisões e reproduzir eventos.

**CLI (modo pesquisador):** criar versões, lançar lotes, controlar execuções, obter logs e exportar resultados. Todos os comandos conversam com a API; não precisam reiniciar a aplicação.

**API (modo integração):** automatizar experimentos, construir bots de CI, dashboards externos e, no futuro, cenários empresariais.

A interface gráfica e a CLI chamam o **mesmo Control Plane**. Nenhuma deve manipular diretamente tabelas do banco ou HP/itens dos agentes.

## 2. Tela principal — wireframe proposto

```text
┌────────────────────── DUNGEON EVOLUTION LAB ──────────────────────────┐
│ Experimento: arena-042       [▶ Executar] [Ⅱ Pausar] [→ Passo] [⟲]  │
│ Seed: 1234   Cenário: dungeon-07   Modo: Arena   Tick: 38            │
├───────────────────────────────────────────┬──────────────────────────┤
│               MAPA / PIXIJS               │ AGENTES                  │
│                                           │ ● Scout  HP 70  Ouro 25  │
│       [loja]      [inimigo]                │ ● Warrior HP 42  Ouro 10│
│           ─── corredor ───                │ ● Solver HP 90  Ouro 35 │
│       [puzzle]     [saída]                 │ [Novo agente +]         │
│                                           ├──────────────────────────┤
│  [inspecionar sala] [seguir selecionado]   │ AGENTE SELECIONADO       │
│                                           │ Objetivo: obter a chave │
│                                           │ Última ação: INSPECT     │
│                                           │ Memórias: 4  Skills: 2   │
├───────────────────────────────────────────┴──────────────────────────┤
│ TIMELINE / EVENTOS                                                   │
│ t36 Scout  INSPECT door -> observou símbolo                          │
│ t37 Solver INTERACT lever -> puzzle 1/2                              │
│ t38 Warrior BUY potion -> ouro -10                                    │
│ [Filtrar por agent] [Ações] [Memórias] [Erros] [Tokens] [Exportar]     │
└───────────────────────────────────────────────────────────────────────┘
```

Não precisa animar uma decisão por token; o backend executa **turnos discretos** e o cliente desenha animações interpoladas entre estados.

## 3. Telas principais

1. **Lobby/Laboratório:** cenários, experimentos salvos, criar/duplicar execução.
2. **Editor de agentes:** clonar perfil, selecionar modelo, persona, memória, ferramentas, orçamento e versão.
3. **Editor de masmorras:** visualizar spec, testar solucionabilidade, ver razões de recusa, publicar.
4. **Arena ao vivo:** mapa, agentes, play/pause/step, HUD, eventos e linha do tempo.
5. **Inspetor:** observação recebida, ação proposta, resumo declarativo, ferramenta, resultado, memória recuperada/escrita, tokens/latência e política aplicada.
6. **Experimentos:** matrizes de comparação, ablações V0–V3, treino/teste, seeds e exportação CSV/JSON/Markdown.
7. **Replay/Branch:** navegar no histórico e criar um novo episódio derivado de um snapshot, marcado como nova execução.

## 4. O que você poderia fazer DURANTE uma execução

- Clicar no agente e seguir seus movimentos em tempo real.
- Pausar todos ou só o avanço do próximo turno de um agente, respeitando o scheduler.
- Avançar **um tick**, **uma ação** ou **até um breakpoint** (ex.: HP < 30, compra, falha de puzzle).
- Filtrar logs por agente, tipo de ação, custo, episódio e erro.
- Consultar memórias que influenciaram o contexto daquele turno e a lista de habilidades.
- Criar um novo agente pela tela ou CLI **e agendar entrada na próxima rodada/episódio**; entrada imediata apenas no modo sandbox e com evento de intervenção.
- Enviar um evento controlado (*apagão*, *mercador fechou*, *porta bloqueada*) em modo sandbox, não editar estado sem passar pelo motor.
- Duplicar episódio a partir de um snapshot e testar hipótese alternativa, preservando o original.
- Jogar manualmente como um explorador para observar diferenças, em modo exploratório.

**Regra científica:** alterações humanas no meio da execução são sinalizadas e não entram em benchmarks controlados sem tratamento próprio.

## 5. CLI proposta — especificação de experiência (não implementada)

```bash
# Gerenciamento
lab agents list
lab agents create --from scout --name observador-b
lab agents clone solver --name solver-memory --memory persistent
lab dungeons generate --difficulty 2 --rooms 8
lab dungeons validate dungeon-07

# Experimentos
lab experiments create --mode arena --dungeon dungeon-07 --agents scout,warrior,solver --seed 1234
lab run start arena-042
lab run watch arena-042                 # eventos contínuos no terminal
lab run pause arena-042
lab run step arena-042 --count 1
lab run resume arena-042
lab run stop arena-042

# Inspeção
lab agents inspect scout --run arena-042
lab logs tail arena-042 --agent scout --type action
lab memory inspect scout --run arena-042
lab replay open arena-042 --at 38
lab reports export arena-042 --format markdown

# Sandbox, não válido como experimento controlado
lab run inject arena-042 --event merchant_closed --at next-tick
lab run queue-agent arena-042 --agent solver-memory --join next-episode
```

A CLI deve ter autocompletar e também um **modo interativo REPL**: `lab shell`, com comandos reduzidos como `pause`, `step`, `agents`, `inspect scout`, `logs`, `resume`.

### Exemplo de saída de observação

```text
> inspect scout
Agente: scout@v2      Episódio: 12       Turno: 38
Local: corredor-norte  HP: 70  Ouro: 25
Objetivo: encontrar chave vermelha
Observação: símbolo próximo da porta e ruído ao norte
Memórias recuperadas: mem-91 (inspecionar símbolos antes de avançar)
Ação proposta: INSPECT target=door-red
Resultado: viu padrão [sol, lua, sol] (1 ação utilizada)
Resumo declarado: "Preciso investigar antes de gastar a chave."
Trace: trace-385      Latência: ...      Tokens: ...
```

## 6. API e eventos ao vivo

- `POST /experiments` cria experimento.
- `POST /experiments/:id/start|pause|resume|step|stop` controla execução.
- `POST /agents` cria configuração versionada; `POST /experiments/:id/agents/queue` agenda entrada.
- `GET /experiments/:id/events` consulta eventos com paginação; `GET /experiments/:id/replay` serve snapshots e eventos.
- `GET /agents/:id/memories` consulta registros autorizados.
- WebSocket com eventos como `episode.started`, `agent.decision`, `world.action_resolved`, `memory.written`, `episode.finished`.

**Cuidados:** idempotency key para comandos sensíveis, autenticação e autorização para controle remoto, limites de requisição, versionamento de schemas e reconexão com cursor de eventos. Em modo local, autenticação pode ser simples; acesso por rede requer proteção real.

## 7. Modos de observação

| Modo | Melhor para |
|---|---|
| **Cinema** | Assistir NPCs e masmorras sem sobrecarregar a tela. |
| **Cientista** | Ver ações, métricas, memórias, hipóteses e comparações. |
| **Debugger** | Step-by-step, breakpoints, payloads, traces, logs e erros. |
| **Diretor** | Inserir eventos controlados e criar agentes em modo sandbox. |
| **Benchmark** | Experimentos congelados, relatórios e séries de execuções. |

## 8. Interação futura avançada

- Comparar replays lado a lado, sincronizados pelo mesmo estado inicial.
- Linha do tempo ramificada: "e se ele comprasse poção em vez de espada?".
- Mapa de conhecimento por agente mostrando itens, pistas e habilidades descobertas.
- Experimentos A/B criados em uma linha de comando ou botão, sem editar código.
- Exportação do histórico para análise externa (CSV/Parquet).
- **Manual takeover**: assumir um personagem por N turnos, em modo sandbox, para estudar handoff humano-agente.

## 9. Escolha recomendada para o MVP

Não escolher *tela OU terminal*. Construir **um único backend com eventos** e dois clientes finos. A interface visual deve ser o principal meio de descobrir comportamentos; a CLI, o meio mais rápido de repetir experimentos, automatizar avaliações e inspecionar detalhes.