# 03 - Auditoria do Fluxo de Desenvolvimento Assistido por IA

> Auditoria do repositório `BoraFut-ia` (README, 6 commands, 5 templates, contexto vivo) e
> do seu uso real nos repos (pastas `.ia/tasks/` com 6 features no backend e 3 no frontend).
> Contém PROPOSTAS com diffs prontos para aplicar **depois da sua revisão**: nada foi
> alterado em nenhum repositório (modo somente leitura respeitado).

## Sumário executivo

1. **O fluxo é maduro e está acima do que vejo em times profissionais pequenos**: gates
   humanos em todos os pontos certos (perguntas antes do PRD, aprovação high-level antes
   das tasks, commit exclusivamente humano), tasks auto-contidas, e um comando de review
   com 3 lentes (Micro/Macro/Spec) com filtro anti-superficialidade. A prova de que
   funciona está no código: as convenções dos commands (SQL raw, `AS "camelCase"`,
   comentários de motivo, erros em PT) aparecem fielmente no backend (ver doc 02, F8/F14).
2. **A maior fraqueza é a manutenção do contexto vivo**: o `borafut_contexto_prd.md`
   contradiz o próprio cabeçalho em 4 pontos (WebSocket "fora do MVP" no §4, rotação
   antiga nos §5.4/5.6, overrides removidos no §5.9, rotas `/co-organizer/` no §8.2).
   Qualquer PRD gerado hoje herda esses erros. O processo prescreve "atualize a
   documentação" mas não tem mecanismo que force isso. Proposta D1 (patch do contexto) e
   D2 (gate de sync no criar-pull-request).
3. **Reviews não deixam rastro**: o `fazer-review` proíbe salvar relatório por padrão.
   Para um projeto a dois, o relatório salvo é o registro do que o revisor aceitou.
   Proposta D3.
4. **Gaps menores**: falta template de ADR (a fase 8 do criar-techspec pede ADRs e a pasta
   `adr/` existe no futs-module, sem padrão); falta caminho leve para bugfix (pipeline
   completo é pesado demais para correções pequenas, o que incentiva pular o processo);
   o piso de testes do frontend não é exigido em lugar nenhum (e o repo tem zero testes).
   Propostas D4, D5, D6.
5. **Proposta mais drástica (D7)**: dividir o contexto em "núcleo estável" + "índice de
   navegação por seção", e padronizar nos cards do Jira as âncoras de seção (que vocês já
   fazem informalmente com "Fonte pro /fazer-prd: §X"). Reduz tokens por task e elimina a
   leitura integral de 2.900 linhas de contexto a cada PRD.

---

## 1. O que funciona (com evidência)

| # | Prática | Evidência |
|---|---|---|
| 1 | Pipeline com gates humanos: PRD exige perguntas antes de gerar; tasks exigem aprovação da lista high-level; IA nunca commita | `commands/criar-prd.md:32` (REGRA ABSOLUTA), `criar-tasks.md:25-27`, `executar-task.md:119-122` |
| 2 | Tasks auto-contidas (trechos da techspec copiados inline) - engenharia de contexto correta, o executor não navega entre arquivos | `criar-tasks.md:101-107` |
| 3 | Convenções de código embutidas no executor e refletidas no código real | `executar-task.md:64-71` vs achados F8/F14 do doc 02 |
| 4 | Review em 3 lentes com execução obrigatória de lint/test e checklist anti-superficial ("se só achou o óbvio, revisou raso") | `fazer-review.md:54-118` |
| 5 | "Arquivos lidos" como saída obrigatória da fase de esclarecimento - o humano audita o contexto consumido pela IA | `criar-prd.md:44-68` |
| 6 | Separação por repo com dependências externas explícitas | `criar-prd.md:22-28`, `criar-tasks.md:10-12` |
| 7 | PR autossuficiente com setup e cenários executáveis | `criar-pull-request.md` §"Requisito crítico", `templates/PULL_REQUEST_TEMPLATE.md` |
| 8 | Contexto compartilhado via submodule, mesma verdade nos dois repos | `README.md` §Estrutura, `.ia/shared` em ambos |
| 9 | Histórico de uso real: 9 features executadas pelo pipeline com PRD+TechSpec+tasks+PR | `.ia/tasks/` nos dois repos |
| 10 | Cards do Jira já apontam âncoras de seção da spec ("Fonte pro /fazer-prd: fut_in_app_context.md §8.5, §4.15.2...") | jira.csv (KAN-34, KAN-46, KAN-48...) |

