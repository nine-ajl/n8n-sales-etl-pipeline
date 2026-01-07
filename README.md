Sales ETL Pipeline with State Management (n8n)

Este projeto consiste em uma automação desenvolvida em n8n para simular um pipeline de ETL (Extract, Transform, Load) para processamento de pedidos de vendas em tempo quase real.

O objetivo é demonstrar competências em orquestração de dados, integração de APIs e tratamento de limites de requisição (Rate Limiting).

🏗️ Arquitetura da Solução

O fluxo segue a lógica de Polling com controle de estado:

Extract (Extração): Um Cron Job verifica a cada 5 minutos uma planilha (Google Sheets) em busca de novos registros.

Filter (Filtragem): Aplica um filtro para capturar apenas pedidos com status Pendente, garantindo eficiência.

Loop & Transform: Itera sobre cada pedido individualmente, formatando o payload JSON.

Load (Carga): Envia uma notificação estruturada via API (Webhook do Discord).

State Update (Atualização de Estado): Realiza um Write-back no banco de dados (Sheets), alterando o status para Processado para garantir idempotência (evitar duplicidade).

Rate Limiting: Implementação de um nó de espera (Wait) para respeitar os limites de requisição da API de destino (HTTP 429).

🛠️ Tecnologias Utilizadas

Orquestrador: n8n (Workflow Automation)

Database (Simulado): Google Sheets API

Mensageria/Notificação: Discord Webhooks

Protocolos: HTTP (POST), JSON

⚙️ Destaques Técnicos

1. Idempotência e Controle de Estado

O sistema foi desenhado para evitar processamento duplicado. Mesmo que o gatilho rode a cada 5 minutos, a lógica de filtro (Status = 'Pendente') combinada com a atualização final (UPDATE Status = 'Processado') garante que cada venda seja notificada apenas uma vez.

2. Tratamento de API Rate Limits

Durante o desenvolvimento, foi identificado que o envio em massa disparava o erro HTTP 429 (Too Many Requests) da API do Discord.
Solução: Implementação de um Wait Node de 2 segundos dentro do loop de processamento, garantindo a entrega confiável das mensagens sem bloqueios.

3. Manipulação de JSON

Utilização de expressões dinâmicas para mapear dados recebidos da planilha para o corpo da requisição HTTP.

// Exemplo do Payload enviado
{
  "content": "💰 Nova Venda Processada! Cliente: {{ $json.Cliente }} - Valor: {{ $json.Valor }}"
}


🚀 Como executar

Instale o n8n (via npm ou Docker).

Importe o arquivo workflow.json deste repositório.

Configure suas credenciais do Google Cloud Console (OAuth2).

Crie uma planilha com as colunas: ID, Cliente, Produto, Valor, Status.

Ative o workflow.

Desenvolvido por Karine Oliveira.
