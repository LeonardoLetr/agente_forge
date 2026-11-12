# 02 — Tipos de agentes e estratégias iniciais

## 1. Separar persona, política e mecanismo de aprendizado

Uma **persona** (cauteloso, guerreiro, economista) é uma preferência inicial. Uma **política** decide ações; pode ser heurística, LLM ou RL. Um **mecanismo de aprendizado** muda o comportamento ao longo de episódios por memória, reflexão, habilidades ou treinamento de parâmetros. Essas dimensões devem ser configuráveis de forma independente.

`AgentConfig = Identidade + Perfil de decisão + PolicyAdapter + Ferramentas + MemoryAdapter + LearningAdapter + Budgets + Versão`.

**Controle de comparação:** perfis diferentes geram diversidade visível; para concluir que *memória* foi útil, testar o mesmo perfil/modelo com memória ligada e desligada.

## 2. Arquiteto da masmorra

**Missão:** gerar desafios válidos, variados e progressivamente adequados à competência observada dos exploradores.

**Entradas:** tema, dificuldade-alvo, habilidades testadas, seed, teto de salas/inimigos/recursos, histórico de resultados do conjunto de treinamento.

**Saída:** `DungeonSpec` validado, incluindo salas, conexões, puzzles, inimigos, lojas, loot e condição de vitória.

**Ferramentas propostas:** `propose_dungeon`, `validate_dungeon`, `simulate_baselines`, `revise_dungeon`, `publish_dungeon`.

**Estratégia inicial:** gerador híbrido. O código gera topologia estrutural básica e verifica invariantes; o LLM sugere combinações, narrativa, puzzles tipados e evolução curricular. O Arquiteto recebe resultados **agregados de treino**, não gabaritos nem seeds de teste.

**Objetivo de ajuste:** maximizar variedade e poder discriminativo dos desafios, mantendo uma faixa-alvo de sucesso — não simplesmente matar todos os exploradores. Rejeitar cenários inviáveis.

## 3. Exploradores: catálogo inicial

| Identificador | Preferências iniciais | Decisões típicas | Risco que queremos observar |
|---|---|---|---|
| `baseline-random` | Seleção aleatória de ações válidas | Explorar sem estratégia | Referência de desempenho mínimo |
| `baseline-heuristic` | Regras fixas | Curar HP baixo; evitar batalha desvantajosa | Limite de políticas sem LLM |
| `scout` | Exploração e informação | Inspecionar salas, mapear rotas, poupar HP | Inspeção excessiva e custo alto |
| `warrior` | Combate e progressão rápida | Comprar ataque, enfrentar inimigos | Morre por assumir riscos excessivos |
| `merchant` | Gestão de recursos | Comparar preços, guardar ouro, comprar poções | Economia excessiva, falta de poder |
| `solver` | Puzzles e inferência | Inspecionar pistas, formular hipótese, testar mecanismo | Gastar muitos turnos ou repetir falhas |
| `adaptive` | Ajustar decisões com feedback | Alterar estratégias, recuperar memórias, refletir após falhas | Confundir adaptação real com narrativa |

**Importante:** nenhum desses perfis precisa ser uma instância de modelo separada permanentemente. Podem ser configurações independentes sobre o mesmo serviço de inferência, ativadas por turno.

## 4. Quatro variantes controladas para estudar aprendizado

| Variante | LLM | Memória persistente | Reflexão | Biblioteca de habilidades |
|---|---|---|---|---|
| `V0` | Sim | Não | Não | Não |
| `V1` | Mesmo | Sim | Não | Não |
| `V2` | Mesmo | Sim | Sim, após episódio | Não |
| `V3` | Mesmo | Sim | Sim | Sim |

Manter constantes as ferramentas, os recursos iniciais, o modelo e o orçamento, ou registrar e controlar diferenças. Em cenários com custos de reflexão, avaliar também o orçamento extra consumido.

### Tipos de memória

- **Working memory:** percepção e objetivos atuais dentro do turno/episódio.
- **Episodic memory:** eventos relevantes de uma tentativa específica.
- **Cross-episode memory:** resumos de tentativas anteriores, com limites e validade.
- **Skill library:** procedimento reutilizável com pré-condições, passos, evidências de sucesso e versão.
- **Shared memory:** opcional no modo cooperativo; exige política de acesso e proveniência.

### Como registrar aprendizado sem exagerar conclusões

Exemplo: após morrer em um corredor, o agente registra: *“Antes de atravessar corredores com placas suspeitas, usar INSPECT se o custo permitir.”* Essa é uma **estratégia verbal persistida**, não prova de que os pesos do LLM foram treinados.

Depois, testar a estratégia em corredores com layouts inéditos. Só considerar melhora se métricas sustentarem o efeito, comparadas com versões de controle.

## 5. Avaliador: preferir código determinístico

Em vez de depender de um LLM-juiz para pontuar vitórias, computar vitória/derrota, HP, turnos, ouro, puzzles, compras e ações inválidas diretamente de eventos. Pode existir um **Analista LLM opcional** para resumir episódios, mas seus resumos não substituem métricas independentes.

## 6. Outros papéis opcionais para fases posteriores

- **Mentor:** propõe currículo de treinos com base em déficits medidos.
- **Crítico:** sugere correções de estratégia após um episódio, sem editar resultados.
- **Equipe cooperativa:** cartógrafo, tanque, curandeiro e negociador, com comunicação limitada.
- **Auditor de segurança:** detecta ações inválidas, prompt injection e tentativas de contornar ferramentas.
- **Agente humano:** jogador manual com as mesmas ações que as políticas de IA, útil para referência exploratória (não necessariamente comparação estatística justa).

## 7. Exemplos de decisões a observar

- **Combate:** lutar, defender, usar item, recuar; comparar dano esperado com HP e probabilidade de sobrevivência.
- **Economia:** comprar arma agora ou poupar para uma poção posterior; registrar o impacto da escolha.
- **Puzzle:** usar pistas, testar hipóteses, verificar pré-condições e evitar tentativas redundantes.
- **Exploração:** escolher corredores desconhecidos, voltar para uma loja, evitar zonas de risco.
- **Colaboração:** compartilhar mapa, reservar itens, negociar ordem das ações e atribuir papéis.

## 8. Objetivos mínimos por agente

Cada agente precisa ter: identificador, versão imutável, descrição visível, policy adapter, ferramentas permitidas, limites de ações/tokens/custo, estratégia inicial, namespace de memória, modo de execução, conjunto de episódios e histórico de alterações.

Mudanças ao vivo criam **nova versão** ou uma intervenção registrada. Não substituir silenciosamente a política de um agente no meio de uma avaliação controlada.