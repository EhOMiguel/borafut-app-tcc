# 02 - Avaliação de Código: BoraFut Backend e Frontend

> Review dos repositórios `BoraFut-Backend` (branch `feat/futs-module`) e `BoraFut-Frontend`
> (branch `feat/court-reviews`), nas cabeças dos branches em 11/06/2026.
> Cada achado tem severidade (crítico / importante / menor), referência arquivo:linha e a
> classificação **[COBERTO]** (já previsto na refatoração pendente KAN-46 ou em card
> existente do Jira) ou **[NOVO]** (gap real, sem cobertura planejada).
> Critério da hierarquia de verdade: onde o código diverge do `fut_in_app_context.md` e a
> divergência está no escopo de um card, é atraso esperado, não defeito.
>
> **RE-AUDITADO EM 13/07/2026** (backend `feat/fut-lifecycle-cron`, frontend `main`).
> Status atualizado por achado abaixo. Resumo da reauditoria:
> - Backend avançou 7 cards desde 11/06: schema/papéis (KAN-46), presença/chegada/fila,
>   tempo real (WS + cache/ETag), **gestão de partidas (fut ao vivo completo)**, push infra,
>   lifecycle read-path e lifecycle cron. 665 testes unit + 57 e2e verdes; CI nos dois repos.
> - **C1 (SES) CONTINUA ABERTO** — e-mail segue 100% mock; é o único bloqueador externo do
>   beta e o item mais urgente do projeto.
> - I2 resolvido (KAN-46 executado); I3 segue parcial (presença informal não existe);
>   I4 continua aberto (main.ts sem helmet/throttler); I5 resolvido (jest-expo, 25 arquivos
>   de teste no frontend). M1/M2/M4 resolvidos (card refactor-menores); M7 segue aberto.
> - **Novo achado importante (13/07): contrato de `GET /courts/:id` divergente** — o backend
>   renomeou `futsDoDia`→`futsSemanais` (com `criador`/`nome`/`cor`); o frontend
>   (`CourtScheduleTab.tsx`, `features/courts/types.ts`) ainda consome `futsDoDia` com
>   `organizador`. A aba de agenda da quadra está silenciosamente vazia no app.
>   DECISÃO 13/07: correção já prevista em card do board; usar
>   `BoraFut-ia/api-rotas-contratos.md (cópias em .ia/shared/)` (novo doc de contratos) como fonte.
> - `feat/gestao-partidas` (match) foi MERGEADA na main (PR #26). O cron
>   (`feat/fut-lifecycle-cron`) recebeu as correções da review 001 + auditoria em 13/07
>   (commit `dcd7735`), foi PUSHADO para o origin e aguarda PR/merge.

## Sumário executivo

1. **A qualidade geral é alta e acima da média de projeto acadêmico**: transações
   serializáveis com retry centralizado, refresh token com hash + rotação, consumo atômico
   de códigos de login, DTOs rigorosos, convenções de erro consistentes, frontend com
   single-flight de refresh e token de acesso só em memória. A fundação sustenta o que vem
   na Fase 3-4.
2. **1 achado crítico [NOVO]**: não existe implementação real de envio de e-mail (só mock;
   `EMAIL_PROVIDER != mock` derruba o boot). Sem isso, **nenhum usuário externo consegue
   logar** no beta de julho nem no lançamento de 10/09. Não há card no Jira para o adapter
   SES. É o item de maior urgência do projeto inteiro.
3. **2 achados importantes [NOVO]**: (a) `/courts/nearby` não implementa bounding box em
   nenhuma das pontas, contrariando o contrato do PRD, os cards KAN-27/28 e o RNF02 da
   monografia; (b) ausência de hardening HTTP e rate limit global para o lançamento
   público. Mais 1 importante de processo: o frontend tem **zero testes automatizados**.
4. **Os desvios de vocabulário e regra de presença** (organizador→criador, bloqueio de
   presença sobreposta na criação, elegibilidade de avaliação por presença informal) estão
   todos **[COBERTO]** por KAN-46/KAN-34/KAN-49: não são gap real.
5. Recomendações prioritárias antes da Fase 3: criar card "SES adapter + sandbox/verified
   identities" (bloqueia beta), decidir bounding box (implementar ou despriorizar
   formalmente e ajustar o texto do TCC), e um card curto de "production hardening"
   (helmet, throttler, correlação de logs) antes de 10/09.

