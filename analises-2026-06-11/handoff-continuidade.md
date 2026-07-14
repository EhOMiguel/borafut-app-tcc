# Handoff de continuidade (escrito em 11/06/2026; ATUALIZADO em 13/07/2026)

> Documento de passagem de bastão. Quem está lendo isto (humano ou IA) assume o trabalho
> do TCC2 + produto BoraFut exatamente do ponto em que parou, com as mesmas regras.
> Leitura obrigatória ANTES de qualquer edição: este arquivo, depois `04-plano-tcc-v2.md`
> (plano mestre, incluindo o Adendo §0 e as Regras §8).
> A seção §8 (abaixo) traz o **estado em 13/07/2026** — em conflito com o corpo antigo,
> vale a §8.

## 8. ESTADO EM 13/07/2026 (auditoria completa — sobrepõe o que estiver desatualizado acima)

### Dev — backend (muito à frente do plano)
Mergeado em `main`: auth + refactor menores (M1/M2/M4), courts/reviews/favoritos,
futs CRUD+share, **schema/papéis (KAN-46)**, **presença/chegada/fila + organizadores +
sucessão de criador**, **tempo real (gateway Socket.IO + cache 2s + ETag + seq)**,
**lifecycle read-path (transições lazy)**, push infra (token/dispatcher), CI verde.
Também na main (PR #26): `feat/gestao-partidas` (**fut ao vivo completo**: iniciar fut,
vamos-lá, gol/gol contra/desfazer 15s, último lance, pênaltis, force-end, rotação
unificada + times reduzidos, swap, regras-snapshot, encerrar fut, pushes).
Em PR pendente: `feat/fut-lifecycle-cron` (cron 5min: transições backstop, escalada de
inatividade T+2h/2h15/2h30 + prorrogação, lembretes 24h/1h/horário, ledger idempotente,
receipts Expo) — correções da review 001 + auditoria APLICADAS em 13/07 (commit
`dcd7735`), branch pushada; falta abrir/mergear a PR.
670 testes unit + 57 e2e verdes. Novo doc de contratos:
`BoraFut-ia/api-rotas-contratos.md (cópias em .ia/shared/)`.

### Dev — frontend (gargalo atual)
Feito: onboarding/auth, mapa+pins, busca, perfil da quadra (abas, reviews, favoritos),
push (token/banner/deep-link), jest-expo (25 specs), CI.
Placeholders: create-fut, profile, settings. **Não existe a feature de futs inteira**
(detalhe do fut, presença, chegada, fila, partida ao vivo, share link, cliente WS —
`socket.io-client` nem está instalado).
**BUG:** `CourtScheduleTab` consome `futsDoDia`/`organizador`; o backend responde
`futsSemanais`/`criador` — agenda da quadra vazia no app.

### Pendências P0 (13/07; decisões do Miguel aplicadas na mesma data)
1. **SES real** (C1 do doc 02) — segue inexistente. DECISÃO: aguardar mais um pouco
   (registrado); é o gate do beta com usuário externo.
2. Contrato `futsSemanais` no frontend — correção já prevista em card.
3. ~~Correções da review do cron~~ FEITAS 13/07 (commit `dcd7735`, branch pushada);
   falta abrir/mergear a PR do `feat/fut-lifecycle-cron`.
4. Frontend do fluxo de fut — previsto em card; caminho crítico do beta e da demo.
5. Sync das specs — FEITO 13/07 (fut_in_app_context.md §3.4/3.8/3.9/4.15.2/5.4/6.3/
   7.5/8.5/9.10 + ponte do contexto §8.2 para `api-rotas-contratos.md (BoraFut-ia / .ia/shared)`), nas 3
   cópias; commit no BoraFut-ia pendente de revisão do Miguel.

### Não implementado em lado nenhum (gates do freeze de 03/08)
Ativação de gerenciamento (§5.3), coleta passiva do não-gerenciado (§5.2 +
elegibilidade cond. 3), card pós-fut + estatísticas (§6.1/§6.2), SES, S3/CloudFront em
produção (KAN-45), hardening HTTP, GET /version + exclusão de conta (LGPD),
aba conscientização (P18).

