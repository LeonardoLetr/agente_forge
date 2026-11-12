# 08 — Tradução do laboratório para aplicações empresariais

## 1. Qual é a conexão real?

A masmorra isola problemas encontrados em produtos de IA: decidir qual ferramenta chamar, planejar múltiplas etapas, preservar estado, lidar com falhas, avaliar custos, recuperar informações e coordenar especialistas. A interface de jogo é um **simulador controlado** para estudar mecanismos, não uma prova automática de confiabilidade em produção.

| Dungeon Lab | Aplicação empresarial plausível |
|---|---|
| Explorar corredor desconhecido | Investigar sistema/serviço com observabilidade parcial. |
| Inspecionar porta e pista | Consultar logs, runbooks, documentação e métricas. |
| Comprar poção vs. espada | Selecionar ferramenta ou alocar orçamento de execução. |
| Combate com recursos limitados | Resolver tarefas com limites de custo, tempo e risco. |
| Falhar, memorizar e refletir | Registrar lições de incidentes e recuperar casos parecidos. |
| Criador gerar masmorra adversarial | Gerar testes de robustez e cenários de falha válidos. |
| Equipe cooperar no puzzle | Orquestração de agentes especialistas e delegação. |
| Replay e logs de cada decisão | Tracing, auditoria, avaliação e suporte operacional. |

## 2. Primeiro caso real recomendado: investigação de incidentes (somente leitura)

### Cenário

Um monitor detecta latência alta em serviço fictício. Um **agente coordenador** atribui tarefas a especialistas:

- **Logs:** identifica exceções e padrões temporais.
- **Métricas:** correlaciona latência, CPU, memória e erros.
- **Documentação:** procura runbooks e mudanças recentes.
- **Diagnóstico:** integra evidências e propõe hipóteses com graus de confiança.

O sistema retorna uma **proposta explicável** de causa provável, evidências, limitações e ações sugeridas. Alterações de produção dependem de aprovação humana e integração explicitamente permitida.

### Métricas reais de utilidade

Precisão de diagnóstico em casos sintéticos com gabarito; tempo até hipótese correta; quantidade de chamadas; custo por caso; evidências adequadamente citadas; escalonamento quando há baixa confiança; taxa de recomendações indevidas.

### Equivalência arquitetural

O `WorldEngine` passa a ser um conjunto de ferramentas de consulta **somente leitura**; o `Observation` passa a trazer telemetria; o `Agent Runtime` permanece modular; `Evaluation` utiliza cenários com diagnóstico conhecido; a tela continua exibindo eventos e replays.

## 3. Habilidades demonstráveis no currículo

- Orquestração e gerenciamento de estado de agentes.
- Projeto de ferramentas com validação de contratos.
- Memória, recuperação, reflexão e avaliação baseada em evidências.
- Observabilidade e rastreabilidade de decisões.
- Benchmarks reproduzíveis, comparação e análise de regressões.
- Controles de execução: quotas, timeouts, sandbox, allowlists, aprovação humana.
- Design de sistemas distribuídos e de interfaces de investigação.

## 4. Como apresentar o portfólio sem exagerar

**Mostrar:** vídeo curto da arena, diagrama técnico, código-fonte, conjunto de cenários, scripts reproduzíveis, resultados e limitações.

**Descrever como:** *Visual multi-agent simulation and evaluation platform for planning, memory, adaptive strategies, and reproducible agent benchmarking.*

**Não descrever como:** *treinei um novo modelo de IA autoaprendente* se só houve memória textual e prompts.

## 5. Evolução para colônia espacial

A colônia permite simular vários subsistemas e especialistas com recursos conflitantes. O paralelo empresarial mais forte é gestão coordenada de sistemas: serviços interdependentes, pressão de recursos, detecção de falhas e planejamento de mitigação. O experimento da colônia passa a ser um **módulo adicional** do motor, não outro projeto inteiro.

## 6. Salvaguardas

- Acesso externo e ferramentas de produção desligados por padrão.
- Ferramentas separadas por permissão, com validação e auditoria.
- Ações com efeitos externos exigem aprovação explícita e justificativa apoiada em evidências.
- Prompt injection e dados não confiáveis tratados como entrada, jamais como autoridade.
- Logs redigidos para não armazenar credenciais ou dados privados inadvertidamente.
- Avaliar qualidade, custo, risco e incerteza, não somente conclusão de tarefas.