---

## 1. Pontos fortes (com referência)

### Backend

| # | Ponto forte | Referência |
|---|---|---|
| F1 | Transação serializável com retry automático de conflito (P2034) centralizada em um único helper, usada por auth, futs e reviews | `src/prisma/prisma.service.ts:33-52` |
| F2 | Refresh token nunca armazenado em claro: SHA-256 no banco, rotação a cada refresh, revogação atômica com `WHERE revogado = false` | `src/modules/auth/auth.service.ts:477-479, 533-546, 641-657` |
| F3 | Código de verificação com consumo atômico via optimistic concurrency (`AND tentativas = X AND expira_em > now` no UPDATE) - elimina corrida de duplo uso | `auth.service.ts:587-639` |
| F4 | Falha no envio de e-mail expira o código recém-criado (rollback compensatório) e retorna 503 honesto | `auth.service.ts:112-128` |
| F5 | Rate limit de request-code por janela contado no banco, dentro da transação | `auth.service.ts:82-110` |
| F6 | Geração de share code com retry de colisão e detecção de unique violation em 3 formatos de erro | `src/modules/futs/futs.service.ts:444-515, 572-608` |
| F7 | Cancelamento de fut idempotente: UPDATE condicionado ao status no WHERE, à prova de corrida | `futs.service.ts:273-285` |
| F8 | Padrão de validação de DTO segue à risca o contexto (§6 Validação de DTOs): `@ValidateIf` para rejeitar null em campos non-nullable, bounds e whitelists | `src/modules/futs/dto/create-fut.dto.ts`, `src/modules/auth/dto/update-me.dto.ts` |
| F9 | `calcularCorDoPin` é função pura exportada (testável isoladamente) e implementa exatamente as faixas do PRD §5.12 | `src/modules/courts/courts.service.ts:92-124` |
| F10 | Elegibilidade de avaliação já contempla fut não-gerenciado (condição 2 do fut_in_app §6.3) | `courts.service.ts:381-402` |
| F11 | Reviews com versionamento (inativa + nova linha), cooldown de edição, primeira página destacando a própria avaliação - regras do contexto §5.14 fielmente implementadas | `src/modules/courts/court-reviews.service.ts:131-300` |
| F12 | `ClockService` injetável: tempo testável, pré-requisito correto para o cron de backstop futuro | `src/common/clock.service.ts`, uso em todos os services |
| F13 | IDs de status carregados no boot com falha explícita se o seed não rodou | `futs.service.ts:105-142`, `courts.service.ts:138-175` |
| F14 | Mensagens de erro ao cliente em português, padronizadas conforme contexto §6 | services em geral |
| F15 | 10 arquivos de teste cobrindo os services críticos (auth, futs, courts, reviews, utils, validators) | `find src -name "*.spec.ts"` |

### Frontend

| # | Ponto forte | Referência |
|---|---|---|
| F16 | Fronteira HTTP única: timeout com AbortController, erro normalizado (`ApiError`), retry pós-refresh com flag anti-loop | `src/services/http.ts:46-122` |
| F17 | Refresh com single-flight lock (uma promise compartilhada para N requests 401 simultâneas) | `src/contexts/AuthContext.tsx:83-117` |
| F18 | Access token vive só em memória (ref), refresh token só no SecureStore - exatamente o modelo do contexto §6 | `AuthContext.tsx:44-52`, `src/services/storage.ts:1-7` |
| F19 | Bootstrap de sessão silencioso com estados explícitos (`sem_sessao/ativo/expirado/deslogado`) e modo visitante | `AuthContext.tsx:197-237, 165-172` |
| F20 | TanStack Query com query keys centralizadas, staleTime e invalidação pós-mutação | `src/features/courts/hooks.ts:50-200` |
| F21 | Organização por camadas fiel ao contexto §7.1 (app/ components/ features/ contexts/ services/ theme/) | árvore de `src/` |

