# 📦 Sistema de Processamento de Pedidos (n8n Workflow)

Este repositório contém um fluxo de trabalho (workflow) para o **n8n** projetado para automatizar o monitoramento, notificação e atualização de status de pedidos de vendas.

O sistema lê uma planilha do Google Sheets em busca de novos pedidos, notifica uma equipe via Discord e atualiza o status do pedido na planilha para evitar duplicidade.

## 🚀 Funcionalidades

1.  **Monitoramento Agendado:** O fluxo roda periodicamente (Schedule Trigger) para verificar novos dados.
2.  **Leitura Condicional:** Busca apenas as linhas onde a coluna `Status` é igual a "Pendente".
3.  **Notificação Automática:** Envia uma mensagem formatada para um canal do Discord via Webhook.
4.  **Atualização de Status:** Altera automaticamente o `Status` na planilha de "Pendente" para "Processado".
5.  **Controle de Fluxo:** Utiliza um loop com espera (`Wait`) de 2 segundos entre execuções para respeitar os limites de taxa (rate limits) das APIs.

## 🛠️ Pré-requisitos

Para utilizar este workflow, você precisará de:

* Uma instância do **n8n** instalada (local ou nuvem).
* Uma conta no **Google Cloud Platform** com a API do Google Sheets habilitada.
* Uma conta no **Discord** e um servidor onde você tenha permissão para criar Webhooks.

## 📊 Estrutura da Planilha (Google Sheets)

O workflow espera uma planilha com, no mínimo, as seguintes colunas (cabeçalhos na primeira linha):

| ID | Cliente | Produto | Valor | Status | Data do Pedido | Detalhes da Entrega |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 001 | João Silva | Curso Python | R$ 150,00 | **Pendente** | 01/01/2026 | E-mail |

> **Nota:** A coluna `Status` é obrigatória para o filtro inicial e para a atualização final. As colunas `Cliente` e `Valor` são usadas na mensagem do Discord.

## ⚙️ Instalação e Configuração

### 1. Importar o Workflow
1. Abra seu editor do n8n.
2. Crie um novo workflow.
3. Clique no menu (três pontos) no canto superior direito e selecione **"Import from File"**.
4. Selecione o arquivo `workflow.json` deste repositório.

### 2. Configurar Credenciais do Google
1. No nó **"Get row(s) in sheet"** e **"Update row in sheet"**:
2. Selecione ou crie uma nova credencial para `Google Sheets OAuth2 API`.
3. Substitua o `Document ID` (atualmente fixo no JSON) pelo ID da sua própria planilha ou selecione-a na lista "From List".
4. Certifique-se de que a aba da planilha (Sheet Name) esteja correta (padrão: `Página1`).

### 3. Configurar o Discord
1. No nó **"HTTP Request"**:
2. Localize o campo `URL`.
3. Substitua a URL existente pela **URL do seu Webhook do Discord**.
   * *Para gerar um:* Vá nas configurações do canal do Discord > Integrações > Webhooks > Novo Webhook.

## 🧩 Detalhes dos Nós (Nodes)

* **Schedule Trigger:** Inicia o fluxo (atualmente configurado para rodar a cada X minutos).
* **Get row(s) in sheet:** Filtra linhas onde `Status` = `Pendente`.
* **Loop Over Items:** Divide os pedidos encontrados para processar um por um.
* **HTTP Request (Discord):** Envia um POST com o JSON:
    ```json
    {
      "content": "💰 Venda Nova! Cliente: {{ $json.Cliente }} - Valor: {{ $json.Valor }}"
    }
    ```
* **Update row in sheet:** Localiza a linha pelo `ID` e muda o status para `Processado`.
* **Wait:** Pausa de 2 segundos para segurança da API.

## ⚠️ Atenção

O arquivo `workflow.json` contém IDs específicos de planilhas e Webhooks que são exemplos. **Lembre-se de alterá-los para os seus dados reais antes de ativar o fluxo.**

---
*Gerado automaticamente para documentação de automação no n8n.*
