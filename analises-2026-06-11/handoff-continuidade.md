# Handoff de continuidade (escrito em 11/06/2026; ATUALIZADO em 23/07/2026)

> Documento de passagem de bastão. Quem está lendo isto (humano ou IA) assume o trabalho
> do TCC2 + produto BoraFut exatamente do ponto em que parou, com as mesmas regras.
> Leitura obrigatória ANTES de qualquer edição: este arquivo, depois `04-plano-tcc-v2.md`
> (plano mestre, incluindo o Adendo §0 e as Regras §8).
> A seção §8 (abaixo) traz o **estado em 23/07/2026** — em conflito com o corpo antigo,
> vale a §8.

## 8. ESTADO EM 23/07/2026 (sobrepõe o que estiver desatualizado acima)

### Dev — backend (fluxo do fut fechado)
Tudo na `main`: auth + refactor menores (M1/M2/M4), courts/reviews/favoritos, futs
CRUD+share, schema/papéis (KAN-46), presença/chegada/fila + organizadores + sucessão de
criador, tempo real (gateway Socket.IO + cache 2s + ETag + seq), lifecycle read-path,
push infra, **fut ao vivo completo** (PR #26: iniciar fut, vamos-lá, gol/gol contra/
desfazer 15s, último lance, pênaltis, force-end, rotação unificada + times reduzidos,
swap, regras-snapshot, encerrar fut, pushes), **cron de backstop** (PR #28: transições,
escalada de inatividade, lembretes, ledger idempotente, receipts Expo) e **fut
não-gerenciado** (PR #31: presença/chegada informal, stepper de gols declarados,
ativação "Quem está aqui", escalada de inatividade unificada em ciclos de 1h).

Também na main (PR #32, B9): suíte de integração contra **Postgres real** (banco de teste
dedicado, truncate entre casos) e o **fix F1 de auth**, achado por ela: o contador de
tentativas do verify-code era perdido no rollback da transação, anulando o lockout de
força bruta. 698 testes unitários + 71 e2e verdes contra banco real.

**Não há trabalho de backend fora da `main`.**

Doc de contratos: `BoraFut-ia/api-rotas-contratos.md` (cópias em `.ia/shared/`).

### Dev — frontend (gargalo atual, sem mudança relevante desde 13/07)
Feito: onboarding/auth, mapa+pins, busca, perfil da quadra (abas, reviews, favoritos),
push (token/banner/deep-link), jest-expo, CI, landing page.
Última atividade do Arthur: 18/07, na branch `feat/fut-crud-share` (só "destacar futs em
andamento na aba da quadra" + merge da main). **A feature de futs continua inexistente**
(detalhe do fut, presença, chegada, fila, partida ao vivo, share link, cliente WS —
`socket.io-client` nem está instalado). Isso é o caminho crítico do beta e da demo.
**BUG aberto:** `CourtScheduleTab` consome `futsDoDia`/`organizador`; o backend responde
`futsSemanais`/`criador` — agenda da quadra vazia no app.

### Pendências P0
1. **SES real** — segue inexistente. DECISÃO do Miguel: aguardar; é o gate do beta com
   usuário externo.
2. **Frontend do fluxo de fut** — caminho crítico; depende do Arthur.
3. Contrato `futsSemanais` no frontend — previsto em card.
4. **Instrumentação (B5)** — reescopada em 23/07 e movida para o fim da fila. O funil de
   adoção foi descartado: o beta será fechado, com participantes recrutados, e sob viés de
   seleção nenhuma métrica de escolha voluntária mede o que promete. Ficam a verificação
   dos RNFs em campo, a robustez operacional e a divergência entre a decisão automática do
   app e a decisão social do grupo. Ainda precisa preceder o primeiro fut do beta.

### Não implementado em lado nenhum (gates do freeze de 03/08)
Card pós-fut + estatísticas (§6.1/§6.2 da monografia dependem dele), SES, S3/CloudFront
em produção (KAN-45), hardening HTTP, `GET /version` + exclusão de conta (LGPD), aba
conscientização (P18), instrumentação do beta (B5, reescopada).
(Ativação de gerenciamento e coleta passiva do não-gerenciado saíram desta lista: foram
implementadas no PR #31.)

### Monografia
Capítulo 6 "Desenvolvimento" **completo**: processo, arquitetura, fundação, descoberta,
§6.5 motor do fut, §6.6 tempo real, §6.7 qualidade (com os testes de integração contra
Postgres real) e §6.8 decisões e trade-offs em cinco ADRs. Também prontos: C4
contexto/contêineres/componentes, fluxos do Cap5 (D09), 13 RFs (D10), granularidade
6 níveis (P12), specs de 11 UCs, US novas (NV1/NV2), observação (P05).

**Modelo de dados (P20) fechado:** dicionário de dados regenerado do `schema.prisma`,
MER conceitual em PlantUML `@startchen` (`mer-conceitual.puml`, com entidades fracas,
relacionamentos identificadores e atributos compostos, multivalorados e derivados),
modelo lógico em DBML desenhado no dbdiagram (`modelo-logico.dbml` → `render/
modelo-logico.png`, 22 tabelas) e a §Modelo de dados reescrita com a distinção
conceitual/lógico (Chen, Elmasri). Diagramas corrigidos após as mudanças de
implementação: UC27 passou a ser do Usuário Registrado; o C4 de componentes ganhou as
setas app→Presence e app→Match.

**B8 feito (23/07):** apêndice do TCLE transcrito do formulário aplicado, §Plano de
privacidade e segurança escrita na metodologia (dados do app e dados de pesquisa,
incluindo a exclusão de conta na tela de perfil, que é compromisso de implementação antes
da defesa) e menção à tela de termos do questionário. Fecha o KAN-16.

**B7 feito (30/07):** registro completo da dinâmica de brainwriting (janeiro de 2024, sete
participantes, 41 cartões consolidados em 27 temas, com a ponte de cada tema até o MVP) em
`BoraFut-ia/documentos-banca/brainwriting-registro.pdf`. O documento vive no repo de IA, que
é **privado**, porque `borafut-app-tcc` é **público** e o registro contém propostas de
monetização e parcerias não publicadas. Subseção *Brainwriting* da metodologia reescrita.
Fecha o KAN-9. Achado: dos 47 itens do quadro do Miro, 6 são histórias de usuário
acrescentadas depois, na construção do backlog; a dinâmica produziu 41.

Pendente de texto: **B6** (apêndice de protótipos), **B4**
(Cap 7.1-7.3, travado por dado: não existe roteiro de entrevista nem resposta de SUS no
repositório, é coleta a fazer) e os bloqueados B10-B12. O Miguel ainda não compilou o PDF
com o Cap 6 e o modelo de dados novos.

### Specs (BoraFut-ia)
Sincronizadas e commitadas: sucessão de criador (§3.9), decisões do cron (§4.15.2, §7.5,
§8.5), e-mail obrigatório no cadastro manual (§3.4), elegibilidade exigindo fut encerrado
(§6.3), ponte do contexto §8.2 para o doc de rotas, e as rotas/modelo de dados do fut
não-gerenciado. Repositório `BoraFut-ia` em dia com o `origin/main`, submódulos dos dois
repos de código apontando para o commit atual. O gate de sincronização foi endurecido:
todo plano de tasks termina obrigatoriamente com a task de sincronização dos documentos
de contexto.

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
