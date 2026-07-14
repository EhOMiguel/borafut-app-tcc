# 04 - Plano TCC v2: BoraFut (correção TCC1 + atualização + fechamento TCC2)

> Entregável principal. Cobre: (a) cada ponto de `correções/pontos_a_corrigir_tcc1.md`
> mapeado para ação concreta; (b) cada divergência monografia ↔ estado atual do projeto,
> com capítulo/seção e o que reescrever; (c) estrutura proposta do TCC2 justificada pelos
> 3 TCCs de referência e pelo nosso contexto; tudo sequenciado contra os prazos reais.
> Insumos: `01-avaliacao-produto.md`, `02-avaliacao-codigo.md`, `03-avaliacao-fluxo-ia.md`.
> Escrito para ser executável por qualquer dev ou IA sem contexto adicional.

## Sumário executivo

1. **Baseline**: a monografia NÃO está no estado de dezembro/2025. O working tree já contém
   correções aplicadas (Cap1 delimitação de escopo, Cap3 reescrito com stack real e tabela
   de versões, matriz de mercado no Cap4, RNF reescritos com rastreabilidade, Figura 9 e
   refs de tabela corrigidas). Este plano parte desse baseline e dá os 20 pontos da banca
   como: 6 concluídos, 5 parciais, 9 a fazer.
2. **Estrutura alvo**: 8 capítulos. Mantêm-se os 5 atuais (corrigidos) e entram
   **Cap 6 Desenvolvimento** (com seções únicas "Motor do fut" e "Tempo real"),
   **Cap 7 Resultados e Validação** (usabilidade + SUS + beta fechado) e
   **Cap 8 Conclusão**. Apêndices reorganizados com 4 novos (TCLE, specs de casos de uso,
   dicionário de dados, roteiro/SUS) e protótipos como último apêndice.
3. **Decisões herdadas das suas respostas (11/06)**: monetização fica como perspectiva
   futura COM declaração explícita de núcleo gratuito permanente; texto descreve tudo como
   implementado (sem "pendente") com **regra de freeze**: o que não estiver implementado
   até 03/08 sai do texto ou vira trabalho futuro; vocabulário criador/organizador em todo
   o Cap 5; conscientização (B18) entra como aba enxuta com gate no freeze.
4. **Diagramas**: casos de uso e C4 (contêineres + componentes) serão gerados por
   PlantUML/C4-PlantUML (saída de ferramenta, notação padrão, confiável); o MER conceitual
   (Chen) você desenha no brModelo. O `specs-diagramas-banca.md` existente é a base, mas
   precisa de atualização para os papéis criador/organizador e o fluxo completo do
   fut_in_app antes de qualquer desenho.
5. **Acoplamento com o produto**: o plano sincroniza escrita e desenvolvimento. Itens do
   doc 02 que bloqueiam o cronograma do TCC: SES real (sem ele não há beta em julho, e sem
   beta o Cap 7 perde a fonte de dados reais) e bounding box (sem ele o RNF02 do texto é
   falso). Ambos entram na semana 1.
6. **Riscos de calendário** monitorados: saída do sandbox AWS SES (prazo externo),
   review das lojas para lançar em 10/09 (submeter até ~29/08), e beta atrasar
   (fallback: Cap 7 sustenta-se em usabilidade + SUS, beta vira relato qualitativo).

---

## 0-bis. Adendo de estado — 13/07/2026 (auditoria completa; atualiza os status abaixo)

**DIRETRIZ DE ESCRITA (decisão do Miguel, 13/07 — substitui a regra de freeze §8.2 como
regra operativa do texto):** a monografia descreve o **MVP completo, como aplicativo
pronto e recém-publicado nas lojas** — todos os fluxos, módulos e integrações previstos
(inclusive card pós-fut, ativação de gerenciamento, coleta passiva, SES, S3, push) são
narrados como fato. Seções ainda não escritas (Cap6 6.5-6.8, Cap7, Cap8, P20 §Modelo de
dados) serão produzidas em sessões dedicadas; o mapa de prontidão do insumo de cada uma
está no comentário ao fim de `desenvolvimento.tex`. As tabelas §3 (P01-P20) e §4 (D*/NV*)
continuam sendo o guia de escrita — status conferidos e atualizados nesta auditoria.

Reauditoria de código+texto em 13/07. O que muda em relação às tabelas §3/§4 e ao
cronograma §7 (o corpo abaixo permanece como plano original):

**Monografia — concluído desde 11/06** (working tree, 15 commits): P07b (3 diagramas UC +
apêndice com 11 UCs), P12 (backlog 6 níveis + matriz), P20 PARCIAL (dicionário feito na baseline de 15/06,
mas DECISÃO de 13/07 manda regerá-lo do schema COMPLETO atual — tabelas operacionais
entram; falta também MER Chen + lógico + rework do §Modelo de dados), D06/D07/D09/D10/D10b (Cap5
reescrito: fluxos, RFs, papéis, C4 com componentes), NV1/NV2 (US novas), P05 (observação),
**Cap6 "Desenvolvimento" criado** (6.1-6.4; 6.5-6.8 corretamente comentadas até o freeze).

**Monografia — segue pendente:** P06 (brainwriting doc à parte), P09 (apêndice protótipos),
P13 (passe ABNT), P14 (TCLE), P18 (conscientização — gate), P20-resto (MER), D11-resto
("oito" capítulos), N3 (tempo verbal), NV4 (resumo), Caps 7-8. Correções pontuais achadas
na auditoria de 13/07 (Cap3: SES/S3 descritos como fato antes de existirem; §SGBD afirma
filtragem por bounding box que o código não faz; "fotos de perfil" não existem;
Cap6: lista de módulos ficará defasada quando o match/cron mergear) — ver relatório.

