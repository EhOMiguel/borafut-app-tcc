# 01 - Avaliação de Produto: BoraFut

> Avaliação da proposta de valor, encaixe de mercado, riscos e monetização do BoraFut.
> **Frente analisada:** o projeto BoraFut que continua após o TCC (negócio real). O recorte
> acadêmico (o que entra na monografia) é tratado no `04-plano-tcc-v2.md`; aqui a análise é
> de produto, sem as restrições de linguagem do texto da banca.
> Fontes primárias: `BoraFut-ia/borafut_contexto_prd.md`, `BoraFut-ia/fut_in_app_context.md`,
> análise de mercado e questionário da monografia (`latex/editaveis/metodologia.tex`,
> `latex/editaveis/apendices.tex`). Código usado apenas como checagem de viabilidade.

> **ATUALIZAÇÃO 13/07/2026:** a análise de produto abaixo continua válida (mercado,
> riscos, monetização não mudaram). O que mudou de fato: (1) o **motor do fut e o tempo
> real estão prontos no backend** (a aposta A1 depende agora do frontend, não do motor);
> (2) o **card pós-fut (A2) segue não implementado** — decidir se entra antes do beta ou
> vira pós-MVP explícito; (3) a **A3 (beta de julho instrumentado) está em risco**: SES
> não existe (ninguém externo loga) e a instrumentação de funil (card 6) não apareceu no
> código; sem esses dois, o beta não responde as 3 perguntas de PMF do §1.3; (4) o mapa
> público (A4) está entregue no app. Prioridade de produto da quinzena: SES + frontend do
> fluxo de fut.

## Sumário executivo

1. **A tese de produto é boa e tem um recorte raro**: nenhum concorrente combina quadra
   pública + descoberta hiperlocal por mapa + gerenciamento do jogo ao vivo. O BoraFut não
   compete por "reserva de quadra" (mercado do Agendei/Appito) nem por "gestão de grupo
   fechado" (Chega+/Peladeiros): ele ataca o espaço vazio entre os dois, que é exatamente
   onde o futebol do DF acontece.
2. **O concorrente real é o WhatsApp**, não os apps. A estratégia vencedora não é "migrar o
   jogador", é **migrar o organizador**: quem cria o grupo, cobra presença e sofre com furo.
   Um organizador convertido traz 15-25 jogadores de uma vez.
3. **O maior risco não é técnico, é cold start**: mapa sem fut é app morto. As mitigações
   existem no design (modo não-gerenciado como porta de baixa fricção, catálogo de quadras
   com valor próprio, convite por link), mas precisam virar estratégia de lançamento
   deliberada: dominar 3-5 quadras/grupos antes de pensar em volume.
4. **O card pós-fut é o ativo de crescimento mais subestimado do produto**: é o único
   artefato que sai do app e circula no WhatsApp por iniciativa do usuário. Trate-o como
   canal de aquisição (e, depois, como inventário de patrocínio), não como feature cosmética.
5. **Monetização**: o núcleo (organizar/informar) deve ser gratuito para sempre, como já
   decidido. O caminho para dois fundadores viverem do app é sequencial: (1) PMF e escala
   local sem receita; (2) campeonatos como primeira receita; (3) assinatura premium de
   organizador/grupo; (4) parcerias B2B locais (patrocínio de card e quadra). Estimativa
   honesta: app social local não sustenta dois salários antes de 18-24 meses de tração.
6. **Cinco apostas priorizadas**: (A1) ferramenta do organizador impecável; (A2) card
   pós-fut como motor viral; (A3) beta fechado de julho com funil instrumentado; (A4) mapa
   público como utilidade standalone; (A5) campeonatos como cunha de monetização pós-TCC.

---

## 1. Proposta de valor e product-market fit

### 1.1 O problema (validado)

A dor é real e está bem evidenciada pelos dados próprios do projeto (questionário com 60+
respondentes, `metodologia.tex` §Questionário):

- 98,4% organizam jogos pelo WhatsApp; não existe ferramenta especializada em uso.
- 64,1% relatam que jogos deixam de acontecer ocasionalmente ou com frequência.
- Motivações declaradas: "juntar a galera / completar time" (84,4%), "marcar sem dor de
  cabeça" (73,4%), "achar quadra e horário" (62,5%), "evitar furos" (53,1%).
- 53,1% jogam em grupos com mais de 10 pessoas: coordenação coletiva é o gargalo.

O problema tem três camadas, e o BoraFut é o único posicionado nas três:

