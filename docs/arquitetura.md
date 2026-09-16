# Arquitetura — GOTA Prospector

## Fluxo principal

`09:00 -> busca -> filtra sem site -> valida telefone/WhatsApp -> remove duplicados -> acumula até 8 -> CRM -> primeiro contato -> resposta -> IA -> reunião`

## Critérios mínimos do lead

- empresa local
- sem site próprio identificado
- telefone válido
- ainda não existente no CRM
- WhatsApp confirmado quando possível

## CRM

O CRM atual é um Google Sheets com abas de configuração, leads, conversas e métricas. O workflow deve reutilizar essa estrutura em vez de criar outro banco no MVP.

## Prospecção

O alvo diário é 8 novos leads válidos. A busca pode continuar por múltiplos termos/regiões até atingir 8, respeitando limites de segurança de cota e repetição.

## Primeiro contato

Mensagem inicial curta:

`Oi, tudo bem? Falo com o responsável pela [EMPRESA]?`

Após resposta, a IA apresenta a Gota e prioriza criação de site. Gestão de redes sociais entra como oferta secundária.

## IA comercial

A IA deve atualizar, no mínimo:

- interest_level
- wants_meeting
- is_opt_out
- main_pain
- objection
- conversation_summary

## Segurança operacional

- DRY RUN antes de liberar envio real
- sem segredos no GitHub
- evitar duplicidade de empresa/telefone
- registrar opt-out e impedir novo contato
- limitar volume diário