## 2. O que limita (diagnóstico)

### L1. Contexto vivo sem mecanismo de sincronização (principal)
O fut_in_app_context.md nasceu fora do pipeline (sessão de refinamento) e o cabeçalho do
contexto base foi atualizado, mas o corpo não. Estado atual das contradições internas no
`borafut_contexto_prd.md`:
- §4 "Fora do MVP" item 7 ainda lista "Real-time via WebSocket" (contradiz §6 e o cabeçalho)
- §5.4 mantém "Rotação após partida / Substituição parcial" substituídas pela rotação
  unificada (fut_in_app §4.13)
- §5.9 lista overrides removidos do MVP (montar times manualmente, desfazer rotação,
  adicionar em posição específica - fut_in_app §4.14.7-9)
- §8.2 usa rotas `/futs/:id/co-organizer/:jogadorId` (vocabulário antigo) e §8.2 Futs
  lista `PATCH .../teams` ("editar composição manualmente"), removido do MVP
- §2 Papéis do Criador inclui "Montar times manualmente" implícito no item 8 do §5.9

Consequência: PRDs/TechSpecs gerados a partir do contexto herdam regra morta. O risco é
real porque os próximos cards (Fase 3/4) são exatamente os afetados.

### L2. Review sem rastro persistente
`fazer-review.md:17` proíbe salvar relatório por padrão. Entre vocês dois, o relatório é a
memória do que foi apontado e aceito; sem ele, re-reviews repetem trabalho e não há
evidência de processo.

### L3. Sem caminho leve para bugfix
O pipeline completo (PRD→TechSpec→Tasks) para um bug de 30 minutos é desproporcional. O
que acontece na prática (inferido pelos commits `fix:` diretos nos branches) é o bypass
total do processo: nem registro, nem teste de regressão exigido.

### L4. ADR citado mas sem template
`criar-techspec.md` fase 8 pede ADRs; `.ia/tasks/prd-futs-module/adr/` existe; não há
`adr-template.md` no shared. Cada ADR sai num formato diferente.

### L5. Definition of done não cobre o piso de testes do frontend
Contexto §11 exige "validação objetiva suficiente", mas o frontend não tem sequer runner
de teste (doc 02, I5). O enforcement por command não funciona se a infraestrutura não
existe.

### L6. Custo de contexto crescente
Hoje, gerar um PRD de fut exige ler ~1.130 linhas (contexto) + 1.779 linhas (fut_in_app).
Vai piorar. Vocês já mitigam com âncoras de seção nos cards do Jira; falta formalizar.

## 3. Propostas (diffs prontos; aplicar após sua revisão)

> **STATUS (11/06): D1 a D8 aprovados pelo Miguel. D1-D7 APLICADOS no BoraFut-ia em
> 11/06** (contexto sincronizado, gate de sync na PR, review com rastro salvo, template
> ADR criado, comando corrigir-bug criado, piso de testes no executar-task e no contexto
> §11, índice de leitura seletiva no fut_in_app + regra no criar-prd/criar-techspec).
> D8 é prática a adotar ao criar cards novos.
>
> **STATUS (13/07): tudo COMMITADO e em uso.** O BoraFut-ia tem os commits de sync
> (inclusive contrato real-time chegou-only no §8.3); o fluxo com rastro de review
> funciona na prática (`reviews/001-review.md` existe em gestao-partidas e
> fut-lifecycle-cron); o pipeline executou 7 cards de backend desde 11/06 com
> PRD→TechSpec→tasks→review. **Nova dívida de sincronização** (mesma natureza do L1):
> decisões dos cards recentes ainda não voltaram para o `fut_in_app_context.md` —
> sucessão de criador, `motivo_sistema`/ledger/receipts/tick 5min do cron, e-mail
> obrigatório no cadastro manual, elegibilidade de avaliação exigindo fut encerrado.
> Ação: rodada de sync do contexto (o gate D2 só cobre PRs novas; essas decisões
> passaram em PRs cujo sync ficou "pro final").