| Camada | Dor | Quem atende hoje |
|---|---|---|
| Descoberta | "Onde tem quadra livre / fut rolando perto de mim?" | Ninguém (boca a boca) |
| Coordenação | "Quem vai? Vai ter rotação? Que horas?" | WhatsApp (mal) |
| Operação do jogo | "Quem chegou primeiro? Quem sai? Quem ganhou?" | Ninguém (discussão na quadra) |

### 1.2 O insight diferenciador

A camada 3 (operação do jogo ao vivo: fila por chegada, rotação automática, último lance,
pênaltis, override) especificada no `fut_in_app_context.md` é **conhecimento de domínio que
nenhum concorrente codificou**. As regras sociais do fut de quadra pública (perdedor sai,
ordem de chegada vale, "último lance", a discussão sobre quem entra) são exatamente o que
gera conflito presencial; transformá-las em software neutro é a proposta de valor mais
defensável do produto. É difícil de copiar porque não é uma feature, é uma modelagem fina
de comportamento social (38 decisões-chave do §11 do fut_in_app_context).

O **modo não-gerenciado** é a segunda decisão de produto mais inteligente do conjunto:
reconhece que nem todo grupo quer o app mandando no jogo e cria um degrau de adoção
(presença + descoberta apenas), com transição unidirecional para o gerenciado. Isso reduz a
barreira de entrada sem bifurcar o produto.

### 1.3 Estado do PMF

PMF ainda é hipótese, não fato: o produto não rodou com usuários reais em quadra. O que
existe de evidência é demanda declarada (questionário) e demanda análoga (downloads dos
concorrentes). As métricas certas já estão definidas no PRD (§3: taxa de futs criados que
iniciam, presença → chegada, tempo até 10 confirmados, retenção semanal, convites por fut).
O que falta é o que o beta de julho deve responder:

- **Pergunta 1 (ativação):** um organizador real consegue criar fut, encher a lista e
  iniciar sem ajuda de vocês?
- **Pergunta 2 (operação):** o gerenciamento ao vivo sobrevive a um fut real (celular no
  banco, suor, pressa), ou o pessoal abandona o registro no segundo jogo?
- **Pergunta 3 (recorrência):** o mesmo grupo volta a usar no fut seguinte sem empurrão?

Se as três respostas forem sim com 2-3 grupos, há PMF local; escala vira problema de
distribuição, não de produto.

---

## 2. Concorrentes e diferenciais

Base verificada: análise de mercado da monografia (`metodologia.tex` §Análise de mercado,
matriz `tab:comparativo_mercado`).

| Solução | Força | Fraqueza explorável pelo BoraFut |
|---|---|---|
| WhatsApp (baseline) | Universal, gratuito, zero atrito | Sem descoberta, sem quadra, confirmação manual, informação se perde |
| Chega+ (500k+) | Gestão de grupo madura (sorteio, stats, fila) | Grupo fechado; sem mapa/quadra pública; reclamações de bugs e plano pago caro |
| Appito (1M+) | Escala, gamificação, jogos públicos | Foco migrou para arenas próprias/reserva (SP); não atende quadra pública gratuita |
| PELADEIROS / FINTTA (100k+) | Gestão de grupo privado | Mesmo recorte do Chega+, menor alcance |
| Agendei Quadras | Marketplace de reserva multiesporte | Modelo depende de quadra paga; ignora o jogo espontâneo |

**Posicionamento BoraFut:** "o app do futebol que já acontece nas quadras públicas". Os
concorrentes monetizam a infraestrutura (reserva) ou o grupo (assinatura); o BoraFut
organiza o espaço público e o jogo em si. A matriz da monografia captura isso bem: é o
único com ✓ em descoberta por localização + quadra pública + recorte DF + gratuito.

**Riscos competitivos (inferidos):**

1. **Appito ou Chega+ adicionarem mapa de quadras públicas.** Probabilidade baixa no curto
   prazo (incentivo deles é monetizar reserva/assinatura; quadra pública não gera receita
   direta), mas a janela existe. Defesa: velocidade no DF + profundidade do motor de jogo +
   catálogo de quadras curado (dado proprietário difícil de replicar de fora).
2. **Inércia do WhatsApp.** O grupo de zap não some: a estratégia correta é coexistir
   (convite por link circula NO WhatsApp; card pós-fut volta PRO WhatsApp), nunca exigir
   abandono. O produto já entendeu isso; manter como princípio.
