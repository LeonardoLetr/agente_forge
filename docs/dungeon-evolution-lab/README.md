# Dungeon Evolution Lab

> **Status:** projeto conceitual e documentação inicial. **Não há aplicação implementada.**
> **Versão das decisões:** 0.1 — 10/11/2026.

## Visão

Um laboratório visual e reproduzível para estudar **agentes autônomos que planejam, executam ferramentas, acumulam experiências e adaptam estratégias** em masmorras geradas por outro agente. O projeto começa como ambiente de experimentação e evolui para uma plataforma capaz de testar arquiteturas úteis em sistemas empresariais.

### Ideia central

1. Um **Arquiteto** propõe masmorras com salas, inimigos, puzzles, equipamentos, lojas e recursos.
2. Um **Validador determinístico** verifica regras, recursos necessários, limites e solucionabilidade; o Arquiteto não pode alterar o mundo livremente.
3. Diversos **Exploradores** enfrentam os desafios usando estratégias e mecanismos de aprendizado distintos.
4. O **Motor de Mundo** é a fonte da verdade: decide consequências, dano, compras, inventário e progressão.
5. Um **Avaliador independente** calcula métricas, compara versões e testa generalização em desafios inéditos.
6. Uma **interface visual**, uma **CLI** e uma **API** expõem a mesma simulação e os mesmos eventos.

## O que queremos aprender

- Autonomia: como um agente percebe, decide, usa ferramentas e executa uma tarefa de múltiplas etapas.
- Planejamento: como escolhe entre explorar, combater, investigar e comprar.
- Memória: o que muda quando retém experiências anteriores.
- Reflexão e habilidades: quando estratégias reutilizáveis melhoram o desempenho.
- Multiagentes: como agentes especializados competem ou cooperam.
- Geração adaptativa de desafios: como o Arquiteto cria um currículo progressivo sem gerar fases inválidas.
- Engenharia: estado persistente, rastreabilidade, custos, limites, falhas e segurança.

## O que **não** vamos afirmar sem medir

- Que o LLM atualizou seus pesos quando apenas salvou memórias.
- Que uma persona agressiva ou cautelosa é um mecanismo de aprendizado.
- Que vencer repetidamente a mesma masmorra demonstra generalização.
- Que ter mais agentes significa obter melhores resultados.
- Que uma justificativa textual do agente revela seu raciocínio interno completo.

## Princípios de projeto

1. **Separar regra de jogo, inteligência e avaliação.** O LLM produz intenções/ações; código valida e aplica consequências.
2. **Reprodutibilidade.** Registrar seed do mapa, versão do motor, cenário, agente, modelo, configuração, custo e sequência de eventos; modelos externos podem continuar sendo probabilísticos.
3. **Justiça experimental.** Ao comparar arquiteturas, manter constantes modelo, orçamento e desafios sempre que possível.
4. **Aprendizado observável.** Registrar memória escrita/recuperada, hipótese ou estratégia declarada e desempenho em desafios novos.
5. **Controle humano.** Pausa, passo a passo, replay, limites de execução e trilha de auditoria.
6. **Modularidade.** Começar sem dependência obrigatória de framework de agentes; integrar SDKs depois.
7. **Permissões mínimas.** Ferramentas do agente só podem acessar ações explicitamente permitidas pela simulação.

## Decisões iniciais

- **Stack preferencial:** TypeScript/Node.js; React + PixiJS; HTTP/REST + WebSocket; SQLite na etapa inicial e PostgreSQL quando necessário.
- **Primeiro modo:** *arena comparativa* — agentes disputam **instâncias separadas da mesma masmorra** (mesmo seed e conteúdo), evitando interferência entre eles.
- **Segundo modo:** *grupo cooperativo* — múltiplos agentes no **mesmo mundo** compartilhando parte dos recursos e mensagens.
- **Memória:** separação entre memória do episódio, memória entre episódios e biblioteca de estratégias.
- **Aprendizado inicial:** memória, reflexão e transferência de estratégias; RL com atualização de políticas é estudo futuro.
- **Criador de masmorras:** LLM propõe, código valida, simulador testa, avaliação pontua.
- **Observabilidade:** eventos estruturados primeiro; logs legíveis e visualização derivados deles.

## Como ler o pacote

| Documento | Para que serve |
|---|---|
| [01-ARQUITETURA.md](01-ARQUITETURA.md) | Componentes, fluxo de dados, modos de simulação e responsabilidades. |
| [02-AGENTES-E-ESTRATEGIAS.md](02-AGENTES-E-ESTRATEGIAS.md) | Definição do Arquiteto, exploradores e abordagens de aprendizado. |
| [03-ROADMAP.md](03-ROADMAP.md) | Marcos de implementação, critérios de aceite e sequência técnica. |
| [04-INTERACAO-UX-CLI.md](04-INTERACAO-UX-CLI.md) | Como operar pela tela, terminal, comandos ao vivo e replays. |
| [05-METRICAS-E-EXPERIMENTOS.md](05-METRICAS-E-EXPERIMENTOS.md) | Hipóteses, métricas, desenho de testes e riscos de avaliação. |
| [06-CONTRATOS-E-EXEMPLOS.md](06-CONTRATOS-E-EXEMPLOS.md) | JSON e endpoints **propostos**, para orientar implementação. |
| [07-DECISOES-E-BACKLOG.md](07-DECISOES-E-BACKLOG.md) | Decisões tomadas, em aberto e funcionalidades adiadas. |
| [08-APLICACOES-EMPRESARIAIS.md](08-APLICACOES-EMPRESARIAIS.md) | Como aproveitar o que foi aprendido em sistemas reais. |

## Referências técnicas consultadas

- [OpenAI Agents SDK — conceitos](https://openai.github.io/openai-agents-js/guides/agents/) e [tracing](https://openai.github.io/openai-agents-js/guides/tracing/).
- [LangGraph — persistência e checkpoints](https://github.com/langchain-ai/langgraphjs/blob/main/docs/docs/concepts/persistence.md).

> **Nota:** exemplos de terminal, APIs, dados e telas deste pacote são **design proposto**, não recursos que já funcionam.