### D1. Patch de sincronização do `borafut_contexto_prd.md` [prioridade máxima]

```diff
--- a/borafut_contexto_prd.md  §4 Fora do MVP
-   6. Atualização colaborativa do mapa em escala
-   7. Real-time via WebSocket (Socket.IO) para tela de partida ao vivo
+   6. Atualização colaborativa do mapa em escala
+   (WebSocket/Socket.IO REMOVIDO desta lista: faz parte do MVP — ver §6 e fut_in_app_context.md §8)
```

```diff
--- a/borafut_contexto_prd.md  §5.4 Formação de times e rotação
-   #### Rotação após partida (time completo na fila)
-   ... (regras antigas de rotação completa)
-   #### Substituição parcial (fila incompleta)
-   ... (regras antigas)
-   #### Sem fila (0 na fila)
-   ...
+   #### Rotação após partida
+   A rotação é regida pela REGRA UNIFICADA do `fut_in_app_context.md` §4.13 (fonte de
+   verdade): time perdedor inteiro vai para o fim (posições MAX+1 em ordem), recalcula
+   primeiros N×2. Casos especiais: "0 na fila" e "jogadores insuficientes" (§4.13.1).
+   As antigas regras de "substituição parcial" foram absorvidas pela regra unificada.
```

```diff
--- a/borafut_contexto_prd.md  §5.9 Override do criador
-   7. Adicionar jogador em posição específica da fila
-   8. Montar times manualmente (ignorar automação)
-   9. Desfazer rotação automática de times
+   (Itens 7-9 REMOVIDOS do MVP — ver fut_in_app_context.md §4.14.7-4.14.9 e §10.7.
+   Cobertos por [Trocar jogador] e [Mover jogador na fila].)
```

```diff
--- a/borafut_contexto_prd.md  §8.2 Endpoints
-   24. PATCH /futs/:id/match/:partidaId/teams — editar composição de times manualmente (criador)
+   24. POST /futs/:id/swap — trocar dois jogadores (quadra↔quadra, quadra↔fila, fila↔fila) — fut_in_app §4.14.4
...
-   26. POST /futs/:id/co-organizer/:jogadorId — promover a organizador
-   27. DELETE /futs/:id/co-organizer/:jogadorId — revogar organizador
+   26. POST /futs/:id/organizers/:jogadorId — promover a organizador
+   27. DELETE /futs/:id/organizers/:jogadorId — revogar organizador (só criador)
```

Adicionalmente (achado M3 do doc 02): registrar em §5.1 a regra implementada "fut só pode
ser criado com `dateTime` até 30 dias no futuro".

### D2. Gate de sincronização no `criar-pull-request.md`

```diff
--- a/commands/criar-pull-request.md
 ## Checklist antes de finalizar
+
+### Sincronização de specs (obrigatório)
+Antes de gerar o texto da PR, responda explicitamente:
+1. Esta mudança alterou ou contrariou alguma regra do `borafut_contexto_prd.md` ou do
+   `fut_in_app_context.md`? Liste as seções afetadas.
+2. Se sim: gere o diff de atualização do(s) documento(s) e inclua na PR (ou registre
+   "spec update pendente" na descrição, com as seções).
+3. Se não: escreva "Specs verificadas: sem impacto" na seção Impactos da PR.
```

### D3. Rastro de review no `fazer-review.md`

```diff
--- a/commands/fazer-review.md
 ## Escopo
 ...
-- Não salva relatório em arquivo, salvo instrução explícita do usuário
+- Salva o relatório final em `.ia/tasks/prd-[funcionalidade]/reviews/NNN-review.md`
+  (NNN incremental). Se a feature não tiver pasta em `.ia/tasks`, apresenta apenas no
+  chat. O relatório salvo é o único arquivo que este comando pode criar.
```

### D4. Novo `templates/adr-template.md`