3. **Cópia local de baixo custo.** Um clone de mapa é fácil; o catálogo qualificado de
   quadras (32 hoje, com fotos, piso, iluminação) e o grafo social dos grupos não são.

---

## 3. Riscos de adoção, retenção e engajamento

Ordenados por severidade × probabilidade. Mitigações marcadas como [já no design] quando o
spec atual cobre, [a fazer] quando exigem ação nova.

### R1. Cold start / mapa vazio (crítico)
Mapa sem fut válido = pins cinza = "app morto" na primeira impressão.
- [já no design] Catálogo de quadras com fotos/atributos tem valor mesmo sem futs; modo
  visitante mostra isso sem exigir conta.
- [a fazer] Estratégia de lançamento "bairro a bairro": dominar Asa Norte (onde o seed de 32
  quadras já existe) com 3-5 grupos âncora antes de divulgar mais amplamente. Densidade
  local > volume total.
- [a fazer] Recrutar organizadores âncora e semear os futs da primeira quinzena
  manualmente. Base real confirmada em 11/06: 4-5 grupos próprios (a Copa na Praça,
  citada no PM Canvas do TCC1, NÃO é mais parceira - premissa corrigida).

### R2. Fricção do gerenciamento ao vivo (alto)
O fluxo de partida exige alguém com celular na mão registrando gol. Em quadra, isso compete
com jogar.
- [já no design] Próximo time da fila registra (eles estão parados esperando); override
  acessível; pênaltis e último lance modelados.
- [a fazer] Tratar o beta de julho como teste desta hipótese especificamente: medir % de
  partidas com placar registrado vs partidas jogadas. Se < 50%, simplificar (ex.: registrar
  só vencedor, sem autor do gol) antes do lançamento.
- [a fazer] O card pós-fut é a recompensa que justifica o esforço de registrar: deixar isso
  visível durante o fut ("registre para sair no card").

### R3. Cascata de cancelamento / lista fraca (alto)
Já identificado no próprio spec (`fut_in_app_context.md` §10.2): lista com 10 vira 8, vira
6, fut morre.
- [já no design - parcial] Push de lembrete 24h/1h.
- [a fazer pós-MVP, mas cedo] Sinais sociais anti-cancelamento ("se você sair, sobram 9"),
  visibilidade de "rotação garantida". Priorizar logo após o beta se o dado confirmar.

### R4. Retenção semanal (alto)
Fut é hábito semanal; o app precisa entrar no ritual sem virar obrigação.
- [já no design] Push de lembrete, estatísticas pessoais inferíveis, histórico.
- [a fazer] O gatilho de retenção mais forte é o fut recorrente (mesmo grupo, mesma quadra,
  mesmo horário). Hoje não há conceito de recorrência no modelo (cada fut é criado do
  zero). Avaliar "fut recorrente / grupo" como primeira grande feature pós-MVP: reduz
  fricção do organizador (não recriar toda semana) e cria âncora de retenção.

### R5. Dependência do criador (médio)
Se o criador não vai, o fut trava (só ele inicia; encerramento automático mitiga).
- [já no design] Promoção de organizadores; cron de backstop para encerramento; fut nunca
  iniciado cancela em T+2h.
- [a fazer] Incentivar promoção de organizador no onboarding do criador ("promova um vice").

### R6. Confiança e segurança social (médio)
Futs abertos juntam desconhecidos em espaço público; 71,9% da amostra é masculina, e o
produto pode herdar essa assimetria.
- [já no design - parcial] log_chegada/gps_validado preparam score de credibilidade.
- [a fazer] Pós-MVP: perfil com histórico de participação visível, denúncia/bloqueio básico
  antes de escalar futs 100% abertos. Para o público feminino, futs "do grupo" (fechados ao
  link) já atendem; comunicar isso.

### R7. Custos de plataforma (médio, cresce com sucesso)
Google Maps cobra por tile/geocoding acima do crédito mensal; push e SES são baratos mas
não zero.
- [a fazer] Monitorar custo por MAU desde o beta; cache agressivo de tiles é limitado por
  ToS, mas bounding box + debounce (já no código do mapa) controlam chamadas. Ter plano B
  (MapLibre/OSM) documentado caso o custo exploda. (Checagem de viabilidade no código:
  `GET /courts/nearby` por bounding box já evita geocoding recorrente; ok.)

### R8. Sazonalidade e clima (baixo)
Chuva/férias derrubam métricas semanais; não confundir com churn.
- [a fazer] Olhar métricas por coorte mensal, não semana isolada.

