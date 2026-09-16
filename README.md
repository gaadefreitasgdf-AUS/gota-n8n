# GOTA n8n

Automação comercial da Agência Gota para prospecção de negócios locais sem site.

## Objetivo

Executar diariamente às 09:00, encontrar até 8 novos leads válidos, registrar no CRM (Google Sheets), iniciar contato por WhatsApp e encaminhar respostas para a IA comercial com foco principal em criação de sites.

## Estrutura

- `workflows/01-prospector-automatico.json` — captura de leads + CRM + primeiro contato.
- `workflows/02-inbound-base-atual.json` — base atual do fluxo de respostas recebidas via WhatsApp/IA.
- `docs/arquitetura.md` — desenho do sistema e regras.
- `config/env.example` — nomes das credenciais/variáveis necessárias.

## Segurança

Nenhuma chave de API deve ser commitada neste repositório. Use Credentials do n8n para SerpApi, Evolution API, Google Sheets e OpenAI.

## Estado atual

1. Estrutura inicial criada.
2. Workflow de prospecção preparado com DRY RUN forçado para teste.
3. Workflow inbound preservado como base para correção e conexão com o CRM.

## Próximos passos

1. Importar `01-prospector-automatico.json` no n8n.
2. Selecionar as credenciais no n8n.
3. Testar em DRY RUN.
4. Validar gravação de até 8 leads.
5. Corrigir e finalizar `02-inbound-base-atual.json`.
6. Só então liberar envio real.