### Monografia (working tree, 15 commits desde 11/06)
Feito além do plano de 11/06: Cap6 "Desenvolvimento" criado (processo, arquitetura,
fundação, descoberta; seções motor-do-fut/tempo-real corretamente comentadas até o
freeze), C4 contexto+contêineres+componentes, fluxos do Cap5 reescritos (D09),
13 RFs (D10), granularidade 6 níveis (P12), dicionário de dados (P20 parcial),
specs de 11 UCs, US novas (NV1/NV2), observação (P05).
Pendências de texto: ver plano mestre §3/§4 com os status atualizados em 13/07 e o
relatório da auditoria de 13/07 (correções pontuais em Cap3/Cap6).
MER Chen no brModelo continua com o Miguel.

### Specs (BoraFut-ia)
D1-D7 aplicados E COMMITADOS. Contexto atualizado com real-time chegou-only e §8.3.
As defasagens achadas na auditoria foram **sincronizadas em 13/07** (working tree do
BoraFut-ia + cópias dos submodules; commit pendente do Miguel): sucessão de criador
(novo §3.9), decisões do cron (§4.15.2 notas, §7.5, §8.5), e-mail obrigatório no
cadastro manual (§3.4, "email opcional" adiado pós-MVP), elegibilidade exigindo fut
encerrado (§6.3), push final do auto-encerramento marcado como previsto-para-MVP junto
do card pós-fut (§4.15.2/§9.10), e ponte do contexto base §8.2 para o doc de rotas.

## 1. Contexto em uma frase

Monografia de TCC2 do BoraFut (app de futebol amador em quadras públicas do DF, dupla
Miguel + Arthur, FGA-UnB): corrigir os 20 pontos da banca do TCC1, atualizar o texto para
o estado real do projeto e escrever os capítulos novos (6-8), com entrega em agosto,
defesa em setembro e lançamento do app em 10/09.

## 2. O que já foi feito (não refazer)

### Análises e plano (pasta `analises-2026-06-11/`)
- `01-avaliacao-produto.md`: estratégia de produto (concorrente real = WhatsApp; migrar o
  organizador; card pós-fut como motor viral; monetização sequenciada pós-TCC).
- `02-avaliacao-codigo.md`: review dos dois repos. Achado crítico: e-mail era só mock
  (virou card). Pontos menores aprovados para o próximo refactor.
- `03-avaliacao-fluxo-ia.md`: auditoria do pipeline de IA com 8 propostas (D1-D8), TODAS
  aprovadas e D1-D7 APLICADAS (ver §4 abaixo).
- `04-plano-tcc-v2.md`: **PLANO MESTRE**. Mapeia os 20 pontos da banca (P01-P20), as
  divergências texto↔projeto (D*/NV*), a estrutura alvo de 8 capítulos, o cronograma
  S0-S9 e as regras de execução (§8). O Adendo §0 registra as 20 decisões fechadas com o
  Miguel em 11/06. O `plano-tcc-final.md` da raiz é o plano ANTIGO (insumo histórico,
  superado por este).
- `cards-jira.md`: 7 cards prontos que o Miguel está criando no Jira, com ordenação
  definida (ver §5).

### Diagramas (B07b, B20, D06, E05) - PRONTOS exceto MER
- `specs-diagramas-banca.md` (raiz do repo TCC): especificação v2 completa (papéis
  Criador/Organizador, 43 casos de uso, MER conceitual com log_chegada/timestamps/
  snapshots, C4 contêineres e componentes).
- Fontes PlantUML em `latex/figuras/diagramas-src/*.puml` e PNGs RENDERIZADOS E
  VERIFICADOS em `latex/figuras/diagramas-src/render/`: `uc-conta-mapa`,
  `uc-fut-organizacao`, `uc-fut-partida`, `c4-conteineres`, `c4-componentes`.
  Re-render: `java -jar plantuml.jar -tpng -charset UTF-8 -graphvizdot <dot.exe> -o render *.puml`.
- FALTA: MER conceitual (Chen) e lógico atualizado - o MIGUEL desenha no brModelo
  seguindo a seção 2 das specs. Não gerar por IA (decisão).
