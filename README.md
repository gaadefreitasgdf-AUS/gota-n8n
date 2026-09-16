# GOTA n8n

Automação comercial da Agência Gota para prospecção de negócios locais sem site.

## Objetivo

Executar diariamente às 09:00, buscar negócios locais no Google Maps via SerpApi, continuar pesquisando até acumular até 8 leads novos sem site e com telefone, remover duplicados usando o CRM e registrar os leads no Google Sheets. Em seguida, o projeto será conectado ao WhatsApp e à IA comercial para conduzir a conversa até uma reunião.

## Estrutura

- `workflows/01-captura-leads.json` — captura automática de até 8 leads + deduplicação + gravação no CRM.
- `workflows/02-inbound-base-atual.json` — base atual do fluxo de respostas recebidas via WhatsApp/IA; ainda precisa de correções e conexões.
- `docs/arquitetura.md` — desenho do sistema e regras.
- `config/env.example` — nomes das credenciais/variáveis necessárias.
- `.gitignore` — bloqueia arquivos locais de segredo.

## Segurança

Nenhuma chave de API deve ser commitada neste repositório. SerpApi, Evolution API, Google Sheets e OpenAI devem ficar em Credentials do n8n.

## Estado atual

1. Repositório inicializado e versionado.
2. Captura automática às 09:00 criada.
3. O workflow percorre diferentes segmentos/regiões e para ao chegar em 8 leads ou ao esgotar as buscas da execução.
4. O filtro exige telefone, ausência de website e ausência de duplicidade no CRM.
5. Os leads aprovados são gravados na aba `LEADS` com estratégia `site_first`.
6. A primeira mensagem já é preparada no campo `_message`, mas o disparo de WhatsApp será conectado em um workflow separado para facilitar testes e segurança.
7. O workflow inbound anterior foi preservado como base e ainda não está pronto para produção.

## Próximos passos

1. Importar `workflows/01-captura-leads.json` no n8n.
2. Vincular Google Sheets e SerpApi nas Credentials do n8n.
3. Testar a captura manualmente e validar os 8 leads no CRM.
4. Criar `02-outbound-whatsapp.json` para disparar os leads aprovados.
5. Corrigir e finalizar o inbound/IA.
6. Conectar agendamento e atualização do CRM.

## Regra operacional desejada

`09:00 -> buscar -> sem site? -> com telefone? -> já existe no CRM? -> acumular até 8 -> salvar CRM -> WhatsApp -> resposta -> IA -> reunião`