**Dev — muito à frente do cronograma §7:** KAN-46, presença/chegada/fila (KAN-34/35),
tempo real (KAN-47), **gestão de partidas (KAN-37) completa e MERGEADA na main (PR #26)**,
push infra (KAN-43 back + front), lifecycle read-path e **cron (KAN-48)** implementados.
Cron: correções da review 001 + auditoria aplicadas em 13/07 (commit `dcd7735`),
branch pushada, PR pendente. CI nos dois repos; jest-expo no front.

**Dev — pendências que mudaram de cor (com decisões de 13/07):** SES (card 1) NÃO feito;
DECISÃO do Miguel: aguardar mais um pouco (registrado) — continua sendo o gate do beta
com usuário externo (plano B: verified identities no sandbox). Hardening (card 2)
previsto em card. Instrumentação do beta (card 6) não encontrada no código — decidir
antes do beta, senão o funil do Cap7.4 fica sem dado. Frontend do fluxo de fut previsto
em card (caminho crítico até 25/07). Contrato `futsSemanais` no front previsto em card.

**Riscos §9 recalibrados:** R1 (SES/sandbox) = VERMELHO enquanto a espera durar (decisão
consciente; plano B do sandbox segura o beta fechado); R3 (partida ao vivo estourar) =
VERDE no backend / VERMELHO no frontend (a tela de partida nem começou); R2 (beta
atrasar) = alto se SES ou frontend do fut escorregarem.

## 0. Adendo - decisões fechadas em 11/06 (respostas do Miguel)

| Tema | Decisão |
|---|---|
| Datas/freeze | Mantidos como propostos (freeze 03/08; ajustar quando sair a data oficial da entrega) |
| Bounding box | **NÃO implementar no MVP** (decisão consciente: custo desnecessário com catálogo pequeno; entra pós-MVP com a expansão). Registrada no `borafut_contexto_prd.md` §8.1/§4. O texto do RNF02 **fica como está** por decisão do autor. Sugestão opcional registrada: reformular para "consultas de quadras indexadas por coordenadas geográficas, dimensionadas ao catálogo do piloto" caso queira blindar contra pergunta técnica da banca |
| Regra 30 dias | Decisão intencional de produto; documentada no contexto §5.1 |
| Modo de edição | Repo de IA e repo do TCC liberados desde já; código (cards de dev) só após o Arthur concluir as reviews (~15/06) |
| Responsáveis | Marcações [M]/[A] são indicativas, sem dono fixo |
| Diagramas | Confirmado: IA atualiza specs e gera UC + C4 (contêineres e componentes) via PlantUML; Miguel desenha MER Chen + lógico no brModelo |
| Apêndice de links (P04) | Não criar (URLs nas referências bastam) |
| Conscientização (P18) | Escopo mínimo aprovado (tela estática com conteúdo curado do Cap1); card redigido em `cards-jira.md` |
| SonarCloud (E03) | Só se sobrar tempo na S8 |
| Observação/brainwriting | Material está no Miro; extração via integração Miro (com autorização do Miguel) ou export manual; brainwriting censurado (método + categorias + volume, sem ideias sensíveis) |
| TCLE | Miguel envia o texto do forms quando a tarefa for tocada (S2) |
| Protótipos | Exports caem em `latex/figuras/prototipos/` |
| Hardening/borda | Backend ficará atrás de proxy (Cloudflare free recomendado); incluído no card 2 |
| CI | Confirmado inexistente; card 3 criado |
| Cards | 7 cards redigidos em `analises-2026-06-11/cards-jira.md` (SES, hardening+borda, CI, jest-expo, conscientização, instrumentação, refactor-menores). Bounding box sem card (decisão acima) |
| Fluxo IA | D1-D8 aprovados; D1-D7 APLICADOS em 11/06 no BoraFut-ia (aguardando commit do Miguel); D8 é prática ao criar cards novos |
| Achados menores doc 02 | Todos aprovados; M1/M2/M4 viram o card 7 (próximo refactor) |
| Beta | Copa na Praça NÃO é mais parceira; base real: 4-5 grupos próprios |
| Instrumentação | Eventos no próprio Postgres (card 6); sem ferramenta externa |
| SUS | Conduzido pelos dois; horas contabilizadas no cronograma |

---

## 1. O que aproveitei e o que descartei do `plano-tcc-final.md`

**Aproveitado (e por quê):**
- O dossiê B01-B20 e a leitura dos pontos da banca: conferi item a item contra
  `pontos_a_corrigir_tcc1.md` e está fiel; reutilizo a numeração B* como referência
  cruzada.
- Os princípios invioláveis (nova verdade sem narrar mudança; zero rastro de IA; não
  referenciar a banca; app não reserva; palavras qualificadas; confidencialidade;
  tipografia) - mantidos integralmente como regras de execução (§8).
- O registro de execução (§11 do plano antigo): é o que define o baseline real do working
  tree, incluindo as exceções aprovadas (Flutter mantido como escolha avaliada; trocas de
  palavra que você reverteu e NÃO devem ser reaplicadas).
- A decisão brModelo para o MER e a tese de que motor do fut + tempo real merecem seções
  próprias no TCC2 (confirmada pelos sumários dos 3 TCCs de referência: nenhum tem nada
  parecido, é nosso diferencial).
- A invariante N2 (stack descrita em 3 capítulos: Cap3, Cap4 §arquitetura, Cap5 §C4 -
  qualquer mudança varre os três).

**Descartado ou substituído (e por quê):**
- **Prazo**: o plano antigo mirava TCC2 ~outubro. Realidade: monografia em agosto, defesa
  em setembro, lançamento 10/09. Todo o sequenciamento foi refeito (§7).
- **"Miguel desenha - NÃO IA"** para todos os diagramas: substituído. A crítica da Milene
  foi à imagem amadora gerada por chatbot, não a ferramentas. PlantUML/C4-PlantUML
  produzem notação UML/C4 padrão, idêntica à de qualquer engenheiro usando a ferramenta.
  Mantém-se manual apenas o MER Chen (brModelo é a ferramenta certa e não há gerador
  confiável de notação de Chen). Decisão validada com você em 11/06 (resposta b.8).
- **A marcação "explicitar o que não está implementado"** (regra 4 da hierarquia do
  enunciado): substituída pela sua decisão b.7 - o texto descreve o estado final sem
  pendências, protegido pela regra de freeze (§8.2).
- O plano antigo não enxergava as dependências código→texto (SES→beta→Cap7;
  bounding box→RNF02; KAN-46→vocabulário do Cap5). Este plano as torna explícitas.
- O plano antigo tratava B11 (backlog 5-7 níveis) só como reescrita conceitual; agora há
  insumo real: as fases/cards do Jira e as tasks executadas fornecem as camadas
  Tarefa/Subtarefa com conteúdo verdadeiro (§3, P12).

---

## 2. Linha do tempo mestre e capacidade

| Data | Evento (fonte: suas respostas 11/06) |
|---|---|
| 15/06 | Arthur conclui reviews; desenvolvimento retoma |
| julho | Entrevistas de usabilidade + SUS continuam (alvo: >10 respostas SUS até agosto) |
| ~25/07 a 08/08 | **Beta fechado**: fut real em ambiente de teste, coleta de dados de uso |
| 03/08 | **Freeze de escopo do texto** (regra §8.2) - data interna proposta |
| agosto | Entrega da monografia (dia exato A CONFIRMAR - ajustar §7 quando souber) |
| ~29/08 | Submissão às lojas (antecedência para review Apple/Google) |
| 10/09 | Lançamento do app |
| setembro | Defesa (data A CONFIRMAR) |

Capacidade: você + Arthur, ~40 h/semana cada (~80 h/semana totais). Alocação sugerida até
fim de julho: ~60% desenvolvimento / 40% monografia; em agosto inverte (~30/70). A divisão
de quem faz o quê está marcada nas tabelas como [M] Miguel, [A] Arthur, [M+A], [IA]
(tarefa executável por agente com revisão humana).

---

## 3. Frente A - Correções da banca (20 pontos → ação concreta)

Numeração P* segue `correções/pontos_a_corrigir_tcc1.md`; B* cruza com o plano antigo.
Status: ✅ feito no baseline · 🔄 parcial · ⬜ a fazer.

| P | Banca pediu | Status | Ação concreta (arquivo → o que fazer) | Quem/Quando |
|---|---|---|---|---|
| P01 (B01) | Monetização clara: núcleo nunca pago | ✅ | `introducao.tex:33` já diz "fora de escopo... perspectiva futura". ADICIONAR frase explícita: o núcleo (organizar, descobrir, informar) é gratuito e assim permanecerá; eventual monetização futura recairia sobre extras que não tocam o fluxo principal. Varrer `metodologia.tex:147` (PM Canvas "potencial de monetização no futuro" - ok, manter) e Cap5 por menções a pago/premium | [M] S1 |
| P02 (B02) | Aspas consistentes; índice ≤4 níveis | ✅ regra | Nada a escrever agora; entra no passe final (P13): conferir aspas LaTeX e profundidade de seções nos caps novos | [M+IA] S8 |
| P03 (B03) | "Evitar conflitos" + mecanismo | ✅ | Cap1 reescrito (disputa = falta de informação) ✅. FALTA Cap5: em `projetoborafut.tex` §Descrição, 1 parágrafo ligando pins/agenda da quadra ao mecanismo "ver ocupação antes de se deslocar e escolher outra quadra/horário" | [IA→M] S1 |
| P04 (B04) | Matriz comparativa + links dos apps | ✅ | `tab:comparativo_mercado` + 6 refs `@online` com URL ✅ compila. Pendente seu crivo: células ◐ conservadoras (PELADEIROS/FINTTA). OPCIONAL: apêndice "Links das soluções analisadas" (modelo: vandor Apêndice A) - recomendo NÃO criar (as URLs já estão nas referências) | [M] S1 (revisão 15 min) |
| P05 (B05) | Documentação da observação | ✅ | DECISÃO 11/06: vai NO texto, não em documento separado (observação não expõe nada sensível; só o brainwriting é confidencial). `metodologia.tex` §Observação reescrita documentando o episódio fundador (dez/2023, duas quadras vizinhas a ~500m, oito jogadores em cada, nenhuma completa o jogo por falta de visibilidade entre grupos), enquadrado como observação participante. Pendência opcional: citação metodológica para "observação participante" (ex. Gil) | feito S1 |
| P06 (B06) | Brainwriting documentado, sem expor | ⬜ | Documento à parte (confidencial, NÃO anexar): método 6-3-5, seis categorias, nº de cartões e resultados em alto nível, sem as ideias sensíveis; citar no texto e entregar à banca em separado. Material no Miro | [M] S1-S2 |
| P07a (B07a) | RNF ancorados na proposta de valor + rastro | ✅ | RNF01-07 + `tab:rastro-valor` ✅. RNF02: RESOLVIDO em 11/06 (adendo §0) - bounding box fica pós-MVP por decisão consciente; texto mantido (sugestão opcional de reformulação registrada no adendo) | - |
| P07b (B07b) | Casos de uso: diagrama UML correto + especificações com episódios | ✅ (núcleo) | FEITO em 11/06: (a) 3 diagramas UML (PlantUML) gerados e inseridos no Cap5 `projetoborafut.tex:135`, substituindo o `uc-borafut.jpg` de IA, com fronteira, generalização Criador→Organizador→Registrado→Não Registrado, include/extend e cardinalidade de papéis no texto; (b) novo Apêndice "Especificação dos Casos de Uso" (`apendices.tex`, `apendice:casos-uso`) com 7 UCs de maior densidade (UC02 autenticar, UC15 presença, UC19 criar fut, UC23 chegada, UC27 cadastro manual, UC29 iniciar, UC34 encerrar partida), cada um com pré/pós-condições, fluxo principal, alternativos e exceções. PENDENTE: compilar para validar refs/figuras; opcional, ampliar para mais UCs; decidir se adiciona multiplicidades nas linhas do diagrama (hoje a cardinalidade está no texto). ATUALIZAÇÃO 16/06: especificação textual ampliada de 7 para 11 UCs (UC11 avaliar quadra, UC18 compartilhar fut, UC32 registrar eventos da partida, UC41 ativar gerenciamento), dentro da meta 8-12 do §6.5 | feito S1 |
| P08 (B08) | Cross-ref Figura 9 | ✅ | Corrigido no baseline (`sec:prototipacao` com label). Conferir no PDF do freeze | [M] S8 |
| P09 (B09) | Protótipos legíveis no último apêndice | ⬜ | Re-exportar telas do Figma (você confirmou ser simples); criar apêndice FINAL "Telas do protótipo" com grades 2-3 por linha; em `projetoborafut.tex` §Prototipação manter só as 3 telas representativas e referenciar o apêndice | [M exporta; IA monta] S3 |
| P10 (B10) | (Elogio) justificativa do XP | ✅ | Não mexer em `metodologia.tex` §Processo de desenvolvimento | - |
| P11 | (Elogios gerais) preservar pontos fortes | ✅ regra | Checklist no freeze: nome BORAFUT, justificativa XP, elicitação 60+, dados Cap1 (OMS, 10k→2k passos), justificativa banco relacional, heurísticas Nielsen, C4, identidade visual intactos | [M] S8 |
| P12 (B11/12) | Backlog com 5-7 níveis de granularidade | ✅ | FEITO em 11/06: `metodologia.tex` §Priorização ganhou a subseção "Granularidade e hierarquia do backlog" com os 6 níveis (Tema → Épico → Feature → História → Tarefa → Subtarefa) definidos, citações (Cohn `cohn2004`, SAFe `safe2023`, Sommerville/Wiegers para rastreabilidade) e a Tabela `tab:decomposicao` com um exemplo completo do tema à subtarefa. Temas = pilares da proposta de valor (amarra com P07a; ref `subsec:proposta-valor`). No apêndice de US, cada um dos 6 épicos foi anotado com seu Tema predominante + nota sobre temas transversais + Tabela `tab:backlog-temas` (sidewaystable) com a matriz 6 épicos × 6 níveis (Opção B, decidida em 11/06). DECISÃO: profundidade demonstrada na matriz do apêndice + exemplo da metodologia, não na decomposição exaustiva das ~49 US (evitar bloat). PENDENTE: compilar | feito S2 (adiantado) |
| P13 (B13) | Polimento ABNT (itálico, iniciais, vírgulas) | ⬜ | Passe final de leitura no texto completo, com checklist: itálico em estrangeirismos, iniciais de títulos, vírgulas, siglas definidas no 1º uso | [M+A] S8 |
| P14 (B14) | TCLE mencionado e anexado | ⬜ | FONTE definida 16/06: o TCLE é o termo COMPLETO dos testes de usabilidade (Miguel envia depois), NÃO o consentimento simplista do questionário de 2025; novo apêndice "Termo de Consentimento Livre e Esclarecido"; citar na metodologia (questionário E entrevistas de usabilidade do Cap7) - modelo: miaAjuda Apêndice C | [M] S2 |
| P15 (B15) | Versões das tecnologias | ✅ | `tab:tecnologias` ✅. Manter viva: ao instalar Socket.IO/SES/S3 de verdade, conferir versões antes do freeze | [M] S8 |
| P16 (B16) | Toda tabela citada no texto | ✅ | Corrigido no baseline; revalidar no freeze incluindo tabelas novas dos caps 6-7 | [M] S8 |
| P17 (B17) | App NÃO reserva quadra; desafio de catalogar | ✅ | Cap1 ✅; parágrafo de catalogação adicionado no Cap5 (`projetoborafut.tex:27`). Varredura `reserv/agend/garant` feita em 11/06: LIMPA. Ocorrências restantes são o app dizendo que NÃO reserva, contexto do mundo real (Plano Piloto) e descrição de concorrentes (Appito/Agendei). Único item a revisitar na S2: US `E3-F2-US1` ("ocupada ou reservada") junto do rework do backlog | feito S1 |
| P18 (B18) | Espaço de conscientização | ⬜ | Produto: criar card (você confirmou que ainda não existe) e implementar aba enxuta (conteúdo estático curado: nutrição, vida ativa, riscos do excesso de tela, ancorado nas refs do Cap1). Texto: subseção curta no Cap5 (escopo) + menção no Cap6. GATE no freeze (03/08): se não implementada, move para Trabalhos Futuros no Cap8 e sai do Cap5 | [A] S6-S7; gate S8 |
| P19 (B19) | Palavras fortes qualificadas | ✅ | "equitativo"→"inclusivo" ✅ no Cap2 (`referencialteorico.tex:21`). Varredura `garant/assegur/equitativ/igualitár` feita em 11/06: LIMPA. Nenhuma promessa do app ao usuário; as ocorrências de "garantir/assegurar" são citações de fontes (ITU), descrição de metas da ONU (ODS 11.7), a dor descrita ("difícil garantir que o jogo aconteça") ou garantias técnicas/metodológicas legítimas (integridade do banco, qualidade de código, fallback WebSocket) | feito S1 |
| P20 (B20) | MER conceitual (Chen) + separar do lógico | 🔄 PARCIAL 16/06 | DICIONÁRIO FEITO 16/06 (Apêndice `apendice:dicionario`, ~20 entidades geradas do schema.prisma, citado no Cap5 §Modelo de dados em `projetoborafut.tex`). Resta: MER Chen no brModelo + modelo lógico atualizado + rework do §Modelo de dados. Plano dedicado em §6: MER Chen no brModelo (você), modelo lógico ATUALIZADO para o schema pós-KAN-46, dicionário de dados em apêndice (modelo: vandor Apêndice F; gerável do schema.prisma). Reescrever `projetoborafut.tex` §Modelo de dados: apresentar conceitual primeiro, depois lógico, nomeando-os corretamente | [M desenha; IA specs+dicionário] S2-S3 |

---

## 4. Frente B - Divergências monografia ↔ estado atual (o que reescrever, onde)

Itens D* do plano antigo conferidos contra o working tree + novos achados desta análise.

| ID | Capítulo/Seção (arquivo:linha) | Estado atual do texto | Reescrever para | Quando |
|---|---|---|---|---|
| D01-D04 | Cap3 inteiro | ✅ stack real (NestJS/Fastify/Prisma/SES/S3/Expo Push/Socket.IO + versões) | Nada; só manter versões vivas (P15) | - |
| D05 | Cap4 §Arquitetura geral (`metodologia.tex:458-498`) | ✅ reconciliado (NestJS/Fastify, SES, REST+WS) | Conferência no freeze (invariante N2: Cap3+Cap4+Cap5 dizem o mesmo) | S8 |
| D06 / E05 | Cap5 §C4 (`projetoborafut.tex`) | ✅ FEITO 15/06 | Título → "contexto, contêineres e componentes"; intro "dois" → "três níveis"; figura minimalista substituída pela enriquecida (`diagramas-src/render/c4-conteineres.png`); nova subseção "Diagrama de componentes (C3)" com `c4-componentes.png` (sidewaysfigure); texto de comunicação rotulada (REST/WebSocket/Prisma); Expo Push adicionado aos externos. CONSISTÊNCIA: Cap4 §Integrações reconciliado (era 2 integrações, agora 4 = SES, Maps, Expo Push, S3/CloudFront — invariante N2). PENDENTE: compilar (conferir a sidewaysfigure de componentes) | feito |
| D07 | Cap5 §casos de uso e §fluxos | ✅ FEITO | Vocabulário Criador/Organizador aplicado no §casos de uso (P07b) e no §Fluxos (D09). Resta varrer os demais capítulos por menções antigas (a varredura de papéis de 15/06 já cobriu Cap5; apêndice US ajustado) | feito |
| D09 | Cap5 §Fluxos principais (`projetoborafut.tex:177-218`) | ✅ FEITO 16/06 | Seção inteira reescrita para o modelo-alvo: explorar mapa/quadras (com visitante), perfil da quadra (favoritar/avaliar), criar fut (modo + parâmetros + bloqueio de sobreposição), confirmar presença (sobreposição <2h), compartilhar, confirmar chegada (GPS não-bloqueante + terceiro + revogação), iniciar e formar times (horário + mínimo), acompanhar partida ao vivo (gol/desfazer/último lance/rotação/pênaltis/override), encerrar + pós-fut (card + estatísticas), fut não-gerenciado (transição + ativação), cadastrar presencial. Vocabulário Criador/Organizador. CONSISTÊNCIA: varredura do Cap5 corrigiu "criação de partidas"/"modalidade"/"vagas" → futs/configuração no §Descrição (linha 18), RF03/RF04 (precisão) e §prototipação. PENDENTE: compilar. NOTA: rótulo do botão "Criar fut" (monografia) vs "Marcar Fut" (card KAN-33) — RESOLVIDO 16/06: verificado no frontend (create-fut.tsx e court/[id].tsx) que o app usa "Criar Fut", idêntico ao doc; a menção a "Marcar Fut" (KAN-33) está superada, sem divergência doc+app | feito |
| D10 | Cap5 §Requisitos funcionais | ✅ FEITO 16/06 | Ampliado de 5 para 13 RFs (RF06 favoritos, RF07 avaliação, RF08 link, RF09 chegada/fila, RF10 partida ao vivo, RF11 cadastro presencial, RF12 push, RF13 pós-fut); `tab:rastro-valor` atualizada mapeando os novos RFs aos pilares. PENDENTE: compilar | feito |
| D10b | Cap5 §Descrição | ✅ FEITO 16/06 | Bullets do MVP atualizados: + avaliação/favoritar, modos gerenciado/não-gerenciado na criação, gerenciamento da partida ao vivo, resumo pós-fut. Rótulo do botão alinhado para "Marcar fut" (UI real) em todo o Cap5 | feito |
| D11 | Cap1 §Organização (`introducao.tex:61-73`) | 🔄 PARCIAL 16/06 | Feito: "cinco" → "seis" e parágrafo do Cap6 (introducao.tex:63 e 72), acompanhando a criação do Cap6. Resta: virar "oito" e adicionar os parágrafos dos Caps 7-8 quando existirem | S7 |
| N3/E06 | Caps 3-5 (global) | Tempo verbal misto ("será desenvolvido", "planejada") | Varredura `grep -i "será\|serão\|planejad\|prevista"` → presente/pretérito (o sistema FOI implementado). Exceção: seções explicitamente de futuro | S8 |
| N4/E04 | `consideracoes.tex` (comentado no tcc.tex) | Boilerplate | Vira o Cap8 (ver §5); descomentar include | S7 |
| NV1 | Cap5 §casos de uso texto (`projetoborafut.tex:106`) | ✅ FEITO 16/06 | US de push criadas no apêndice: E4-F4 "Lembretes e avisos" (lembrete pré-fut e aviso no horário), apendices.tex:145 | S2 |
| NV2 | Apêndice US (`apendices.tex`) | ✅ FEITO 16/06 | US adicionadas: último lance (E5-F5-US3), pênaltis (E5-F5-US4), modo não gerenciado (E5-F7-US1/US2/US3), card pós-fut (E5-F6-US3/US4), push (E4-F4), promover organizador (E5-F1-US4), chegada de terceiro e revogar chegada (E5-F2-US4/US5). Reconciliação "jogo" → "fut" em E4/E5 e na metodologia (resolve pendência §8.4 item 5) | S2 |
| NV3 | RNF02 (`projetoborafut.tex:59`) | "limitando os resultados do mapa à área visível" | RESOLVIDO (adendo §0): texto mantido por decisão do autor; decisão técnica documentada no contexto | - |
| NV4 | Resumo/abstract (`editaveis/resumo.tex`) | Provavelmente no tom proposta TCC1 (não auditado nesta análise) | Reescrever no fim: trabalho desenvolvido + validado + resultados | S8 |

---

## 5. Frente C - Estrutura do TCC2 (proposta justificada)

### 5.1 Por que esta estrutura

Dos 3 TCCs de referência (sumários conferidos):
- **vandor (aluguéis)**: Cap "Desenvolvimento" organizado por sprints com "modificações da
  proposta", validação com usuários (SUS) e análise de qualidade no mesmo cap; apêndices
  fortes (specs de casos de uso, dicionário de dados, links de benchmarking). É o modelo
  estrutural mais próximo (TCC de projeto, dupla, app).
- **miaAjuda**: capítulo de estudo + capítulo de "Análise de Resultados" por ciclos de
  teste + conclusão com "status dos objetivos" - bom modelo para o nosso Cap7 e Cap8;
  TCLE em apêndice.
- **Mina**: capítulo monográfico do produto (requisitos→arquitetura→app) e suporte
  tecnológico com versões - já refletido no nosso Cap3/Cap5.

Nosso contexto pede dois desvios deliberados dos modelos (defensáveis perante a banca):
o **motor do fut** (presença→chegada→fila→times→rotação→pênaltis→pós-fut) e a **camada de
tempo real** (WebSocket + fallback + cron) são densos e originais o suficiente para serem
seções de destaque do Cap6 - nenhum dos 3 modelos tem equivalente, e é onde o trabalho
mostra profundidade de engenharia (o que a banca elogiou querer ver: requisitos que fazem
o app "dar certo", rastro, estados).

### 5.2 Estrutura alvo (8 capítulos)

| Cap | Título | Conteúdo | Base |
|---|---|---|---|
| 1 | Introdução | Atual + ajustes P01/P03/P17 + organização 8 caps (D11) | existente |
| 2 | Referencial Teórico | Atual (sem mudanças além de P19 já aplicado) | existente |
| 3 | Suporte Tecnológico | Atual (✅ reescrito) | existente |
| 4 | Metodologia | Atual + backlog 6 níveis (P12) + observação/brainwriting referenciando docs da banca (P05/P06) + TCLE citado (P14) | existente |
| 5 | Projeto do BORAFUT | Atual + RFs ampliados (D10), fluxos reescritos (D09), papéis novos (D07), diagrama de casos de uso novo (P07b), C4 enriquecido + componentes (D06), MER conceitual + lógico atualizado (P20), conscientização (P18, gate) | existente, reescrita pesada |
| 6 | **Desenvolvimento** (novo) | 6.1 Processo e iterações (fases reais de construção, sem citar ferramentas de gestão); 6.2 Arquitetura implementada (visões C4, organização por módulos, convenções); 6.3 Fundação: dados, autenticação passwordless, segurança de sessão; 6.4 Descoberta: mapa, catálogo de quadras, avaliações; 6.5 **Motor do fut**: máquinas de estados (fut, participação, partida), fila posicional, formação e rotação de times, pênaltis, overrides; 6.6 **Tempo real**: WebSocket + fallback REST, cache/ETag, cron de backstop; 6.7 Qualidade: testes automatizados, validação, padrões; 6.8 Decisões e trade-offs (3-5 ADRs resumidas: ex. fila nunca resequenciada, snapshot de regras por partida, WebSocket no MVP) | vandor cap4 + Mina cap5; seções 6.5-6.6 originais |
| 7 | **Resultados e Validação** (novo) | 7.1 Método de avaliação (roteiro de entrevistas, perfil dos participantes, TCLE, instrumentos); 7.2 Testes de usabilidade sobre o protótipo de alta fidelidade (achados → iterações de design; ser explícito que o objeto foi o protótipo); 7.3 SUS (n>10, score, interpretação na escala); 7.4 Teste piloto em ambiente real (beta fechado: fut real, métricas - futs criados/iniciados, presença→chegada, partidas registradas, compartilhamentos); 7.5 Qualidade de software (resultados de lint/testes; SonarCloud se houver tempo - E03); 7.6 Discussão: objetivos específicos × evidências, limitações | miaAjuda caps 5-6 + vandor 4.4-4.5 |
| 8 | **Conclusão** (novo, substitui consideracoes.tex) | Síntese; contribuições (técnica: motor do fut/tempo real documentados; social: catálogo de quadras públicas + organização comunitária); limitações; trabalhos futuros (genéricos o bastante para não expor estratégia: evolução do score de presença, recorrência de futs, expansão geográfica, campeonatos) | todos os modelos |

### 5.3 Apêndices alvo (ordem final)

A) MER lógico atualizado · B) PM Canvas · C) User Stories (Temas→...→amostra de
tarefas, P12) · D) Questionário · E) TCLE (novo, P14) · F) Especificação dos casos de uso
(novo, P07b) · G) Dicionário de dados (novo, P20) · H) Roteiro de entrevista + SUS (novo)
· I) **Telas do protótipo (último**, P09).
Observação e brainwriting: documentos À PARTE para a banca (P05/P06), citados e não
anexados (princípio de confidencialidade).