- A INSERÇÃO dos diagramas nos .tex fica junto com a reescrita do Cap 5 (S3), não antes.

### Monografia (working tree do repo TCC)
Baseline já contém (não refazer): Cap1 com delimitação de escopo, Cap3 reescrito (stack
real + tabela de versões), matriz de mercado no Cap4, RNF reescritos com rastreabilidade
(`tab:rastro-valor`), Figura 9 e refs de tabela corrigidas. Status por ponto: tabela §3 do
plano mestre.

### Pipeline de IA (repo BoraFut-ia) - patches aplicados, commit pendente do Miguel
- `borafut_contexto_prd.md`: sincronizado com o fut_in_app (rotação unificada, overrides
  removidos, rotas `/organizers/`, WebSocket no MVP, regra dos 30 dias, decisão do nearby
  sem bounding box) + Índice de leitura seletiva no topo.
- `fut_in_app_context.md`: Índice de leitura seletiva no topo.
- `commands/`: criar-pull-request com gate de sync de specs; fazer-review agora SALVA
  relatório em `.ia/tasks/prd-x/reviews/NNN-review.md`; executar-task com piso de testes
  frontend; criar-prd e criar-techspec com regra de leitura seletiva; NOVO
  `corrigir-bug.md` (fluxo leve para defeitos).
- `templates/`: NOVO `adr-template.md`.

## 3. Regras invioláveis (resumo; íntegra no plano mestre §8)

1. Escrever a nova verdade, nunca narrar a mudança ("era X, virou Y" é proibido).
2. ZERO menção a uso de IA, em qualquer artefato da monografia.
3. Não referenciar a apresentação/feedback da banca no texto.
4. O app NÃO reserva quadra: informa ocupação em melhor esforço. Banir "reserva/garantia".
5. Qualificar palavras fortes (nada de "garantir", "equitativo", "assegurar").
6. Vocabulário do fut: **Criador** (autoridade máxima) e **Organizador** (promovido).
7. Tipografia: aspas LaTeX consistentes; índice <= 4 níveis; SEM travessão (preferência do
   Miguel); sem subseção de 1 parágrafo.
8. Regra de freeze (03/08): o texto descreve tudo como implementado; o que não estiver
   implementado até o freeze sai do texto ou vira trabalho futuro.
9. Invariante N2: stack descrita em 3 lugares (Cap3, Cap4 §arquitetura, Cap5 §C4); mudou
   um, varre os três.
10. NÃO reaplicar trocas que o Miguel reverteu (comprovada/alarmantes/através de, Cap1) e
    NÃO remover o Flutter do Cap3 (exceção aprovada).
11. Commits: sem trailer de coautoria de IA. A IA nunca commita (staged + sugestão).
12. Hierarquia de verdade: fut_in_app_context.md > código > borafut_contexto_prd.md >
    monografia. Conflito novo: registrar em "Divergências encontradas", não decidir só.
13. **Disciplina de consistência (CRÍTICA):** o texto é editado em partes; a cada alteração
    que toque um conceito recorrente (granularidade do backlog = 6 níveis; papéis
    Criador/Organizador; nomes dos pilares; app não reserva; stack; vocabulário fut/partida),
    fazer `grep` do termo em TODOS os `.tex` de `latex/editaveis/` (inclusive `resumo.tex` e
    `abstract.tex`) e reconciliar antes de fechar. Checklist completo de invariantes no plano
    mestre §8.4. Pendência conhecida: §Fluxos principais do Cap5 ainda em vocabulário/conteúdo
    antigos, a reconciliar no D09 (S3).

## 4. Como trabalhar com qualidade (instruções para a IA que assume)

- Toda sessão: reler `04-plano-tcc-v2.md` (Adendo §0 + §8) antes de editar qualquer coisa.
- Nas reescritas pesadas (Cap 5 fluxos, Cap 6.5/6.6, specs textuais de casos de uso): o
  insumo é o `fut_in_app_context.md`; usar o Índice de leitura seletiva e LISTAR na saída
  as seções lidas (mecanismo de auditoria do Miguel).
