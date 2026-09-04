# Plano de cards — julho/2026 (limpeza do board + cards novos priorizados)

> Gerado em 21/07/2026 cruzando o export do Jira (board KAN) com o estado real do código
> (backend em `origin/main`) e da monografia (working tree). Duas partes:
> **A) Limpeza** — cards que já podem fechar. **B) Cards a criar** — propostas prontas,
> ordenadas por prioridade, para executar em chats dedicados.
>
> **Regra de ouro para os cards de escrita do TCC:** todo texto novo entra no padrão do que
> já está escrito — sem travessão (—); "partida" preferencial e "fut" só entre aspas para
> rótulo de interface; papéis **Criador** (autoridade máxima) e **Organizador** (promovido);
> app **não reserva** quadra (linguagem de melhor esforço); invariante N2 (stack idêntica em
> Cap3 + Cap4 §arquitetura + Cap5 §C4); toda figura/tabela citada no texto; e, a cada
> conceito recorrente tocado, `grep` em todos os `.tex` de `latex/editaveis/` para
> reconciliar (regra §8.4 do plano mestre). Insumo de produto: `fut_in_app_context.md`,
> `borafut_contexto_prd.md`, `api-rotas-contratos.md` (no BoraFut-ia / `.ia/shared`).

---

## STATUS DA EXECUÇÃO (atualizado em 23/07/2026)

| Card | Status | Evidência |
|---|---|---|
| **B1** Cap 6.5 + 6.6 | **FEITO** | `desenvolvimento.tex` §6.5 (5 subseções) e §6.6 (3 subseções); commit `367e3e9` |
| **B2** P20 modelo de dados | **FEITO** | dicionário regenerado (`apendices.tex`), MER conceitual (`mer-conceitual.puml` + render), lógico em DBML desenhado no dbdiagram (`modelo-logico.dbml` + `render/modelo-logico.png`), §Modelo de dados reescrita; commit `52f779c` |
| **B3** Cap 6.7 + 6.8 | **FEITO** | `desenvolvimento.tex` §6.7 (qualidade) e §6.8 (5 ADRs em prosa); commit `52f779c` |
| **B9** cenários de integração | **FEITO E MERGEADO** | PR #32 na `main` (`76f8fe6`). 698 unit + 71 e2e contra Postgres real. Revelou e corrigiu o bug F1 de auth (contador de tentativas perdido no rollback) |
| **B8** privacidade + TCLE | **FEITO** | apêndice do TCLE (transcrição do formulário aplicado), §Plano de privacidade e segurança escrita na metodologia (antes comentada) e menção à tela de termos do questionário na §questionário. **Fecha KAN-16.** Siglas SUS e TCLE adicionadas |
| **B6** apêndice de protótipos | pendente | livre |
| **B7** brainwriting | **FEITO** | registro completo em `BoraFut-ia/documentos-banca/brainwriting-registro.pdf` (repo privado; o do TCC é público). Subseção *Brainwriting* da metodologia reescrita com data, adaptação do 6-3-5, os 41 cartões e a ponte para o backlog. **Fecha KAN-9** |
| **B4** Cap 7.1-7.3 | **travado por dado** | não há roteiro de entrevista nem respostas de SUS no repositório; é coleta a fazer, não escrita |
| **B5** instrumentação de divergência e desempenho | pendente, **por último** | escopo trocado em 23/07: o funil de adoção não sobrevive ao viés do beta fechado. Ver o card para a nova formulação |
| **B10 / B11 / B12** | bloqueados | esperam beta / Cap 7 / texto completo |

Fora da lista B, seguem em aberto no backend: **card pós-fut + estatísticas** (§6.1/§6.2 da
monografia dependem dele), SES, S3/CloudFront, hardening HTTP, `GET /version` e exclusão de
conta (LGPD).

---

## A. Limpeza do board — pode fechar agora

### A.1 Bugs [TCC1] da banca resolvidos no texto

| Card | Título | Evidência de que está feito |
|---|---|---|
| **KAN-14** | Backlog com mais granularidade (5-7 níveis) | FEITO. `metodologia.tex:358` §"Granularidade e hierarquia do backlog" (6 níveis Tema→Subtarefa) + `tab:decomposicao` + `tab:backlog-temas` no apêndice de US. Fecha o P12. |
| **KAN-12** | Referência da Figura 9 errada | FEITO. `projetoborafut.tex:311` tem `\label{sec:prototipacao}` e a referência em `:283` resolve corretamente. Fecha o P08. |