---

## 6. Plano específico dos diagramas (P07b, P20, D06, E05)

1. **Atualizar `specs-diagramas-banca.md`** (raiz do repo TCC) ANTES de desenhar:
   - papéis: Criador (herda de Usuário Registrado) e Organizador (idem); ator base Usuário
     Não Registrado;
   - casos de uso do fluxo completo: incluir não-gerenciado (confirmar presença em fut
     simples), ativação de gerenciamento, card pós-fut, avaliação de quadra com
     elegibilidade, cadastro manual durante o fut; revisar includes/extends
     correspondentes (ex.: "Disputar pênaltis" extends "Encerrar partida" permanece);
   - MER: incorporar `log_chegada` (entidade fraca de FUT ligada a JOGADOR com papel
     ator), timestamps de participação como atributos do relacionamento PARTICIPA,
     snapshot de regras como atributos de PARTIDA;
   - C4 contêineres: já correto; adicionar spec do **C4 componentes (C3)** do backend:
     módulos auth, courts (quadras+avaliações), futs (presença/fila), partidas (motor),
     realtime (gateway WS), notificações (push/e-mail), cron, prisma/common. [IA, S2]
2. **Gerar e versionar**: casos de uso (PlantUML), C4 contêineres e componentes
   (C4-PlantUML) → exportar PNG/SVG de alta resolução para `latex/figuras/`. Revisão sua
   antes de inserir. [IA gera; M revisa; S2-S3]