---

## 2. Achados - Crítico

### C1. Não existe envio real de e-mail; login impossível para usuários externos [NOVO]

> **STATUS 13/07/2026: AINDA ABERTO.** `email.module.ts` continua aceitando apenas
> `EMAIL_PROVIDER=mock` (boot falha com qualquer outro valor); não há `SesEmailService`.
> Com beta previsto para ~fim de julho, este é o item mais crítico do projeto (o prazo de
> saída do sandbox da AWS é externo). Nenhum outro achado bloqueia o beta.
- **Onde:** `src/modules/email/email.module.ts:16-23` (qualquer `EMAIL_PROVIDER` diferente
  de `mock` lança `Unsupported email provider` no boot); `src/modules/email/mock-email.service.ts:12-22`
  (mock retorna sucesso sem enviar; só loga o código em development/test).
- **Impacto:** o fluxo de autenticação inteiro depende do código por e-mail. Em qualquer
  ambiente com usuários reais (beta fechado de julho, lançamento 10/09), ninguém que não
  tenha acesso aos logs do servidor consegue entrar. Em produção o mock devolve `200 ok`
  sem enviar nada: falha silenciosa.
- **Cobertura no Jira:** nenhuma. KAN-43 é push, KAN-45 é S3. O adapter SES não tem card.
- **Recomendação:** criar card imediato "EmailService SES" (adapter `SesEmailService` na
  factory do `EmailModule`, config de credenciais, verified identity/sandbox-exit da AWS,
  template do código). Atenção a prazo externo: a saída do sandbox do SES depende de
  aprovação da AWS, que pode levar dias - pedir cedo. A interface `EmailService` já está
  pronta para receber o adapter (bom design); o trabalho é pequeno, o risco é o prazo AWS.

## 3. Achados - Importante

### I1. `/courts/nearby` sem bounding box nas duas pontas [NOVO]
- **Onde:** backend `src/modules/courts/courts.controller.ts:23-26` (rota sem query
  params) e `courts.service.ts:549-563` (`SELECT ... FROM quadra ORDER BY nome` retorna
  todas); frontend `src/features/courts/api.ts:17` (chama `/courts/nearby` sem parâmetros;
  nenhum refetch por movimento de mapa/debounce).
