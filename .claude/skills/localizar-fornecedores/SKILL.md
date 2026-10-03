---
name: localizar-fornecedores
description: Localiza empresas fornecedoras de produtos ou serviços, priorizando as sediadas em Belém do Pará e depois as nacionais. Lista nome, site, e-mail comercial, telefone de voz, chat e canal de WhatsApp. Use quando o usuário pedir para achar, cotar, pesquisar ou mapear fornecedores, prestadores ou empresas de um ramo, ou invocar /localizar-fornecedores.
---

# Localizar Fornecedores (Belém/PA → Brasil)

Pesquisa fornecedores de um produto ou serviço e entrega uma tabela de contatos comerciais verificados. Empresas de Belém/PA vêm primeiro e as nacionais depois.

## ETAPA 0 — PERGUNTAR ANTES DE EXECUTAR (obrigatório)

Não pesquise nada antes de ter a resposta. Use `AskUserQuestion`. Se a ferramenta não estiver disponível, pergunte em texto e aguarde.

Pergunta principal (texto livre):
> "Qual ramo comercial, produto ou serviço devo procurar? (ex.: material de escritório, software jurídico, manutenção de ar-condicionado, papelaria, TI)"

Perguntas complementares (todas opcionais; assuma o padrão se o operador não responder):
1. **Especificação:** marca, modelo, volume, prazo ou requisito especial? (padrão: nenhum)
2. **Quantidade de empresas:** quantas de Belém e quantas nacionais? (padrão: até 10 de Belém + até 10 nacionais)
3. **Região metropolitana:** incluir Ananindeua, Marituba e Benevides junto com Belém? (padrão: sim, listadas depois de Belém)
4. **Tipo de fornecedor:** fabricante, distribuidor, revendedor ou prestador de serviço? (padrão: todos)
5. **Formato de saída:** tabela no chat, arquivo CSV ou planilha .xlsx? (padrão: tabela no chat)

Depois de receber as respostas, repita em uma linha o que será buscado e prossiga:
> "Buscando: **[ramo/produto]** — prioridade Belém/PA, depois Brasil."

## ETAPA 1 — GERAR TERMOS DE BUSCA

A partir do ramo informado, monte de 3 a 6 variações: o termo principal, sinônimos, termos técnicos e termos populares. Exemplo para "ar-condicionado": "manutenção de ar condicionado", "instalação split", "climatização", "refrigeração comercial".

## ETAPA 2 — BUSCA PRIORITÁRIA: BELÉM/PA

Use `WebSearch` com as consultas abaixo, substituindo `{termo}` por cada variação:

```
{termo} Belém PA
{termo} em Belém do Pará empresa
{termo} Belém PA telefone whatsapp
{termo} Belém PA orçamento
{termo} Belém PA site:.com.br
{termo} Belém PA fornecedor atacado
{termo} Ananindeua OR Marituba OR Benevides PA
```

Fontes complementares, via `WebSearch` com `site:` ou via `WebFetch`:
- Google Maps e Google Meu Negócio: `{termo} Belém PA maps`
- Guias empresariais: `site:guiamais.com.br`, `site:telelistas.net`, `site:apontador.com.br`
- Associações e federações locais: `{termo} associação Pará`, `site:fiepa.org.br`, `site:fecomerciopa.com.br`
- Redes sociais comerciais: `site:instagram.com {termo} Belém`, `site:facebook.com {termo} Belém`
- Cadastro de CNPJ para confirmar a sede: `{razão social} CNPJ Belém` (consultar sites públicos como cnpj.biz ou casadosdados.com.br)

**Critério de "sediada em Belém":** o endereço da matriz, ou da unidade que atende, deve ser em Belém/PA. Confirme no site (rodapé ou "Contato") ou no CNPJ. Filiais de empresas nacionais ficam na lista de Belém só se tiverem endereço local e contato local, com a observação "filial".

## ETAPA 3 — BUSCA NACIONAL

Execute só depois de concluir a Etapa 2, ou se Belém tiver menos empresas que o pedido. Complete com empresas do Brasil que atendam o Pará.

```
{termo} fornecedor Brasil
{termo} empresa nacional entrega todo o Brasil
{termo} atacado distribuidor
{termo} comprar online empresa CNPJ
{termo} entrega para Belém PA
{termo} fornecedor B2B
```

Prefira empresas que entreguem ou atendam o Pará, ou que tenham representante na Região Norte. Registre isso na coluna "Observações".

## ETAPA 4 — EXTRAÇÃO DOS CONTATOS

Para cada empresa candidata, abra o site oficial com `WebFetch`. Se houver, abra também as páginas /contato, /fale-conosco, /sobre, /atendimento e /orcamento. Extraia:

| Campo | Onde procurar | Regras |
|---|---|---|
| **Nome** | Cabeçalho, rodapé, "Sobre" | Razão social ou nome fantasia, mais CNPJ se disponível |
| **Site** | URL oficial | Use o domínio da própria empresa, nunca um agregador |
| **E-mail comercial** | "Contato", rodapé, "Orçamento" | Prefira vendas@, comercial@, contato@, orcamento@. Marque como "genérico" se for contato@ |
| **Telefone de voz** | Cabeçalho, rodapé, "Contato" | Formato (91) 3xxx-xxxx, 0800 ou celular de atendimento. Não confunda com o número que só aceita WhatsApp |
| **Chat** | Widget no site (Tawk, JivoChat, Zendesk, Intercom, HubSpot, Blip), Messenger, Instagram DM | Informe "Sim" mais o canal ou link, ou "Não encontrado" |
| **WhatsApp** | Botão flutuante, links `wa.me/` ou `api.whatsapp.com/send?phone=` | Registre `https://wa.me/55DDDNÚMERO`. Procure no HTML por `wa.me`, `whatsapp` e `api.whatsapp` |

Se o `WebFetch` não retornar o contato, tente nesta ordem:
1. `WebSearch`: `"{nome da empresa}" telefone email whatsapp`
2. Perfil no Google Maps ou no guia empresarial
3. Perfil público no Instagram ou Facebook da empresa
4. Consulta de CNPJ (e-mail e telefone cadastrados)

## ETAPA 5 — VALIDAÇÃO (regras anti-erro)

1. **Nunca invente dados.** Se não achou, escreva `não encontrado`. Um campo vazio é melhor que um dado falso.
2. **Confirme cada contato em pelo menos uma fonte** que o associe à empresa. Dê preferência ao site oficial.
3. **Descarte:** empresas inativas ou baixadas no CNPJ, sites fora do ar, páginas só de anúncio ou de marketplace sem identificar a empresa, e duplicatas (mesmo CNPJ ou mesmo domínio).
4. **Normalize** telefones para `(DDD) número` e links de WhatsApp para `https://wa.me/55DDDNÚMERO`.
5. **Registre a fonte** (URL de onde veio o contato) e a data da consulta.
6. **Dados pessoais:** inclua só canais comerciais da empresa. Não inclua e-mail ou celular pessoal de sócio ou funcionário.
7. **Coloque a confiança em cada linha:**
   - Alta: contato lido no site oficial
   - Média: contato lido em guia ou rede social
   - Baixa: contato indireto, deve ser conferido

## ETAPA 6 — FORMATO DA RESPOSTA

### 6.1 Resumo
```
🔎 Busca: {ramo/produto}
📅 Data: {data de hoje}
📍 Prioridade: Belém/PA → Brasil
✅ Encontradas: {N} em Belém | {M} nacionais
```

### 6.2 Tabela A — Empresas sediadas em Belém/PA

| # | Empresa | Site | E-mail comercial | Telefone (voz) | Chat | WhatsApp | Confiança | Observações |
|---|---|---|---|---|---|---|---|---|

### 6.3 Tabela B — Empresas nacionais

Use as mesmas colunas e acrescente "Cidade/UF" depois de "Empresa". No campo "Observações", diga se atendem o Pará.

### 6.4 Fontes
Liste as URLs consultadas, agrupadas por empresa.

### 6.5 Alertas
- Empresas encontradas, mas sem contato verificável
- Campos "não encontrado" que merecem uma ligação ou visita ao site
- Contatos de confiança "Baixa" que precisam de conferência

### 6.6 Próximos passos (ofereça ao operador)
- Redigir um e-mail padrão de pedido de cotação para todas as empresas
- Gerar a lista em CSV ou XLSX
- Comparar as propostas depois de recebidas
- Ampliar a busca (outro termo, outro estado)

## ETAPA 7 — EXPORTAÇÃO (somente se pedida)

- **CSV:** use `Write` para gravar `fornecedores_{ramo}_{AAAA-MM-DD}.csv` em UTF-8 com BOM e separador `;`. As colunas são `Grupo;Empresa;Cidade/UF;Site;Email;Telefone;Chat;WhatsApp;Confianca;Fonte;Observacoes`.
- **XLSX:** use a skill `anthropic-skills:xlsx`, com uma aba "Belém" e outra "Brasil".
- **Envio por e-mail:** só se o operador pedir expressamente. Nesse caso, crie primeiro um **rascunho** de e-mail e peça confirmação antes de enviar qualquer mensagem a terceiros.

## REGRAS GERAIS

- Responda sempre em português do Brasil.
- Pergunte antes de buscar. Nunca pule a Etapa 0.
- Belém aparece sempre antes do restante do Brasil, mesmo que as empresas nacionais pareçam melhores. Se achar uma opção nacional muito superior, destaque-a em "Alertas" sem alterar a ordem.
- Não envie mensagens, e-mails ou formulários a nenhuma empresa sem autorização expressa do operador.
- Se a busca retornar pouco, diga isso com clareza e ofereça ampliar para o Pará inteiro, a Região Norte ou termos alternativos.
- Se o ramo for ambíguo, peça esclarecimento em uma pergunta curta antes de buscar.
- Informe sempre a data da pesquisa, pois contatos mudam.