3. **MER conceitual (Chen)**: você desenha no brModelo seguindo a spec; exportar imagem.
   **Modelo lógico**: atualizar no brModelo (ou ferramenta equivalente) para o schema
   pós-KAN-46 (com log_chegada, timestamps, snapshots, `criador_id`). [M, S2-S3]
4. **Dicionário de dados**: gerar tabela (entidade, atributo, tipo, descrição, restrições)
   a partir do `schema.prisma` pós-refactor → apêndice G. [IA, S3]
5. **Especificações textuais de casos de uso** (episódios): 8-12 UCs prioritários (criar
   fut, confirmar presença, confirmar chegada, iniciar fut, registrar gol, encerrar
   partida/pênaltis, compartilhar, avaliar quadra, cadastro manual, ativar gerenciamento)
   com pré-condições, fluxo principal numerado, alternativos e exceções → apêndice F.
   Insumo direto: fut_in_app §3-§6 e §9 (cenários A-M). [IA→M revisa; S3]

---

## 7. Sequenciamento (semana a semana)

Dev e texto andam juntos; itens dev vêm do Jira + achados do doc 02; itens de fluxo IA do
doc 03. Datas assumem entrega da monografia ~meados de agosto (ajustar quando a data sair).

| Semana | Desenvolvimento (app) | Monografia | Fluxo IA / infra |
|---|---|---|---|
| S0 11-14/06 | Colar no Jira os 7 cards de `cards-jira.md` (SES, hardening+borda, CI, jest-expo, conscientização, instrumentação, refactor-menores) | Validar este plano ✅; atualizar `specs-diagramas-banca.md` (§6.1) | D1-D7 APLICADOS em 11/06 (commit pendente do Miguel no BoraFut-ia) |
| S1 15-21/06 | [M+A] KAN-46 (refactor schema/papéis - ANTES de tudo, coordenar merge com KAN-33) + card SES (fase sandbox) + card 7 (refactor-menores) | [M] P01, P03, P17, P19 (varreduras e parágrafos curtos); P04 revisão da matriz; iniciar P05/P06 (extração do Miro) | CI (card 3) pode rodar em paralelo |
| S2 22-28/06 | [A] KAN-34/35 (presença+chegada+fila) | [IA→M] P12 backlog 6 níveis + NV2 US novas; P14 TCLE; gerar diagramas UC/C4 (§6.2); [M] MER no brModelo | D4 (ADR template) antes da techspec do realtime |
| S3 29/06-05/07 | [M] KAN-47 (WebSocket) + início KAN-37 | [IA→M] Cap5: D07/D09/D10/D10b (reescrita pesada) + P07b specs textuais + P20 texto do modelo de dados + dicionário; P09 apêndice protótipos | - |
| S4 06-12/07 | [M+A] KAN-37/38 (partida ao vivo back+front) | [M] Cap6 seções 6.1-6.4 (já escrevíveis: fundação/descoberta prontas) | D5 (corrigir-bug) antes do beta |
| S5 13-19/07 | [M] KAN-48 (cron) + [A] KAN-43 (push) | [M] Cap6 6.5-6.6 (motor do fut + tempo real, conforme implementa); entrevistas SUS em paralelo | - |
| S6 20-26/07 | [M+A] KAN-49/50 (não-gerenciado + pós-fut); KAN-40/41/42 (perfil, visitante) | [M] Cap6 6.7-6.8; preparar instrumentação do beta (métricas do funil - doc 01, A3) | - |
| S7 27/07-02/08 | **BETA FECHADO** (fut real); correções; [A] P18 aba conscientização; KAN-45 (S3/CloudFront) | [M] Cap7 (7.1-7.3 com dados de usabilidade/SUS; 7.4 estrutura aguardando dados do beta); Cap8 rascunho; D11 organização | - |
| S8 03-09/08 | Estabilização; hardening (I4) | **FREEZE 03/08** (gate P18; gate RNF02). Cap7.4 com dados do beta; passe global: N3 tempo verbal, P02/P13/P15/P16, P11 checklist de elogios, NV4 resumo; compilar e revisar PDF completo [M+A] | - |
| S9 10-16/08 | Submissão das builds de teste às lojas (track interno) | Buffer + **entrega** (data a confirmar) | - |
| pós-entrega | ~29/08 submissão final lojas; 10/09 lançamento | Slides + vídeo de demonstração (banca pediu mídia agregada - levar vídeo do app real, não só protótipo) | - |