```markdown
# ADR-NNN: [Título da decisão]

- Status: proposta | aceita | substituída por ADR-XXX
- Data: AAAA-MM-DD
- Contexto da feature: .ia/tasks/prd-[funcionalidade]/

## Contexto
[Problema e forças em jogo. 2-5 frases.]

## Decisão
[O que foi decidido, em voz ativa. 1-3 frases.]

## Alternativas consideradas
1. [Alternativa] - [por que não]
2. [Alternativa] - [por que não]

## Consequências
- Positivas: [...]
- Negativas/dívidas: [...]
- Impacto em specs: [seções do contexto/fut_in_app a atualizar, se houver]
```

### D5. Novo `commands/corrigir-bug.md` (caminho leve)

```markdown
# Corrigir Bug

Fluxo leve para defeitos pequenos. NÃO usar para features ou refatorações (use o
pipeline completo). Se durante o diagnóstico o escopo crescer além de ~meio dia,
PARE e recomende migrar para o pipeline.

## Etapas
1. Reproduzir: descreva o passo a passo que reproduz o bug (ou o teste que falha).
   Se não conseguir reproduzir, PARE e pergunte.
2. Diagnosticar: localize a causa raiz (arquivo:linha) e explique em 2-3 frases.
   Não trate sintoma.
3. Verificar specs: a regra correta está no `borafut_contexto_prd.md` /
   `fut_in_app_context.md`? Cite a seção. Se o comportamento esperado não estiver
   especificado, PARE e pergunte.
4. Corrigir: menor mudança que resolve a causa raiz, seguindo os padrões do
   `executar-task.md` (SQL raw, erros em PT, etc.).
5. Proteger: escreva/ajuste um teste que falharia sem a correção (obrigatório no
   backend; no frontend, quando a causa for função pura/hook).
6. Validar: lint + testes relevantes.
7. Entregar: `git add` (staged) + sugestão de commit `fix: ...`. NUNCA commitar.

## Saída
- Causa raiz (arquivo:linha) e explicação
- Diff aplicado e teste de regressão
- Resultado das verificações
- Sugestão de mensagem de commit
```

### D6. Piso de testes do frontend no `executar-task.md` e no contexto §11