### A.2 Cards de backend já mergeados na main (Jira desatualizado)

Estão como "Em análise"/"Em andamento" no board, mas o PR já está na `main`. Fechar todos:

| Card | Título | PR |
|---|---|---|
| **KAN-34** | Presença + Chegada + Fila [Backend] | PR #23 (mergeado) |
| **KAN-47** | Infra Real-time: WebSocket [Backend] | PR #24 (mergeado) |
| **KAN-37** | Partida ao Vivo [Backend] | PR #26 (mergeado) |
| **KAN-48** | Cron de Backstop + Encerramento Automático [Backend] | mergeado (correções da review 001 + auditoria aplicadas, commit `dcd7735`) |
| **KAN-49** | Fut Não-Gerenciado [Backend] | PR #31 (mergeado) |

Com isso, **todo o backend do fluxo do fut está fechado** (Fases 1-4 backend). O que sobra de
backend são features de lançamento/pós-fut, não o núcleo.

### A.3 Continuam abertos (viram card de escrita na Parte B — não fechar)

- **KAN-13** (Figura 10 protótipos nos apêndices) → coberto pelo card **B6** (apêndice de protótipos, P09).
- **KAN-9** (Brainwriting documentação) → coberto pelo card **B7** (P06).
- **KAN-16** (mencionar a tela de termos do questionário) → coberto pelo card **B8** (privacidade/TCLE, P14).

---

## B. Cards a criar — ordenados por prioridade

Legenda de dependência: **livre** = dá pra fazer já; **gated** = espera algo (beta/Cap anterior).

### B1. [TCC] Cap 6.5 Motor do fut + 6.6 Tempo real — livre · PRIORIDADE MÁXIMA

**Objetivo:** escrever as duas seções centrais e originais do Cap 6 (Desenvolvimento): o motor
do fut e a camada de tempo real. É o diferencial do TCC2 (nenhum dos 3 TCCs de referência tem
equivalente) e o insumo está 100% pronto — backend inteiro na main, specs sincronizadas.

**Depende de:** nada. Backend do match, presença, cron e realtime todos mergeados.

**Fonte (insumo):** `fut_in_app_context.md` §3-§6 (fluxo), §7 (modelo/estados), §8 (real-time),
§9 (cenários A-M); `api-rotas-contratos.md` (contratos §7-§10); código dos módulos
`presence`, `match`, `realtime`, `scheduler`, `common/fut-lifecycle`.

**Padrão de texto a seguir:** mesmo registro descritivo e impessoal das seções 6.1-6.4 já
escritas em `desenvolvimento.tex` (ler antes para calibrar tom). Descrever o sistema como
**pronto e em funcionamento** (diretriz: MVP completo, app recém-lançado). Referenciar o
diagrama de componentes (`fig:c4-componentes`) e o modelo de dados (Cap 5). O comentário no
fim de `desenvolvimento.tex` já traz o mapa de prontidão de cada seção.

**Escopo:**
- **6.5 Motor do fut:** máquinas de estado (fut: agendado→em_andamento→encerrado/cancelado;
  participação: confirmado→chegou→saiu; partida: em_andamento→pausada→encerrada_provisoriamente
  →encerrada / em_penaltis); fila posicional em `lista` que nunca resequencia (posição MAX+1);
  formação e rotação unificada de times (perdedor inteiro pro fim, casos "0 na fila" e times
  reduzidos); último lance, `encerrada_provisoriamente` com janela de 15s, pênaltis; override
  do criador ([Ajustar], trocar jogador, forçar encerramento); snapshot de regras por partida;
  sucessão de criador. Fut não-gerenciado (coleta passiva + ativação de gerenciamento).
- **6.6 Tempo real:** gateway Socket.IO com salas por fut e auth no handshake; eventos de
  domínio pós-commit com `seq` monotônico (idempotência + resync); política "só quem chegou
  entra na sala"; fallback REST/polling; cache in-memory TTL 2s + ETag; cronômetro client-side;
  cron de backstop (transições sem leitor + lembretes + inatividade + receipts de push).

**Critério de aceite:** duas seções escritas no padrão; estende o parágrafo de organização do
Cap 6 (topo de `desenvolvimento.tex`) para anunciá-las; compila; varredura de consistência
(papéis, "fut"/"partida", stack) limpa.

---