**Dependências críticas (não violar):**
1. KAN-46 antes de qualquer card da Fase 3 e antes da reescrita do Cap5 (D07) virar final.
2. SES real antes do beta (S7): sem e-mail, participantes externos não logam (doc 02, C1).
   Iniciar saída do sandbox AWS na S1 (prazo externo).
3. Beta antes do freeze do Cap7.4: se o beta escorregar além de 03/08, aplicar fallback
   (§9, R2).
4. Decisão bounding box (S1) antes do freeze do RNF02.

---

## 8. Regras de execução

### 8.1 Princípios herdados (invioláveis, do plano antigo - mantidos)
1. Escrever a nova verdade, nunca narrar a mudança.
2. Zero menção a uso de IA.
3. Não referenciar a apresentação/feedback da banca no texto.
4. O app NÃO reserva quadra; informa ocupação em melhor esforço; banir "reserva/garantia".
5. Qualificar palavras fortes.
6. Confidencial (brainwriting/observação com planos): citar, não anexar; entregar à parte.
7. Commits sem trailer de IA.
8. Tipografia: aspas LaTeX consistentes; índice ≤4 níveis; sem subseção de 1 parágrafo.
9. Exceção aprovada: Flutter permanece no Cap3 como escolha inicial avaliada (não remover).
10. Não reaplicar trocas de palavra que você reverteu (comprovada/alarmantes/através de -
    registro do plano antigo, Cap1).