- **Impacto:** funciona hoje (32 quadras), mas: (a) contradiz o contrato do contexto §8.1
  ("quadras por bounding box") e os cards KAN-27/KAN-28 ("bounding box + debounce 500ms"),
  que constam como concluídos; (b) contradiz o RNF02 da monografia ("limitando os
  resultados do mapa à área visível"), que a banca pode verificar; (c) não escala quando o
  catálogo crescer para outras RAs.
- **Cobertura:** nenhuma (cards dados como done).
- **Recomendação:** decidir explicitamente. Opção A: implementar bounding box simples
  (4 query params + `WHERE latitude BETWEEN ...`, índice já existe em `idx_quadra_lat_lng`)
  + refetch com debounce no mapa. Opção B: registrar a decisão no contexto.
- **RESOLVIDO (11/06):** opção B por decisão do Miguel - carregamento total é estratégia
  consciente do MVP (custo desnecessário com catálogo pequeno); bounding box entra pós-MVP
  com a expansão do catálogo. Decisão registrada no `borafut_contexto_prd.md` §8.1/§4.
  O texto do RNF02 do TCC fica como está, também por decisão do autor (sugestão opcional
  de reformulação registrada no doc 04, adendo §0). Sem card.

### I2. Vocabulário e regras do fut atrasados em relação ao fut_in_app [RESOLVIDO 13/07]

> **STATUS 13/07/2026: RESOLVIDO.** KAN-46 (`prd-fut-schema-papeis`) executado: schema tem
> `criador_id`, `participacao.organizador`, timestamps, `removido_por_admin`, `log_chegada`;
> o bloqueio de presença sobreposta (§3.3) está implementado em `PresencaSobreposicaoService`
> e roda na criação do fut, no join e no cadastro manual.
- **Onde (amostras):** `prisma/schema.prisma:141` (`organizadorId`), `:182`
  (`coOrganizador`); `src/modules/futs/futs.service.ts:50, 165-184` (criador entra como
  participação confirmada **sem** validar bloqueio de presença sobreposta do §3.3);
  `futs.types.ts` expõe `organizador`/`coOrganizador` no contrato.
- **Impacto:** nenhum hoje; é o atraso previsto. O rename muda contrato de API (resposta
  `organizador` → `criador`), o que exige sincronizar com o frontend (KAN-33 em andamento
  usa o vocabulário novo na UI).
- **Recomendação:** nada além de executar KAN-46 antes de KAN-34/35, como já sequenciado.
  Cuidado de coordenação: o KAN-33 (front) está sendo construído contra a resposta atual;
  alinhar a ordem de merge para não quebrar o app do Arthur.

### I3. Elegibilidade de avaliação sem a condição de presença informal [AINDA PARCIAL 13/07]

> **STATUS 13/07/2026: SEGUE PARCIAL.** `ensureEligibleToReview` cobre as condições 1 e 2
> (agora exigindo fut **encerrado** também no caso gerenciado — pequena divergência textual
> com o §6.3, razoável de produto; atualizar o fut_in_app). A condição 3 (`presenca_informal`)
> continua impossível: o evento nunca é gravado porque a coleta passiva do não-gerenciado
> (§5.2) não foi implementada (card KAN-49 não executado).
- **Onde:** `courts.service.ts:381-402` (canReview cobre condições 1 e 2 do §6.3; condição
  3 depende de `log_chegada`, que nasce no KAN-46 e é populada no KAN-49).
- **Recomendação:** apenas garantir que o card KAN-49 inclua o ajuste do `canReview` (o
  texto do card já menciona "elegibilidade de avaliação ajustada (§6.3)" - ok).

### I4. Sem hardening HTTP nem rate limit global para o lançamento [NOVO]
- **Onde:** `src/main.ts:9-28` (sem `@fastify/helmet`, sem CORS explícito, sem throttler
  global). Endpoints públicos (`/courts/nearby`, `/courts/search`, `/futs/share/:token`)
  não têm limite de requisição; o rate limit existente é só do request-code
  (`auth.service.ts:82-96`).
- **Impacto:** irrelevante em dev; relevante a partir do beta público e crítico no
  lançamento (scraping do catálogo, brute force de share codes - hoje mitigado pelo espaço
  do código, enumeração de quadras, abuso de busca).
- **Cobertura:** nenhuma (Fase 5 "Polimento" não menciona segurança HTTP).
- **Recomendação:** card curto "Production hardening" antes de 10/09: `@nestjs/throttler`
  (limites generosos), `@fastify/helmet`, tamanho máximo de payload, e revisão de logs
  (M6). Esforço ~1 dia.
- **DECIDIDO (11/06):** não havia nada planejado para a borda; aprovada a opção mínima
  proxy gratuito (Cloudflare) na frente do backend + hardening. Card 2 redigido em
  `cards-jira.md`.
- **STATUS 13/07/2026: AINDA ABERTO.** `main.ts` segue sem helmet/throttler/limite de
  payload; o PRD do cron cita "rate limiting dos GETs públicos (KAN-56)" como fora de
  escopo, então o card existe no board mas não foi executado. Necessário antes de 10/09.

### I5. Frontend sem nenhum teste automatizado [RESOLVIDO 13/07]

> **STATUS 13/07/2026: RESOLVIDO.** jest-expo configurado (fuso fixado no jest.config e no
> CI), 25 arquivos `*.spec.*` cobrindo telas de auth, mapa, detalhe de quadra, mappers e
> hooks. CI (`.github/workflows/ci.yml`) ativo nos dois repos.
- **Onde:** repositório inteiro (`find` não encontra `*.test.*`/`*.spec.*`; `package.json`
  sem runner de testes).
- **Impacto:** o rename criador/organizador (KAN-46) e a Fase 4 (partida ao vivo, com
  lógica de timer client-side e aplicação idempotente de eventos WS) vão mexer em código
  sem rede de proteção. A "definição de pronto" do contexto §11 pede validação objetiva.
- **Recomendação:** não é preciso cobertura ampla; um piso pragmático: `jest-expo` +
  testes de unidade para funções puras críticas que estão chegando (cálculo do cronômetro
  a partir de `inicio/tempoPausadoMs/pausadoEm`, mappers de `features/*/mappers.ts`,
  redutores de eventos WS). Incluir como critério de aceite nos cards da Fase 4, não como
  retrofit geral.

## 4. Achados - Menor

> DECIDIDO (11/06): todos aprovados pelo Miguel. M1, M2 e M4 entram no próximo card de
> refactor (card 7 de `cards-jira.md`); M3 já documentado no contexto (patch D1); M5/M6
> cobertos pelos cards 1 e 2; M7 sem ação.
>
> **STATUS 13/07/2026:** M1 RESOLVIDO (`updateMe` roda em `runInTransaction`,
> `auth.service.ts:267`); M2 RESOLVIDO (`timingSafeEqual`, `auth.service.ts:378`);
> M3 resolvido (regra dos 30 dias documentada no contexto §5.1); M4 resolvido no card
> refactor-menores; M5 segue aberto (sem correlação de logs — entra no hardening/I4);
> M6 sem ação (correto); **M7 SEGUE ABERTO** (`GET /version` e exclusão de conta LGPD não
> existem — relevante para publicação nas lojas até ~29/08).

| # | Achado | Classificação | Onde | Nota |
|---|---|---|---|---|
| M1 | `updateMe` faz read-merge-write sem transação: dois PATCH /me concorrentes podem perder campos (last-write-wins do merge) | [NOVO] | `auth.service.ts:252-335` | Risco real baixo (mesmo usuário); embrulhar em `runInTransaction` quando tocar no arquivo |
| M2 | Comparação do código de verificação não é constant-time | [NOVO] | `auth.service.ts:151` | Mitigado por 5 tentativas + rate limit; trocar por `crypto.timingSafeEqual` é 3 linhas |
| M3 | Regra "fut até 30 dias no futuro" não existe no PRD/contexto | [NOVO] | `futs.service.ts:72` (`FUT_CREATE_MAX_DAYS`) | Decisão boa, mas indocumentada: registrar no `borafut_contexto_prd.md` §5.13 ou §5.1 |
| M4 | Array Postgres montado por string join (`{a,b,c}::uuid[]`) | [NOVO] | `courts.service.ts:207, 221` | Seguro hoje (UUIDs vêm do banco); preferir `Prisma.join` se o padrão for reutilizado com input externo |
| M5 | Logs de domínio sem request-id/correlação | [NOVO] | `futs.service.ts:756-761` e equivalentes | Suficiente para MVP; adicionar correlação no card de hardening (I4) |
| M6 | Fallback do refresh token em memória quando SecureStore indisponível | [NOVO] | `src/services/storage.ts:9-13` | Comportamento correto p/ Expo Go; já documentado em comentário; sem ação |
| M7 | `GET /version` e exclusão de conta LGPD ainda não existem (tabela `versao` sem endpoint) | [COBERTO - KAN-40] | schema `prisma/schema.prisma:361-370` | Atraso esperado da Fase 5; relevante p/ lojas (LGPD é requisito de publicação) |

## 5. Divergências encontradas (código ↔ especificações)

1. **Contrato `nearby`** (I1): contexto §8.1 e Jira dizem bounding box; código não tem.
   Não coberto por nenhum card aberto. Decidir A/B conforme I1.
2. **`borafut_contexto_prd.md` desatualizado em relação ao próprio cabeçalho**: §4 ainda
   lista WebSocket como "fora do MVP", §5.4/5.6 mantêm a rotação antiga, §5.9 lista
   overrides removidos, §8.2 usa rotas `/co-organizer/`. O código não diverge disso ainda
   (módulos não construídos), mas qualquer PRD gerado a partir do contexto herdará o erro.
   Ação recomendada no `03-avaliacao-fluxo-ia.md` (sync do contexto).
3. **Monografia RNF02/RNF07 vs código**: o texto afirma limitação por área visível e
   catálogo georreferenciado do DF; o código carrega tudo e o catálogo cobre só Asa Norte
   (32 quadras). Tratado no plano (doc 04) como decisão de texto ou de implementação.

## 6. Recomendações priorizadas (revisadas em 13/07/2026; decisões do Miguel aplicadas)

| Prioridade | Ação | Status/Decisão (13/07) |
|---|---|---|
| P0 | Card SES (C1) + saída do sandbox AWS | **DECISÃO: aguardar mais um pouco** (registrado). Continua sendo o gate do beta: sem SES, o beta roda só com verified identities (lista fixa de e-mails no sandbox) |
| P0 | Contrato `futsSemanais`/`futsDoDia` no frontend | Correção **já prevista em card** do board |
| P1 | Correções da review do lifecycle-cron + merge | **FEITO 13/07**: correções aplicadas (commit `dcd7735`), 670 unit + 57 e2e verdes, branch pushada; falta abrir a PR e mergear |
| P1 | Frontend do fluxo de fut (criar → detalhe → presença → fila → partida + WS) | **Previsto em card**; caminho crítico do beta; insumo: `api-rotas-contratos.md (BoraFut-ia / .ia/shared)` |
| P2 | Card "production hardening" (I4 + M5) | **Previsto em card**; antes de 10/09 |
| P2 | M7: `GET /version` + exclusão de conta LGPD | Documentado; antes da submissão às lojas (~29/08) |
| — | ~~Bounding box (I1)~~ decisão registrada; ~~KAN-46 (I2)~~, ~~testes frontend (I5)~~, ~~M1/M2/M4~~ executados | — |

## 7. Premissas e incertezas

### Verificado (li o código)
- Backend: `main.ts`, `app.module.ts`, `prisma.service.ts`, `auth.service.ts` (integral),
  `futs.service.ts` (integral), `futs.controller.ts`, `courts.service.ts` (integral),
  `courts.controller.ts`, `court-reviews.service.ts` (linhas 1-300 + assinatura de todos os
  métodos), `email.module.ts`, `mock-email.service.ts`, `jwt.strategy.ts`, DTOs de fut e
  perfil, `schema.prisma`, `seed.ts` (valores de status). Contagem de testes via find.
- Frontend: `http.ts`, `AuthContext.tsx`, `storage.ts` (integrais), árvore completa de
  `src/`, grep de `nearby/staleTime/refetch` em `features/courts`. Ausência de testes
  verificada por find + package.json.
- Estado dos branches e Jira: `git log` de ambos os repos; jira.csv completo.

### Inferido / não verificado (lacunas declaradas)
- Não li: telas do frontend em profundidade (`map.tsx`, `court/[id].tsx`, `create-fut.tsx`,
  fluxo de onboarding), `features/courts/hooks.ts` integral, os arquivos `.spec.ts` do
  backend (assumi pela existência e nomes que cobrem os services), migrations SQL,
  conteúdo integral de `court-reviews.service.ts` (300-870), `Makefile`. CI: confirmado em 11/06 pelo Miguel que
  **não existe** - card 3 de `cards-jira.md` criado.
- A avaliação de que os specs do backend dão cobertura adequada é inferência pelo padrão
  dos nomes; uma rodada de `make test` com coverage confirmaria.
- Severidade de I4 assume exposição direta do backend à internet no lançamento; se houver
  CDN/WAF na frente, recalibrar.