---

## 4. Monetização (projeto pós-TCC)

Premissa fixa (decisão sua, alinhada com a banca): **organizar, descobrir e informar nunca
será cobrado**. Isso não é só posicionamento ético: é defesa competitiva (mata a objeção
"vão privatizar a quadra pública") e condição de crescimento (a base precisa ser grande
para qualquer modelo abaixo funcionar).

### 4.1 Modelos avaliados

| # | Modelo | Mecânica | Potencial | Risco/observação |
|---|---|---|---|---|
| M1 | Campeonatos | Organização de copas entre grupos/quadras; taxa de inscrição por time ou serviço premium do organizador do campeonato | Alto e alinhado à cultura (copa de bairro é instituição) | Exige operação (arbitragem de conflito, tabela); pagamento dentro do app traz obrigações (PSP, reembolso) |
| M2 | Premium do organizador/grupo | Assinatura mensal: estatísticas avançadas, histórico ilimitado, card personalizado, recorrência, múltiplos organizadores fixos | Médio; recorrente | Só funciona com base ativa; nada do fluxo core pode cair atrás do paywall |
| M3 | Patrocínio local / card patrocinado | Marca local (loja de material esportivo, açaí, academia) aparece no card pós-fut e no perfil de quadras da região | Médio; CPMs locais baixos, mas venda direta a comércio de bairro é viável | O card é inventário orgânico que já circula no WhatsApp; começar manual (vendas diretas) |
| M4 | B2B quadras privadas/arenas | Listagem premium de quadras pagas no mapa, agenda integrada | Médio-alto | Cuidado: é o território do Agendei/Appito; entrar só quando o lado "público" estiver dominante, como extensão e não pivot |
| M5 | Gamificação paga (skins de card, badges) | Microtransações cosméticas | Baixo-médio | Barato de testar em cima do card; nunca vender vantagem competitiva |
| M6 | Dados agregados para poder público | Relatórios de ocupação/uso de quadras para administrações regionais (visão smart city) | Incerto | Ciclo de venda público é lento; LGPD exige agregação rigorosa; tratar como oportunidade, não plano |

### 4.2 Sequência recomendada

1. **Fase 0 (agora → lançamento, set/2026): receita R$ 0 por decisão.** Instrumentar tudo
   (eventos de funil, custo por usuário). Monetizar antes de PMF mata o dado.
   DECIDIDO (11/06): instrumentação via eventos no próprio Postgres, sem ferramenta
   externa (card 6 de `cards-jira.md`).
2. **Fase 1 (verão 2026/27): campeonato piloto (M1).** O verão é a janela natural (férias,
   copas de bairro). Um campeonato com 16 times × taxa modesta valida disposição a pagar e
   gera o case. Operação manual, app como suporte.
3. **Fase 2 (2027): premium do organizador (M2) + card patrocinado (M3).** Lançar quando
   houver >50 grupos ativos semanais. M3 vende-se porta a porta no comércio do bairro das
   quadras mais ativas.
4. **Fase 3: M4/M5/M6 conforme tração.**

### 4.3 A conta de "viver do app" (estimativa honesta, inferida)

Para dois fundadores em Brasília, assumindo custo de vida + infra ~R$ 9-14 mil/mês por
pessoa como faixa de sobrevivência confortável: alvo de **R$ 25-35 mil/mês de receita
líquida**. Caminhos ilustrativos (números são hipóteses a calibrar, não previsões):

- M2 puro: ~1.700-2.300 assinantes a R$ 14,90/mês. Com conversão otimista de 3-4% de
  organizadores, exige ~50-70 mil usuários ativos. É a rota longa.
- M1+M3 combinados: 4 campeonatos/mês (R$ 2-4 mil líquidos cada) + 15-20 patrocínios locais
  (R$ 500-1.000/mês cada) chegam à faixa com base muito menor (~5-10 mil usuários ativos
  concentrados). **É a rota realista para 2027**, porque monetiza densidade local em vez de
  escala nacional.

Implicação estratégica: **a tese de negócio de curto prazo é densidade no DF, não expansão
nacional**. "Dominar o Brasil" vem depois de o DF pagar as contas; a expansão replicável
(playbook bairro a bairro + catálogo de quadras + organizadores âncora) é o produto real a
ser exportado para outras capitais.

---

## 5. Apostas priorizadas