### 8.2 Regra de freeze (nova, decorre da sua decisão b.7)
O texto descreve o sistema como implementado, sem pendências. Em contrapartida, em
**03/08**: tudo que não estiver implementado e testável **sai do texto** (vira Trabalho
Futuro no Cap8 ou é removido). Candidatos a gate: aba de conscientização (P18), card
pós-fut completo (KAN-50), S3/CloudFront em produção (KAN-45), push (KAN-43). Nada de
descrever feature que não exista no app da defesa - a banca pode pedir demonstração.

### 8.3 Invariante de consistência (N2)
Stack e arquitetura são descritas em 3 lugares (Cap3, Cap4 §arquitetura geral, Cap5 §C4).
Toda mudança em um exige varrer os outros dois. Checagem obrigatória no freeze.

### 8.4 Disciplina de consistência (OBRIGATÓRIA a cada edição)

O texto é grande e editado em partes; conceitos recorrem em vários arquivos. **Após cada
alteração que toque um conceito recorrente, fazer `grep` do termo em todos os `.tex` de
`latex/editaveis/` (incluindo `resumo.tex` e `abstract.tex`) e reconciliar.** Conceitos que
DEVEM permanecer idênticos em todo o documento (checklist de invariantes):

1. **Granularidade do backlog**: hierarquia de 6 níveis (Tema → Épico → Feature → História
   de usuário → Tarefa → Subtarefa). NUNCA descrever como "épicos, features e US" (3 níveis
   antigos). Grep: `épicos|features|user stories|histórias|níveis`.