### B2. [TCC] P20 — Modelo de dados (dicionário regenerado + MER Chen + §Modelo de dados) — livre · ALTA

**Objetivo:** fechar o P20: regenerar o dicionário de dados do schema **completo atual**,
desenhar o MER conceitual (Chen) e o lógico, e reescrever o §Modelo de dados do Cap 5
(conceitual antes, depois lógico, nomeados corretamente).

**Depende de:** nada (o schema está estável na main). O MER Chen é desenho manual do Miguel no
brModelo; a IA gera dicionário e specs.

**Fonte:** `BoraFut-Backend/prisma/schema.prisma` (atual); `specs-diagramas-banca.md` §2
(spec do MER, já com REGISTRO DE NOTIFICAÇÃO e a decisão de incluir as tabelas operacionais);
`fut_in_app_context.md` §7.

**Padrão de texto:** o dicionário segue o formato do apêndice atual (`apendice:dicionario`):
entidade, atributo, tipo, obrigatoriedade, descrição, restrições. O §Modelo de dados do Cap 5
mantém o tom atual, apresentando conceitual→lógico.

**Escopo:**
- Regenerar o dicionário do apêndice a partir do schema atual — inclui as tabelas/colunas novas
  desde a baseline de 15/06: `fut_notificacao`, `push_receipt`, `jogador.expo_push_token`,
  `fut.event_seq`/`motivo_sistema`/`inatividade_reiniciada_em`, `participacao.removido_por_admin`,
  `log_partida.time`/`referencia_log_id`. Sem `tipo_acao='saida_jogador'` (removido).
- MER conceitual (Chen) no brModelo: entidades fracas REGISTRO DE CHEGADA e REGISTRO DE
  NOTIFICAÇÃO; `push_receipt` fica a critério do desenho (sem relação de domínio).
- MER lógico atualizado + reescrita do §Modelo de dados (`subsec:modelo-dados`) e conferência
  da §6.3 do Cap 6.

**Critério de aceite:** dicionário reflete o schema atual atributo a atributo; MER conceitual e
lógico inseridos; §Modelo de dados reescrito; nomes "conceitual"/"lógico" corretos; compila.

---

### B3. [TCC] Cap 6.7 Qualidade + 6.8 Decisões e trade-offs (ADRs) — livre · ALTA

**Objetivo:** escrever o fechamento do Cap 6: qualidade de software (testes, validação,
padrões) e 3-5 ADRs resumidas.

**Depende de:** nada. Reforçado se o card **B9** (cenários A-M) rodar antes.

**Fonte:** CI dos dois repos (`.github/workflows/ci.yml`); contagem de testes (backend ~670
unit + 57 e2e; frontend jest-expo); convenções (`executar-task.md`, SQL raw, DTOs); decisões
já registradas nesta série de análises e no `fut_in_app_context.md`.

**Padrão de texto:** tom das seções 6.1-6.4; ADRs curtas em voz ativa (contexto→decisão→
consequências), sem citar ferramentas de gestão (Jira) nem uso de IA.

**Escopo:**
- 6.7: estratégia de testes (unit + e2e + integração), gate de CI, cobertura, validação de DTOs,
  convenções de erro em português, transações serializáveis.
- 6.8: ADRs candidatas — (1) fila nunca resequenciada (posição absoluta); (2) snapshot de regras
  por partida; (3) WebSocket + fallback no MVP em vez de só polling; (4) transições de ciclo de
  vida por função única compartilhada (lazy no read + cron); (5) idempotência de push por ledger.

**Critério de aceite:** 6.7 e 6.8 escritas; parágrafo de organização do Cap 6 atualizado;
compila; sem menção a IA/Jira.

---

### B4. [TCC] Cap 7.1-7.3 — Método de avaliação + usabilidade + SUS — livre (usa dados do protótipo) · ALTA-MÉDIA

**Objetivo:** escrever a primeira metade do Cap 7 (Resultados e Validação): método de avaliação,
testes de usabilidade sobre o protótipo de alta fidelidade e resultado do SUS.

**Depende de:** dados das entrevistas de usabilidade + SUS (sobre o **protótipo**, não o app —
por isso independe do frontend do Arthur). Precisa do TCLE dos testes (card B8) referenciado.

**Fonte:** roteiro de entrevista, respostas do SUS (n>10 alvo), perfil dos participantes; modelo
estrutural: miaAjuda caps 5-6 e vandor 4.4-4.5 (ver plano mestre §5.1).