| # | Aposta | Por quê | Como medir |
|---|---|---|---|
| A1 | **Ferramenta do organizador impecável** (criar fut em <60s, recorrência cedo no pós-MVP, promoção de vice, zero fricção de cadastro manual) | O organizador é o nó da rede; converter 1 = ganhar o grupo inteiro | nº de organizadores com 2+ futs criados; tempo de criação |
| A2 | **Card pós-fut como motor viral** | Único artefato que circula fora do app por vontade do usuário; cada share é aquisição grátis dentro do grupo certo | % de futs com card compartilhado; instalações por link de card |
| A3 | **Beta fechado de julho com funil instrumentado** | Decide as 3 perguntas de PMF (§1.3) antes do lançamento de 10/09; sem dado, o lançamento é aposta cega | eventos: criou fut → 10 confirmados → iniciou → registrou partidas → card → fut seguinte |
| A4 | **Mapa público como utilidade standalone** | Visitante sem conta achando quadra boa já gera valor, boca a boca e (futuro) SEO/ASO local; é também o argumento social do projeto | sessões de visitante; conversão visitante → conta |
| A5 | **Campeonato piloto no verão (pós-TCC)** | Primeira receita alinhada à cultura, valida M1 sem tocar no núcleo gratuito | inscrições pagas; NPS dos times |

Anti-apostas (deliberadamente não fazer agora): expansão para outras cidades, chat interno
completo (o WhatsApp já é o chat; integrar > substituir), ranking competitivo público
(risco de toxicidade antes de moderação), reserva de quadra (contradiz a tese).

---

## 6. Oportunidades adicionais (registro, sem execução)

1. **Fut recorrente/grupos persistentes** (citado em R4): maior alavanca de retenção não
   especificada ainda; candidata a primeira grande feature pós-MVP.
2. **"Tirar time" (capitães escolhem)**: já mapeada como desejada no fut_in_app §10.7;
   culturalmente forte, diferencia ainda mais o motor de jogo.
3. **Integração WhatsApp mais profunda**: mensagem formatada automática da lista do fut
   (texto pronto para colar no grupo), além do link. Custo baixo, adoção alta.
4. **Aba de conscientização (B18)**: além do valor acadêmico, abre porta para parcerias
   institucionais (Secretaria de Esporte/Saúde) que fortalecem M6 e dão legitimidade
   pública ao app.
5. **Score de confiabilidade anti-furo**: os dados já são coletados (`log_chegada`,
   timestamps de participação); o produto futuro (perfil com % de presença) ataca a dor nº4
   do questionário (evitar furos) e é defensável.
6. **Verão como evento de marketing**: "Copa BoraFut de Verão" combinando A2+A5 (cards de
   campeonato compartilháveis).

---

## 7. Premissas e incertezas

### Verificado (com fonte)
- Dores, números do questionário e perfil da amostra: `latex/editaveis/metodologia.tex`
  (§Questionário, §Análise de mercado) e `latex/editaveis/apendices.tex` (instrumento).
- Comparativo de concorrentes e downloads: `metodologia.tex` §Análise de mercado (matriz
  `tab:comparativo_mercado`).
- Especificação do fluxo do fut, modo não-gerenciado, card pós-fut, riscos de lista fraca:
  `BoraFut-ia/fut_in_app_context.md` (§1-§11).
- Papéis, princípios, métricas de sucesso do MVP, escopo: `BoraFut-ia/borafut_contexto_prd.md`
  (§1-§5).
- Estado do código para checagem de viabilidade (mapa por bounding box, seed de 32 quadras,
  auth, reviews): `BoraFut-Backend/prisma/schema.prisma`, `prisma/seed.ts`, Jira (KAN-23 a
  KAN-50) e branches `feat/futs-module` / `feat/court-reviews`.
- Metas e datas do beta/lançamento: suas respostas de 11/06/2026 (beta jul/início ago;
  lançamento 10/09; entrevistas no protótipo Figma com SUS, alvo >10 até agosto).

### Inferido (sem fonte primária; calibrar antes de decidir)
- Tamanhos de mercado, CPMs locais, taxas de conversão premium (3-4%), valores de
  campeonato/patrocínio e a conta de sustento dos fundadores (§4.3): estimativas minhas a
  partir de padrões de apps sociais/locais; não há benchmark citável no repo.
- Probabilidade de movimento competitivo (Appito/Chega+) e custos de Google Maps em escala:
  inferências; recomendo medir custo por MAU no beta.
- A leitura de que o registro de placar pode ter <50% de adesão em fut real (R2): hipótese
  a testar no beta, não dado.