2. **Papéis**: **Criador** (criou o fut, autoridade máxima; edita/cancela o fut, promove
   organizadores) e **Organizador** (promovido pelo criador, permissões operacionais).
   NUNCA "organizador" no sentido antigo de "quem criou". Grep: `organizador|criador`.
3. **Pilares da proposta de valor** (nomes exatos): centralizar a organização; reduzir a
   dispersão de informação; melhorar a comunicação; aumentar a participação; simplificar a
   dinâmica do jogo; reduzir conflitos pelo uso das quadras; foco nas quadras públicas do DF;
   disponibilidade multiplataforma. (Temas do backlog = estes pilares.)
4. **App não reserva quadra**: linguagem de melhor esforço, sem "reserva/garantia". Grep:
   `reserv|agend|garant`.
5. **Vocabulário**: "fut" (evento agendado) entre aspas para rótulos de interface; "partida"
   (sessão dentro do fut) preferencial; evitar "jogo" ambíguo. PENDÊNCIA: as US do apêndice
   ainda usam "jogo" solto (reconciliar no pass de Cap5/US, S3). Grep: `jogo|fut|partida`.
6. **Stack** (invariante N2, §8.3). 7. **Sem travessão** (—). 8. **RFs/RNFs e tabela de
   rastreabilidade**: quando os RFs expandirem (D10/S3), atualizar `tab:rastro-valor`.

