# Arquitetura — GOTA Comercial

## Visão geral

A automação é dividida em dois workflows independentes.

### 01 — Prospector

`SEG-SEX 09:00 -> ler CONFIG/CRM -> consultar cota SerpApi -> analisar até 50 negócios novos -> registrar LEADS -> identificar sem site -> validar WhatsApp -> qualified_pending -> FIFO -> até 8 primeiros contatos -> CRM/CONVERSATIONS/ERROR_LOG -> DAILY_METRICS`

### 02 — Atendimento

`WhatsApp respondeu -> webhook -> normalizar telefone -> identificar lead -> CONVERSATIONS inbound -> carregar histórico -> IA -> atualizar CRM -> opt-out/reunião/interesse -> responder -> CONVERSATIONS outbound`

O setup/QR da Evolution é uma operação de infraestrutura e não faz parte do workflow agendado.

## Regras do Prospector

- Agenda: segunda a sexta às 09:00 em `America/Sao_Paulo`.
- Limite de análise: 50 negócios novos por dia.
- Lote de busca: 20 resultados; normalmente até 3 consultas por dia.
- Limite de primeiro contato: 8 por dia.
- Não existe mínimo de lote: 1 lead disponível pode receber 1 envio.
- Todos os negócios novos analisados são registrados em `LEADS`.
- Site próprio identificado -> `analyzed`, sem abordagem de criação de site.
- Sem site + telefone -> `awaiting_whatsapp_validation`.
- WhatsApp válido -> `qualified_pending`.
- Fila FIFO por entrada/qualificação; sem ranking para decidir quem sai primeiro.
- Somente envio confirmado muda o lead para `contacted`.
- Falha de envio não marca contato; registra `ERROR_LOG` e mantém o lead disponível para nova tentativa.
- `SUPPRESSION` e opt-out prevalecem sempre.
- Uma reexecução no mesmo dia deve calcular os slots restantes para nunca passar de 8 primeiros contatos.
- Se a cota SerpApi estiver zerada, a busca é pausada até a renovação; a fila já existente pode continuar sendo processada.

## Critério de site

Links de rede social ou plataforma de perfil/agendamento não contam como site próprio para a oferta principal. Exemplos: Instagram, Facebook, Linktree, Booksy e páginas equivalentes.

## CRM

Google Sheets `CRM Comercial - Agência Gota` é a base do MVP.

Base operacional: `LEADS`.

Abas usadas:

- `CONFIG`
- `LEADS`
- `CONVERSATIONS`
- `MEETINGS`
- `SUPPRESSION`
- `DAILY_METRICS`
- `ERROR_LOG`

Não criar uma segunda base de leads.

## Métricas operacionais

`DAILY_METRICS` registra também:

- businesses_analyzed
- no_site_found
- whatsapp_valid
- queue_pending
- serpapi_queries_used
- serpapi_queries_remaining
- serpapi_renewal_date
- whatsapp_state
- run_status

## Evolution API

Instância: `gota`.

A Evolution no Render usa Supabase/Postgres para persistência. O Prospector utiliza a Evolution para checar conexão, validar números e enviar o primeiro contato.

A instância deve ser pareada uma única vez via QR/pairing e reutilizada. Não criar nova instância em cada execução.

## Primeiro contato

Mensagem curta:

`Oi, tudo bem? Falo com o responsável pela [EMPRESA]?`

A apresentação comercial completa ocorre somente após resposta e será responsabilidade do Workflow 02 — Atendimento.

## Segurança operacional

- manter `DRY_RUN = TRUE` durante testes;
- virar `FALSE` apenas no teste real controlado;
- sem segredos no GitHub;
- deduplicar por identificadores estáveis/telefone/empresa;
- bloquear opt-outs e SUPPRESSION;
- limitar volume diário;
- registrar falhas sem repetir mensagens cegamente.