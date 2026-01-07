div align="center">

🚀 Sales ETL Pipeline with State Management

Automação de processamento de pedidos "quase-tempo-real" com garantia de idempotência e tratamento de erros.

Sobre • Fluxo • Tecnologias • Destaques • Como Executar

</div>

📄 Sobre o Projeto

Este projeto consiste numa automação desenvolvida em n8n para simular um pipeline de ETL (Extract, Transform, Load).

O objetivo principal é demonstrar competências de engenharia de dados aplicada a integrações, focando não apenas no transporte de dados, mas na resiliência e controlo de estado da operação.

📐 Arquitetura da Solução

O fluxo segue a lógica de Polling com controlo de estado. Abaixo, a visualização do processo:

graph LR
    A[Cron Job (5min)] -->|Lê Planilha| B(Google Sheets)
    B -->|Filtra 'Pendente'| C{Existem Pedidos?}
    C -->|Sim| D[Loop Over Items]
    C -->|Não| E[Fim]
    D -->|Transforma JSON| F[HTTP Request (Discord)]
    F -->|Sucesso| G[Update Status 'Processado']
    G -->|Wait 2s| D


O Passo a Passo:

Extract (Extração): Um Cron Job verifica a cada 5 minutos a planilha (Google Sheets) em busca de novos registos.

Filter (Filtragem): Aplica um filtro para capturar apenas pedidos com status Pendente.

Loop & Transform: Itera sobre cada pedido individualmente, formatando o payload JSON.

Load (Carga): Envia uma notificação estruturada via API (Webhook do Discord).

State Update (Atualização de Estado): Realiza um Write-back na base de dados (Sheets), alterando o status para Processado.

Rate Limiting: Implementação de um nó de espera (Wait) para respeitar os limites de requisição da API (HTTP 429).

🛠️ Tecnologias Utilizadas

Orquestrador: n8n (Workflow Automation)

Database (Simulado): Google Sheets API

Mensageria: Discord Webhooks

Protocolos: HTTP (POST), JSON

⚙️ Destaques Técnicos

1. Idempotência e Controlo de Estado

O sistema foi desenhado para evitar duplicidade.

Problema: Se o fluxo rodar a cada 5 minutos, ele pegaria os mesmos pedidos repetidamente.
Solução: A lógica de filtro (Status = 'Pendente') combinada com a atualização final (UPDATE Status = 'Processado') garante que cada venda seja processada exatamente uma vez.

2. Tratamento de API Rate Limits

Durante os testes de carga, o envio em massa disparava o erro HTTP 429.

Solução: Implementação de um Wait Node de 2 segundos dentro do loop de processamento (Backoff Strategy), garantindo a entrega confiável.

3. Manipulação de JSON Dinâmico

Mapeamento de dados brutos para payload estruturado:

{
  "content": "💰 Nova Venda Processada! Cliente: {{ $json.Cliente }} - Valor: {{ $json.Valor }}"
}


🚀 Como executar

Instalação: Instale o n8n (via npm ou Docker).

Importação: Importe o ficheiro workflow.json deste repositório para o seu n8n.

Configuração: Configure as suas credenciais do Google Cloud Console (OAuth2).

Base de Dados: Crie uma planilha no Google Sheets com as colunas:

ID

Cliente

Produto

Valor

Status

Start: Ative o workflow.

<div align="center">

Desenvolvido por Karine Oliveira

</div>