**Inconsistência conhecida e pendente (NÃO é descuido):** o §Fluxos principais do Cap5
(`projetoborafut.tex`, "Fazer login", "Confirmar chegada", "Iniciar a partida", "Gerenciar
partida" etc.) ainda descreve os fluxos antigos (dez/2025: chegada simples, "jogadores em
espera controlam", sem não-gerenciado, sem pós-fut) e usa "criador da partida". Isso
contradiz os casos de uso novos já inseridos. Reconciliação completa = D09 (S3); até lá,
está marcado como zona a reescrever.

---

## 9. Riscos do plano e mitigações

| R | Risco | Prob. | Mitigação |
|---|---|---|---|
| R1 | SES preso no sandbox AWS além de julho | média | Iniciar processo na S1; plano B: beta com participantes pré-cadastrados manualmente (verified identities individuais no sandbox funcionam para lista fixa de e-mails) |
| R2 | Beta atrasa além de 03/08 | média | Cap7 sustenta-se em usabilidade+SUS (já garantidos, >10); 7.4 vira "teste piloto" com relato qualitativo do fut de teste interno; remover métricas prometidas |
| R3 | Fase 4 (partida ao vivo) estourar - é o maior bloco técnico | média-alta | É o coração do TCC (Cap6.5): proteger escopo cortando Fase 5 (perfil/menu/visitante podem ser simplificados); KAN-42 modo visitante já funciona parcialmente |
| R4 | Reescrita do Cap5 conflitar com mudanças de produto durante a implementação | baixa | fut_in_app é estável (38 decisões fechadas); desvios passam pelo gate D2 do doc 03 (sync de specs) e refletem no texto na S8 |
| R5 | Review das lojas (Apple) recusar/atrasar o lançamento de 10/09 | média | Submeter build de teste na S9 (track interno/TestFlight) para passar pelo review cedo; conta e certificados prontos antes |
| R6 | Datas oficiais (entrega/defesa) divergirem do assumido | - | Ao confirmar as datas, deslocar S8-S9 mantendo o freeze 10 dias antes da entrega |

---

## 10. Divergências encontradas (registro, regra 6 do enunciado)

1. **Enunciado vs working tree**: "monografia parada desde dezembro" - na prática o
   working tree contém as correções de junho (commits "Incrementa Analise de mercado" /
   "suporte tecnologico" + 3 .tex modificados). Tratei como baseline desejado (confirmado
   por você na Fase 1).
2. **`borafut_contexto_prd.md` vs `fut_in_app_context.md`**: 4 contradições internas
   (WebSocket, rotação, overrides, rotas co-organizer) - detalhadas e com patch pronto no
   doc 03 (D1), conforme regra 3 da hierarquia ("sinalize").
3. **Monografia RNF02 vs código** (bounding box): decisão pendente no gate S1 (P07a/NV3).
4. **Jira vs realidade**: KAN-27/KAN-28 constam concluídos mas o bounding box do escopo
   não existe (doc 02, I1); cards de SES e conscientização não existem e são necessários.
5. **`BoraFut-Frontend/.ia/notas-tcc2.md`**: embrião desatualizado deste plano (polling no
   MVP, coOrganizador). Tratado como registro histórico; sugiro arquivá-lo com nota de
   supersedência apontando para `analises-2026-06-11/` quando você sair do modo
   somente-leitura.

## 11. Oportunidades adicionais (registro, sem execução)

1. **Mídia para a banca**: a banca pediu explicitamente para "agregar mídia". Além do
   vídeo, preparar um QR code nos slides apontando para o vídeo de demonstração do app
   real (e, se o lançamento ocorrer antes da defesa, para a página da loja).
2. **Cenários do fut_in_app §9 como testes**: transformar os cenários A-M em suíte de
   integração nomeada (doc 03, oportunidade 3) renderia material direto para o Cap6.7
   (qualidade) e o Cap7.5.
3. **SonarCloud (E03)**: se sobrar tempo na S8, uma análise estática com print do
   dashboard enriquece o Cap7.5 a baixo custo (os 3 TCCs de referência não têm; seria
   diferencial).
4. **Artigo curto pós-defesa**: o motor do fut (fila posicional + rotação unificada) tem
   originalidade suficiente para um artigo de relato de experiência (SBES/SBSI trilha
   prática) - reaproveita o Cap6.5.

## 12. Premissas e incertezas

### Verificado (com fonte)
- Os 20 pontos da banca: `correções/pontos_a_corrigir_tcc1.md` (lido integral).
- Baseline do texto: leitura integral dos 7 .tex + `git status`/`git log` do repo TCC;
  status ✅/🔄 conferidos no working tree (ex.: `tab:rastro-valor` existe em
  `projetoborafut.tex:77-100`; tabela de versões em `suportetecnologico.tex:121-148`).
- Comportamento-alvo do produto: `fut_in_app_context.md` (integral) e
  `borafut_contexto_prd.md` (integral).
- Estado do código e gaps que afetam o cronograma: doc 02 desta série (leitura direta dos
  services/controllers/DTOs citados lá).
- Estruturas dos TCCs de referência: sumários de miaAjuda.pdf, milene.pdf (Mina) e
  vandor.pdf (lidos nos PDFs).
- Datas de beta/lançamento/defesa e decisões b.1-b.14: suas respostas de 11/06/2026.

### Inferido / a confirmar
- **Datas exatas** da entrega da monografia e da defesa (assumi meados de agosto e
  setembro; o freeze de 03/08 deriva dessa hipótese).
- Distribuição de trabalho [M]/[A] nas tabelas: sugestão minha por afinidade com o que
  cada um vem fazendo (Arthur mais frontend); rebalancear como preferirem.
- Esforço das reescritas do Cap5/Cap6 (estimei pela densidade do fut_in_app, não por
  medição); o cronograma tem buffer S9.
- O conteúdo de `resumo.tex` (NV4): não li o arquivo; assumi que está no tom de proposta.
- Capacidade de o beta gerar métricas quantitativas utilizáveis no Cap7.4 (depende da
  instrumentação ser feita na S6 - listada como tarefa).
