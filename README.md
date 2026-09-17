# GOTA n8n

Automação comercial da Agência Gota para prospecção de negócios locais sem site e atendimento via WhatsApp/IA.

## Arquitetura atual

O projeto está separado em dois workflows principais:

1. **Prospector** — busca, análise, qualificação, fila FIFO e primeiro contato.
2. **Atendimento** — recebe respostas do WhatsApp, consulta histórico, usa IA, atualiza CRM e trata reunião/opt-out.

O setup da Evolution/QR fica fora do fluxo agendado.

## Workflow 01 — Prospector

Fonte de verdade operacional: workflow live no n8n `GOTA - 01 PROSPECTOR - FINAL`.

Regras:

- segunda a sexta às 09:00;
- analisar até 50 negócios novos/dia;
- busca em lotes de até 20, normalmente até 3 consultas/dia;
- registrar todos os negócios novos em `LEADS`;
- sem site + telefone -> validar WhatsApp;
- WhatsApp válido -> `qualified_pending`;
- fila FIFO;
- no máximo 8 primeiros contatos/dia;
- se houver 1 lead disponível, envia 1;
- envio confirmado -> `contacted`;
- falha -> permanece pendente + `ERROR_LOG`;
- reexecução no mesmo dia não pode ultrapassar o limite diário de 8;
- se a SerpApi ficar sem cota, novas buscas param até a renovação, mas a fila existente continua utilizável.

O JSON antigo `workflows/01-captura-leads.json` foi removido porque não representava mais a arquitetura atual. Após o teste real com WhatsApp, o JSON final deve ser exportado do n8n e versionado aqui.

## Workflow 02 — Atendimento

`workflows/02-inbound-base-atual.json` ainda é uma base histórica e não deve ser considerado pronto para produção.

O fluxo final de atendimento será:

`mensagem recebida -> identificar lead -> salvar inbound -> carregar histórico -> IA -> opt-out/reunião/interesse -> enviar resposta -> salvar outbound -> atualizar CRM`

## CRM

Planilha: `CRM Comercial - Agência Gota`.

Base operacional única: `LEADS`.

Abas auxiliares: `CONFIG`, `CONVERSATIONS`, `MEETINGS`, `SUPPRESSION`, `DAILY_METRICS`, `ERROR_LOG`.

Não criar uma base paralela no MVP.

## Infraestrutura

- n8n Cloud: orquestração.
- SerpApi: Google Maps.
- Evolution API no Render: ponte com WhatsApp.
- Supabase/Postgres: persistência da Evolution.
- Google Sheets: CRM e métricas.

## Segurança

Nenhuma API key, senha ou token deve ser commitado neste repositório. Segredos ficam nas Credentials do n8n ou nas variáveis de ambiente dos serviços.

O primeiro contato por WhatsApp deve respeitar `SUPPRESSION`, opt-out e o limite operacional configurado.

## Estado atual

- planilha preparada para a nova etapa;
- `DAILY_ANALYSIS_LIMIT = 50`;
- `DAILY_OUTREACH_LIMIT = 8`;
- Prospector live executou sem quebrar e gravou negócios na planilha;
- último teste analisou 20 negócios e encontrou 12 sem site;
- ainda é necessário corrigir/testar o ciclo de busca para completar até 50 quando houver cota;
- Evolution API está implantada e persistindo em Supabase;
- falta parear a instância `gota` com o WhatsApp e executar o primeiro disparo real controlado.