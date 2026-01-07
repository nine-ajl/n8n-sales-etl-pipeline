<!-- Banner do Projeto -->

<div align="center">
<img src="https://www.google.com/search?q=https://placehold.co/1200x350/5865F2/FFFFFF/png%3Ftext%3DSales%2BETL%2BPipeline%2BAutomation%26font%3Droboto" alt="Project Banner" width="100%">
</div>

<!-- Título e Badges Centralizados -->

<h1 align="center">🚀 Sales ETL Pipeline with State Management</h1>

<div align="center">

Automação de processamento de pedidos "quase-tempo-real" com garantia de idempotência e tratamento de erros.

<p>
<img src="https://www.google.com/search?q=https://img.shields.io/badge/n8n-Workflow_Automation-FF655A%3Fstyle%3Dfor-the-badge%26logo%3Dn8n%26logoColor%3Dwhite" alt="n8n Badge" />
<img src="https://www.google.com/search?q=https://img.shields.io/badge/Google_Sheets-Database-34A853%3Fstyle%3Dfor-the-badge%26logo%3Dgooglesheets%26logoColor%3Dwhite" alt="Google Sheets Badge" />
<img src="https://www.google.com/search?q=https://img.shields.io/badge/Discord-Notification-5865F2%3Fstyle%3Dfor-the-badge%26logo%3Ddiscord%26logoColor%3Dwhite" alt="Discord Badge" />
<img src="https://www.google.com/search?q=https://img.shields.io/badge/JSON-Data_Interchange-000000%3Fstyle%3Dfor-the-badge%26logo%3Djson%26logoColor%3Dwhite" alt="JSON Badge" />
</p>

Sobre • Fluxo • Tecnologias • Destaques • Como Executar

</div>

📄 Sobre o Projeto

Este projeto consiste numa automação desenvolvida em n8n para simular um pipeline de ETL (Extract, Transform, Load) corporativo.

O objetivo principal é demonstrar competências de engenharia de dados aplicada a integrações, focando não apenas no transporte de dados, mas na resiliência e controlo de estado da operação (State Management).

📐 Arquitetura da Solução

O fluxo segue a lógica de Polling inteligente. Abaixo, a visualização do processo lógico:

graph LR
    A[Cron Job (5min)] -->|Lê Planilha| B(Google Sheets)
    B -->|Filtra 'Pendente'| C{Existem Pedidos?}
    C -->|Sim| D[Loop Over Items]
    C -->|Não| E[Fim do Ciclo]
    D -->|Transforma JSON| F[HTTP Request (Discord)]
    F -->|Sucesso 200 OK| G[Update Status 'Processado']
    G -->|Wait 2s (Rate Limit)| D


👣 O Passo a Passo Técnico

Passo

Etapa

Descrição Técnica

01

Extract

Um Cron Job verifica a cada 5 minutos a base de dados (Google Sheets) em busca de novos registos.

02

Filter

Aplica um filtro de pré-processamento para capturar apenas pedidos com status Pendente.

03

Loop

Itera sobre cada pedido individualmente (Batch Size: 1), garantindo processamento atómico.

04

Load

Envia uma notificação estruturada via API (Webhook do Discord) com payload JSON formatado.

05

State

Realiza um Write-back na base de dados, alterando o status para Processado (Idempotência).

06

Backoff

Implementação de um nó de espera (Wait) para respeitar os limites de requisição da API (HTTP 429).

🛠️ Tecnologias Utilizadas

Orquestrador: n8n (Workflow Automation)

Database: Google Sheets API (Simulando Banco Relacional)

Notificações: Discord Webhooks

Linguagem de Dados: JSON & JavaScript (Expressões)

⚙️ Destaques Técnicos

1. Idempotência e Controlo de Estado

O sistema foi desenhado para ser à prova de falhas e duplicações.

O Problema: Em sistemas de polling (verificação periódica), é comum o robô processar o mesmo dado várias vezes se não houver controlo.

A Solução: A lógica de filtro (Status = 'Pendente') combinada com a atualização final (UPDATE Status = 'Processado') cria um "bloqueio lógico", garantindo que cada venda seja processada exatamente uma vez.

2. Tratamento de API Rate Limits

Durante os testes de carga, o envio em massa disparava o erro HTTP 429 (Too Many Requests).

A Solução: Implementação de um Wait Node de 2 segundos dentro do loop de processamento. Esta estratégia de backoff previne o bloqueio do IP da aplicação.

3. Manipulação de JSON Dinâmico

Exemplo da estrutura de dados transformada antes do envio:

{
  "content": "💰 Nova Venda Processada! Cliente: {{ $json.Cliente }} - Valor: {{ $json.Valor }}"
}


🚀 Como Executar

Instalação:

Instale o n8n (via npm ou Docker) ou use a versão Cloud.

Importação:

Baixe o ficheiro workflow.json deste repositório.

No n8n, vá em Workflow > Import from File.

Configuração de Credenciais:

Configure o OAuth2 para o Google Sheets (Console Google Cloud).

Preparação da Base:

Crie uma planilha com o cabeçalho: ID, Cliente, Produto, Valor, Status.

Start:

Ative o workflow e insira dados com status Pendente na planilha.

<div align="center">

Desenvolvido por Karine Oliveira

</div>
