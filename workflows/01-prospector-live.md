# Workflow 01 — Prospector live

Fonte de verdade operacional: workflow n8n `GOTA - 01 PROSPECTOR - FINAL`.

O JSON antigo de captura foi removido para evitar importação de uma versão desatualizada. O JSON definitivo deve ser exportado do n8n somente depois do teste real com WhatsApp e então versionado neste repositório.

## Regras atuais

- Agenda: segunda a sexta, 09:00, timezone `America/Sao_Paulo`.
- `DAILY_ANALYSIS_LIMIT = 50` negócios novos analisados por dia.
- `SEARCH_BATCH_SIZE = 20`, portanto no máximo 3 consultas de busca por dia para completar 50 resultados.
- `DAILY_OUTREACH_LIMIT = 8` primeiros contatos por dia.
- Não existe mínimo de 4: se houver 1 qualificado disponível, envia 1.
- Todos os negócios novos analisados entram em `LEADS`.
- Negócio com site próprio fica como analisado e não entra na fila de site.
- Negócio sem site + telefone passa por validação de WhatsApp.
- WhatsApp válido -> `stage = qualified_pending`.
- A fila é FIFO; os mais antigos saem primeiro.
- Somente envio confirmado muda o lead para `contacted`.
- Falha de envio mantém o lead pendente e registra `ERROR_LOG`.
- `SUPPRESSION` e `opt_out` sempre bloqueiam envio.
- Antes de enviar, o workflow calcula quantos primeiros contatos já foram enviados no dia para não ultrapassar 8 em reexecuções.
- Se a cota SerpApi estiver esgotada, não faz novas buscas, mas pode trabalhar a fila existente.
- `DRY_RUN` deve permanecer `TRUE` durante os testes e virar `FALSE` somente para o teste real controlado.

## Planilha

Arquivo: `CRM Comercial - Agência Gota`.

Base operacional: `LEADS`.

Abas auxiliares usadas pelo Prospector: `CONFIG`, `SUPPRESSION`, `CONVERSATIONS`, `DAILY_METRICS`, `ERROR_LOG`.

`DAILY_METRICS` inclui também:

- `serpapi_queries_used`
- `serpapi_queries_remaining`
- `serpapi_renewal_date`
- `whatsapp_state`
- `run_status`

## Evolution API

Serviço Render: `evolution-api`.

A conexão do WhatsApp usa a instância existente `gota`; não criar outra instância.

O Prospector consulta a Evolution para:

1. verificar `connectionState`;
2. validar números no WhatsApp;
3. enviar o primeiro contato.

O setup/QR não deve fazer parte do fluxo agendado de prospecção.

## Próxima validação

O último teste analisou 20 empresas e encontrou 12 sem site. Antes de publicar, o fluxo live deve ser ajustado/testado para respeitar `DAILY_ANALYSIS_LIMIT = 50`, inspecionando por que a execução anterior parou após o primeiro lote.