**Padrão de texto:** ser explícito que o objeto avaliado foi o **protótipo de alta fidelidade**
(não o app final); linguagem qualificada (sem "garante"); interpretar o SUS na escala padrão.

**Escopo:** 7.1 método (roteiro, participantes, TCLE, instrumentos); 7.2 testes de usabilidade
(achados → iterações de design); 7.3 SUS (score, interpretação).

**Critério de aceite:** 7.1-7.3 escritas com dados reais; TCLE referenciado (apêndice do B8);
compila.

---

### B5. [DEV/backend] Instrumentação de divergência e desempenho — livre · BAIXA (por último)

**Mudança de escopo em 23/07/2026.** O card nasceu como "instrumentação de funil do beta":
tabela `evento_produto` + marcos de ativação, presença que vira chegada, tempo até dez
confirmados, retenção semanal e convites por fut, para responder as três perguntas de PMF
do `01-avaliacao-produto.md` §1.3.

**Por que mudou:** o beta será **fechado**, com participantes recrutados a dedo (pessoas de
confiança e interessadas no projeto, com os pesquisadores presentes). Sob viés de seleção,
efeito Hawthorne e *demand characteristics*, toda métrica de **escolha voluntária** deixa de
medir o que promete: a ativação acontece porque os pesquisadores estão ao lado; a presença
vira chegada porque ninguém convidado vai furar; a lista enche rápido porque as pessoas foram
escolhidas; o gerenciamento ao vivo sobrevive porque os participantes estão ali para testar; e
retenção semanal não existe com acesso restrito e prazo curto. Medir isso e reportar como
resultado seria inflar número, e é o tipo de coisa que a banca desmonta.

Essas perguntas continuam válidas, mas só o **lançamento aberto** as responde. No texto, elas
viram limitação declarada no Cap 7 e trabalho futuro no Cap 8.

**Objetivo (novo):** registrar o que **não depende da vontade do participante**, em três
famílias:

- **A. Verificação dos RNFs em campo.** A monografia afirma números no Cap 5 e o beta é o
  lugar de conferi-los: RNF02 (operações principais em até 3s em rede móvel real), RNF03
  (propagação de presença e placar em poucos segundos, medida entre o registro do gol e a
  atualização nos outros aparelhos) e RNF04 (ações centrais em três a cinco toques).
  Participante motivado não altera a latência da rede nem o relógio.
- **B. Robustez operacional.** Quedas e reconexões de WebSocket na quadra, divergência de
  estado entre aparelhos, requisições com erro, disparo correto do cron de backstop.
- **C. Aderência do modelo de domínio à realidade social.** É a família mais valiosa, porque
  toca a contribuição original do trabalho (as regras sociais do fut codificadas em software).
  A pergunta é "quando o app decidiu, o grupo aceitou a decisão?". Grupo motivado reclama
  **mais** quando a regra sai errada, então esse dado se beneficia do viés em vez de sofrer
  com ele. Cada override é evidência de divergência entre a modelagem e o comportamento real.

**Já existe no código (não refazer):** `log_partida` grava `gol_desfeito`, `pausa`,
`retomada`, `edicao_regras` e `encerramento_partida`; `log_chegada` separa o jogador do ator
(ator diferente do jogador indica que o fluxo self-service não deu conta na hora);
`participacao.removido_por_admin`; `fut.motivo_sistema` distingue encerramento automático de
manual; `partida.tempo_pausado_ms`.

**Escopo (o que falta):**
1. persistir troca e substituição de jogadores no log de partida (novo tipo de ação). Hoje o
   swap emite `match.player_swapped` no tempo real e altera o estado, mas **não grava nada**:
   o override mais informativo de todos é o único que se perde;
2. distinguir encerramento forçado de encerramento por regra (tempo ou gols);
3. carimbo de latência ponta a ponta no evento de tempo real (**depende do cliente**, é o
   aparelho que mede o que o usuário sente; entra em card do Arthur);
4. contadores operacionais de reconexão de WebSocket;
5. consultas de agregação para exportar os números do beta.

**Fora do escopo:** a tabela `evento_produto` de funil, descartada neste momento. A
caracterização da amostra do Cap 7 ("N futs, N participações, N partidas, N gols") sai por
contagem do que já existe, sem instrumentação nova.

**Restrição de prazo:** o que for feito precisa estar no ar **antes do primeiro fut do beta**,
porque esse dado não se recupera depois. Como o beta só começa quando a tela de fut existir no
aplicativo, o gargalo real é o frontend, não este card.