- Cada entrega de texto: citar arquivo:linha do que alterou e o ponto do plano (P*/D*/NV*)
  que fecha.
- Compilar o LaTeX (`make` em `latex/`) antes e depois de mexer; sem suporte a emoji.
- Em dúvida de produto, perguntar ao Miguel; em dúvida de regra do fut, a resposta está no
  fut_in_app (provavelmente nos cenários §9).

## 5. Pendências do MIGUEL (independem da IA)

1. Commitar o BoraFut-ia (patches D1-D7) - mudanças já no working tree.
2. Criar os 7 cards no Jira na ordenação definida (está no fim de `cards-jira.md`? NÃO:
   a ordenação foi passada no chat; resumo: KAN-46 → SES → refactor-menores → CI → KAN-34
   → jest-expo → KAN-35 → KAN-47 → **KAN-43 movido para antes do KAN-48** → KAN-37 →
   KAN-38 → KAN-48 → instrumentação → KAN-49 → KAN-50 → conscientização → hardening →
   KAN-40/41/42 → KAN-45).
3. Desenhar MER conceitual + lógico no brModelo (specs seção 2).
4. Exportar material do Miro (observação + brainwriting) OU autorizar integração.
5. Enviar o texto do TCLE (início do Google Forms) quando a S2 começar.
6. Confirmar datas oficiais de entrega e defesa quando saírem (desloca o freeze).

## 6. Fila de execução (de onde continuar)

**Agora (S1, semana de 15/06) - monografia, pode começar já:**
1. P01: 1 frase no Cap1 (núcleo gratuito permanente; monetização futura nunca toca o fluxo
   principal) + varrer Cap4/Cap5 por menções a pago/premium.
2. P03: parágrafo no Cap5 §Descrição ligando pins/agenda ao mecanismo de evitar conflito
   (ver ocupação antes de se deslocar).
3. P17: grep `reserv|agend|garant` em todos os .tex; ajustar linguagem; parágrafo curto
   sobre o desafio de catalogação no Cap5.
4. P19: grep `garant|assegur|equitativ|igualitár`; qualificar.
5. P04: revisão fina das células ◐ da matriz de mercado (com o Miguel).
6. P05/P06: estruturar os 2 documentos da banca quando o material do Miro chegar
   (observação: roteiro+achados; brainwriting: método 6-3-5+categorias+volume, SEM expor
   ideias futuras).

**Depois (S2):** P12 (backlog 6 níveis: Tema→Épico→Feature→US→Tarefa→Subtarefa, Temas =
pilares da proposta de valor, com citações Cohn/SAFe/Sommerville) + NV2 (US novas no
apêndice: pênaltis, não-gerenciado, card, push, UC22/25/26) + P14 (apêndice TCLE) +
dicionário de dados (gerar do schema pós-KAN-46).

**Depois (S3):** reescrita pesada do Cap5 (D07 papéis, D09 fluxos completos, D10 RFs ~10,
D10b descrição) + apêndice F (specs textuais de 8-12 casos de uso com episódios: fluxo
principal numerado, alternativos, exceções - modelo vandor Apêndice E) + P09 (apêndice de
protótipos) + inserir os 5 diagramas prontos + texto novo do §Modelo de dados (P20).

**S4 em diante:** Caps 6-8 conforme cronograma §7 do plano mestre (Cap6 Desenvolvimento
com seções Motor do fut e Tempo real; Cap7 Resultados com usabilidade+SUS+beta; Cap8
Conclusão), passe global na S8 (tempo verbal, ABNT, elogios preservados, resumo novo).

**Dev (paralelo, ordem do board):** KAN-46 primeiro; SES tem prazo externo AWS; beta
fechado ~25/07 com instrumentação pronta; submissão às lojas até ~29/08.

## 7. Divergências conhecidas (não tratar como erro)

- RNF02 menciona limitação por área visível; o código carrega todas as quadras: DECISÃO
  CONSCIENTE do Miguel (texto fica, bounding box é pós-MVP, documentado no contexto).
- `notas-tcc2.md` no frontend é embrião histórico desatualizado; ignorar.
- Jira KAN-27/28 constam concluídos sem o bounding box do escopo: coberto pela decisão acima.
