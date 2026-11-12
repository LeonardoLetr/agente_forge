# 05 — Métricas e desenho dos experimentos

## 1. Pergunta de pesquisa

> Em masmorras inéditas, arquiteturas de agentes com memória, reflexão e biblioteca de estratégias apresentam desempenho superior a agentes sem esses mecanismos, controlando custo, orçamento e complexidade do desafio?

Hipóteses a testar (não resultados):

- H1: memória entre episódios reduz repetição de erros em desafios semelhantes.
- H2: reflexão aumenta sucesso em problemas novos, mas pode elevar custo e latência.
- H3: biblioteca de habilidades melhora transferência entre masmorras de estruturas distintas.
- H4: Arquiteto adaptativo produz desafios de dificuldade melhor calibrada do que gerador fixo.
- H5: em determinados puzzles cooperativos, equipe especializada supera agente único sob orçamento equivalente.

## 2. Métricas primárias

| Métrica | Como calcular | Interpretação/cuidado |
|---|---|---|
| `success_rate` | vitórias / episódios | Principal; reportar por faixa de dificuldade. |
| `steps_to_success` | ações até concluir, entre vitórias | Pode favorecer ações rápidas mas arriscadas. |
| `failure_type` | classe da falha: HP, limite, puzzle, inválida | Ajudará a entender mecanismos. |
| `generalization_gap` | desempenho treino − desempenho teste | Gap grande indica possível sobreajuste. |
| `cost_per_success` | custo total / vitórias | Considerar testes sem vitórias; não dividir por zero. |
| `invalid_action_rate` | ações inválidas / ações propostas | Robustez do contrato e uso de tools. |
| `resource_efficiency` | recursos finais, dano, potions/loja | Contextualizar por mapa e objetivo. |
| `transfer_success` | taxa em tipos de puzzles não vistos | Medir habilidade reutilizável. |

Métricas secundárias: tokens de entrada/saída, latência por ação, incidentes de orçamento, inspeções redundantes, compras inúteis, dano por encontro, uso efetivo de memória, citações de memória verificáveis, sobrevivência e tempo computacional total.

## 3. Protocolo experimental

1. **Conjuntos separados:** `train` (ajuste), `validation` (calibração) e `test` (não acessível ao gerador/à memória dos agentes durante o desenvolvimento).
2. **Mesmos desafios:** V0–V3 enfrentam instâncias equivalentes do mesmo mapa, recursos e condições iniciais.
3. **Modelo constante:** ao medir memória ou reflexão, manter o mesmo modelo, temperatura e ferramentas quando viável; registrar quaisquer diferenças.
4. **Orçamento controlado:** definir max. ações, tokens, custo e tempo; comparar também a eficiência relativa.
5. **Várias seeds:** repetir em mapas variados, não apenas um mapa icônico; reportar tamanho da amostra e dispersão/incerteza.
6. **Congelar versões:** agente, prompts, motores, ferramentas e construtor do mapa são versionados antes de avaliar.
7. **Sem vazamento:** o Arquiteto adaptativo não recebe dados individuais ou respostas dos conjuntos holdout.
8. **Não confundir replay e nova rodada:** replay usa ações registradas; uma nova inferência é nova execução, mesmo com seed igual.
9. **Publicar falhas:** taxa de sucesso sem custo, falhas e casos ruins é incompleta.

## 4. Avaliações de adaptação

### Estudo A — Memória

Treinar/experimentar em 15 mapas de treino, avaliar em outros mapas da mesma família e em mapas com topologia diferente. Comparar V0/V1; inspecionar memórias relevantes e se eventos foram realmente recuperados antes das decisões.

### Estudo B — Reflexão

Comparar V1/V2. Recompensa não é "escreveu uma boa reflexão"; é sucesso, ações, custo, robustez e transferência após a reflexão.

### Estudo C — Especialização

Comparar personas e políticas diferentes com cenários de combate, compras e puzzles. Não concluir que preferências iniciais são aprendizado. Verificar quais estilos são robustos a mudanças de dificuldade.

### Estudo D — Arquiteto adaptativo

Comparar Arquiteto fixo e Arquiteto adaptativo. Medir validade, diversidade, taxa de sucesso alvo, estabilidade da dificuldade, variedade de recursos e generalização em exploradores novos. Penalizar mapas que bloqueiam todos ou repetem sempre o mesmo padrão.

### Estudo E — Cooperação

Criar puzzle realmente cooperativo (tarefas em posições diferentes com comunicação limitada). Comparar equipe com um agente que receba custo/tempo equivalente e condições de capacidade comparáveis.

## 5. Como saber se houve melhoria real

- Comparar contra baseline e versões anteriores, não só contra a primeira tentativa do mesmo agente.
- Avaliar em **cenários não vistos**; criar variações de puzzles e regras para evitar memorização superficial.
- Reportar taxa de sucesso por grupo, com medidas de incerteza (ex.: intervalo de confiança por bootstrap, quando amostra permitir).
- Analisar custo incremental de reflexão/memória e taxa de sucesso por orçamento.
- Revisar casos de erro qualitativamente, sem substituir avaliação por frases geradas pelo LLM.

## 6. Interface de relatórios

Relatório por execução:

```text
run_id: arena-042
agent_version: adaptive@v3
world_version: engine@0.2
model_version: provider/model-id
seed: 1234
status: WIN
steps: 42
invalid_actions: 1
puzzles_solved: 2
purchases: 1
memory_reads: 7
memory_writes: 3
skills_invoked: 1
tokens_input: <valor real>
tokens_output: <valor real>
cost: <valor real>
interventions: 0
```

Relatório de comparação: tabela por arquitetura, gráficos de evolução, falhas frequentes, custo por vitória, diferença entre treino/teste, versões congeladas e links para replays representativos.

## 7. Falhas que queremos detectar

- **Reward hacking:** o Arquiteto aprende a criar desafios tecnicamente válidos, mas trivialmente insolúveis na prática, maximizando a taxa de derrota.
- **Data leakage:** o Explorador recebe informações ocultas do mapa ou o Arquiteto recebe resultados de teste.
- **Self-evaluation bias:** um agente informa que melhorou sem evidências externas.
- **Memorization:** vitória em mapas idênticos não se transfere para outros.
- **Reward misspecification:** a pontuação incentiva gasto excessivo ou comportamento não desejado.
- **Non-determinism:** diferenças de resultados de LLM causadas por variações de inferência são interpretadas como aprendizado.
- **Metric gaming:** agente consegue alta taxa de sucesso usando custo inviável.