```diff
--- a/commands/executar-task.md  (Diretrizes específicas para frontend)
 7. Garantir labels acessíveis em botões, chips, cards clicáveis e ações protegidas.
+8. Testes mínimos: toda função pura nova/alterada em `utils/`, `features/*/mappers.ts`
+   ou cálculo de estado (ex.: timer de partida, redutores de eventos) deve ter teste
+   unitário (jest-expo). Componentes visuais podem ficar em validação manual.
```

```diff
--- a/borafut_contexto_prd.md  §11 Definição de pronto
-   9. Quando houver backend, deve existir validação objetiva suficiente para o escopo...
+   9. Quando houver backend, deve existir validação objetiva suficiente para o escopo...
+   10. Frontend: funções puras e hooks com lógica de domínio têm teste unitário;
+       telas têm roteiro de validação manual descrito na task.
```

Pré-requisito: card pequeno no Jira "Setup jest-expo no frontend" (não existe hoje).

### D7. [Drástica] Índice de navegação e leitura seletiva de contexto

Problema: cada `/criar-prd` lê ~2.900 linhas de specs. Proposta em duas partes:

1. **Índice no topo do `fut_in_app_context.md`** (e do contexto base): tabela
   seção → tema → "leia se o card menciona...". Exemplo:

```markdown
## Índice de leitura seletiva
| Seção | Tema | Leia quando o card envolver |
|---|---|---|
| §3.1-3.8 | Pré-fut (criação, presença, convite, lembretes) | CRUD de fut, presença, push pré-fut |
| §4.1-4.2 | Chegada e modelo da fila | check-in, fila, lista |
| §4.3-4.15 | Partida ao vivo (eventos, rotação, override, encerramento) | match, timer, gols, pênaltis |
| §5 | Fut não-gerenciado | modo não-gerenciado, coleta passiva, ativação |
| §6 | Pós-fut (card, stats, avaliação) | card.png, estatísticas, reviews |
| §7 | Modelo de dados (migration aditiva) | schema, migrations, seeds |
| §8 | Real-time, cache, cron | WebSocket, polling, ETag, cron |
| §9 | Cenários completos | validação de regra fim a fim |
```

2. **Regra nos commands** (criar-prd/criar-techspec, fase de análise): "leia o índice e as
   seções apontadas pelo card ('Fonte pro /fazer-prd'); leia integralmente apenas o que o
   índice indicar; liste em 'Arquivos lidos' as seções puladas". Os cards do Jira já trazem
   as âncoras; isso só formaliza o que vocês praticam.

Ganho: menos tokens e menos ruído por task, sem perder a fonte única. Risco: seção
relevante fora da âncora; mitigado pelo índice + a seção "Arquivos lidos" auditável.

### D8. (Opcional, barato) Registrar o pipeline nos cards
Adicionar ao texto-padrão dos cards do Jira um rodapé fixo "Pipeline: /criar-prd →
/criar-techspec → /criar-tasks → /executar-task → /fazer-review → /criar-pull-request"
com o slug da pasta `.ia/tasks/` correspondente, fechando a rastreabilidade
Jira ↔ artefatos (hoje o vínculo é implícito pelo nome).

## 4. Ordem de aplicação sugerida

| Ordem | Proposta | Esforço | Quando |
|---|---|---|---|
| 1 | D1 (sync do contexto) | 30 min | antes de qualquer /criar-prd da Fase 3 |
| 2 | D6 + card jest-expo | 1-2 h | antes do KAN-33 terminar |
| 3 | D2 (gate de sync na PR) | 10 min | junto com D1 |
| 4 | D3 (rastro de review) | 10 min | imediato |
| 5 | D4 (ADR template) | 15 min | antes da techspec do realtime (KAN-47, cheia de decisões) |
| 6 | D5 (corrigir-bug) | 20 min | antes do beta (julho: bugs vão aparecer) |
| 7 | D7 (índice) | 1 h | quando começar a Fase 4 (specs mais densas) |
| 8 | D8 | 5 min/card | oportunístico |

## 5. Oportunidades adicionais (registro, sem execução)

1. **Medir o processo**: anotar por task tempo total e nº de idas-e-voltas de review num
   `metrics.md` simples. Em 2-3 meses vocês saberão onde o pipeline gasta tempo de
   verdade (útil para o negócio, independentemente do TCC).
2. **Prompt de "retomada de contexto"**: um `commands/retomar.md` que resume estado do
   board, branches abertos e specs pendentes ao abrir uma sessão nova: reduz o custo de
   recontextualizar a IA a cada dia de trabalho.
3. **Validação cruzada dos cenários do fut_in_app §9**: os 13 cenários (A-M) são ouro para
   teste automatizado; transformá-los em suíte de integração quando KAN-37 for executado
   (cada cenário vira um teste nomeado "Cenário A - rotação normal").

## 6. Premissas e incertezas

### Verificado
- Lidos integralmente: `README.md`, `commands/criar-prd.md`, `commands/executar-task.md`,
  `commands/fazer-review.md`, `commands/criar-tasks.md`; headers/estrutura de
  `criar-techspec.md` (373 linhas, fases 1-10), `criar-pull-request.md` e dos 5 templates;
  estrutura real das 9 pastas `.ia/tasks/` nos dois repos; contradições do contexto
  conferidas linha a linha (`borafut_contexto_prd.md` §4, §5.4, §5.9, §8.2 vs
  `fut_in_app_context.md` §4.13, §4.14, §8, §11).
- A aderência código↔convenções é evidenciada pelos achados do doc 02 (F8, F14).

### Inferido
- Que bugfixes pequenos pulam o pipeline: inferido dos commits `fix:` nos branches sem
  pasta `.ia/tasks` correspondente; não confirmei caso a caso.
- O corpo completo do `criar-techspec.md` (li fases pelos títulos) e o conteúdo integral
  dos PRDs/TechSpecs já gerados (li estrutura, não o texto): a avaliação de qualidade
  desses artefatos é por amostragem estrutural.
- O custo de tokens (L6) é estimativa por contagem de linhas, não medição real.