**Critério de aceite:** troca/substituição e encerramento forçado gravados; consultas de
agregação funcionais; `make test` verde; documentado no runbook do beta.

---

### B6. [TCC] Apêndice de protótipos (P09 / fecha KAN-13) — livre · MÉDIA

**Objetivo:** criar o apêndice final "Telas do protótipo" com as telas do Figma legíveis, e
manter no Cap 5 §Prototipação só as 3 telas representativas referenciando o apêndice.

**Depende de:** Miguel re-exportar as telas do Figma (exports em `latex/figuras/prototipos/`).

**Fonte:** protótipo Figma; ponto P09 do plano; bug KAN-13.

**Padrão de texto:** apêndice como último (ordem de apêndices no plano §5.3); grades 2-3 telas
por linha; legendas consistentes.

**Critério de aceite:** apêndice com telas legíveis; Cap 5 §Prototipação referencia o apêndice;
compila; **fecha KAN-13**.

---

### B7. [TCC] Documentar brainwriting (P06 / fecha KAN-9) — livre · MÉDIA

**Objetivo:** documento à parte (confidencial, **não anexado**) do brainwriting: método 6-3-5,
seis categorias, número de cartões e resultados em alto nível — **sem** expor as ideias
sensíveis. Citar no texto e entregar à banca em separado.

**Depende de:** material do Miro (exportar).

**Fonte:** ponto P06/B06 do plano; `metodologia.tex` §Brainwriting (já existe a descrição do
método — este card produz o documento anexo).

**Critério de aceite:** documento produzido (método + categorias + volume, sem ideias); citado
na metodologia; **fecha KAN-9**.

---

### B8. [TCC] Privacidade + TCLE (P14 + KAN-16) — livre · MÉDIA

**Objetivo:** (a) apêndice "Termo de Consentimento Livre e Esclarecido" (o TCLE completo dos
testes de usabilidade — Miguel envia o texto); (b) mencionar na metodologia a tela de
termos/consentimento do questionário de 2025 (fecha KAN-16); (c) opcional: descomentar e
escrever a §Plano de privacidade e segurança da metodologia (hoje comentada em
`metodologia.tex:494`).

**Depende de:** Miguel enviar o texto do TCLE.

**Fonte:** ponto P14/B14 do plano; bug KAN-16; RNF06 (LGPD) já no Cap5.

**Padrão de texto:** apêndice no formato dos demais; citar o TCLE na metodologia (questionário E
entrevistas de usabilidade do Cap 7).

**Critério de aceite:** apêndice TCLE criado e citado; menção à tela de consentimento do
questionário; **fecha KAN-16**.

---

### B9. [DEV/backend] Cenários A-M como suíte de integração nomeada — livre · MÉDIA

**Objetivo:** transformar os 13 cenários do `fut_in_app_context.md` §9 (A-M) em testes de
integração nomeados. Material direto para o Cap 6.7 e 7.5, e rede de proteção do motor do fut.

**Depende de:** nada (backend do match na main).

**Fonte:** `fut_in_app_context.md` §9 (cenários A rotação normal … M conflito de presença).

**Escopo:** um `describe('Cenário X — ...')` por cenário, montando o estado inicial, disparando o
evento e verificando o estado final descrito na spec.

**Critério de aceite:** 13 cenários cobertos e verdes; nomes casando com a spec; CI verde.

---

### B10. [TCC] Cap 7.4-7.6 — Beta + qualidade + discussão — GATED (beta) · MÉDIA (bloqueado)

**Objetivo:** teste piloto em ambiente real (beta fechado, fut real), resultados de qualidade e
discussão (objetivos × evidências, limitações).

**Depende de:** app do Arthur rodando + SES (login externo) + instrumentação (B5). **Gate:** se o
beta escorregar, aplicar o fallback do plano (§9, R2): 7.4 vira relato qualitativo do fut de
teste interno, sem as métricas prometidas.

**Fonte:** dados do beta (funil do B5); plano mestre §5.2.

**Critério de aceite:** 7.4-7.6 escritas com o que houver de dado; se fallback, deixar explícito
que foi teste piloto qualitativo.

---

### B11. [TCC] Cap 8 Conclusão — GATED (Cap 7) · BAIXA (bloqueado)

**Objetivo:** síntese, contribuições (técnica: motor do fut + tempo real documentados; social:
catálogo de quadras públicas), limitações, trabalhos futuros (genéricos: score de presença,
recorrência de futs, expansão, campeonatos).

**Depende de:** Cap 7 fechado. Descomentar o include de `consideracoes.tex` (vira o Cap 8).

**Critério de aceite:** Cap 8 escrito; organização do Cap 1 (D11) atualizada para "oito
capítulos"; compila.

---

### B12. [TCC] Passe final de consistência e ABNT — GATED (texto todo) · BAIXA (por último)

**Objetivo:** o passe global que fecha o texto, feito depois que os capítulos existirem.

**Depende de:** todos os capítulos escritos. É o último card.

**Escopo:** N3 tempo verbal (`grep "será|serão|planejad|prevista"` → presente/pretérito);
NV4 reescrever `resumo.tex`/`abstract.tex` (hoje no tom de proposta TCC1 — "produção de um
protótipo... base para implementação"; deve virar "trabalho desenvolvido + validado +
resultados"); P13 ABNT (itálico em estrangeirismos, iniciais, vírgulas, siglas no 1º uso);
P02 aspas + índice ≤4 níveis; P11 checklist de elogios preservados; P15 versões conferidas;
P16 toda tabela citada; D11 "oito capítulos"; gate P18 (conscientização entra no texto ou vai
pra Trabalhos Futuros); invariante N2 (stack nos 3 lugares).

**Critério de aceite:** PDF completo revisado; checklist do plano §8.4 batido.

---

## C. Cards de dev que JÁ existem no board (não criar; contexto do que fazer)

Estão corretos e pendentes — o que muda é **quando** e **por quem**:

| Card | O quê | Quando / nota |
|---|---|---|
| **KAN-50** | Pós-Fut: Card + Estatísticas [Ambos] | **A parte backend é livre e alta** (destrava §6.1/§6.2 do TCC e o push final do auto-encerramento; dados já coletados nos logs). Fazer o backend (`GET /futs/:id/card.png` + endpoint de stats) já; o frontend espera o Arthur. |
| **KAN-40** | Perfil + Menu [Backend] (`/futs/my`, `DELETE /users/me` LGPD, `GET /version`) | Backend livre; pré-lançamento/lojas. |
| **KAN-51** | Envio real de e-mail (SES) | Gate do beta com usuário externo (decisão: aguardar; plano B = verified identities no sandbox). |
| **KAN-56** | Hardening de produção + borda (proxy) | Antes do lançamento (10/09); idealmente antes do beta. |
| **KAN-45** | S3 privado + CloudFront | Operacional, perto da entrega. |
| **KAN-33/35/38/57** | Frontend (fut CRUD, presença/fila, partida ao vivo, não-gerenciado) | Arthur. KAN-33 inclui o fix do contrato `futsSemanais`. |
| **KAN-41/42/55** | Frontend (perfil/menu, visitante, conscientização) | Arthur; KAN-55 tem gate de freeze. |

---

## Ordem de execução recomendada (o que o Miguel faz sozinho, em paralelo ao Arthur)

**Agora (sem esperar nada):**
1. Limpar o board (Parte A) — fecha 7 cards em 5 minutos.
2. **B1** (Cap 6.5/6.6) — o diferencial do TCC, insumo pronto.
3. **KAN-50 backend** (card pós-fut + stats) — fecha o fluxo e destrava §6.1/§6.2.
4. **B2** (P20: dicionário + MER + §Modelo de dados).

**Em seguida:**
5. **B3** (Cap 6.7/6.8) — ganha do B9 rodar antes.
6. **B9** (cenários A-M) — barato, alimenta 6.7/7.5.
7. **B4** (Cap 7.1-7.3) — com os dados de usabilidade/SUS.

**Por último:** **B5** (instrumentação de divergência e desempenho), reescopado em 23/07.
Ainda precisa preceder o primeiro fut do beta, mas o beta depende do frontend, então há folga.

**Apêndices e pendências independentes (encaixar quando der):**
9. **B6** (protótipos, fecha KAN-13), **B7** (brainwriting, fecha KAN-9), **B8** (TCLE, fecha KAN-16).
10. **KAN-40** (perfil/menu backend), depois **KAN-56** (hardening), **KAN-51** (SES), **KAN-45** (S3) — trilha de lançamento.

**Bloqueados (esperam beta/capítulos):**
11. **B10** (Cap 7.4-7.6), **B11** (Cap 8), **B12** (passe final).
