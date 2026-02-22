# Chatbot de Previsão do Tempo com Telegram e N8N

## 📋 Descrição do Projeto

Este projeto implementa um **chatbot inteligente de previsão do tempo** que se integra com o Telegram, permitindo que usuários consultem informações meteorológicas em tempo real através do messaging. O bot utiliza:

- **N8N**: Plataforma de automação de workflows de baixo código para orquestração
- **OpenWeather API**: Serviço de previsão do tempo em tempo real
- **Telegram Bot API**: Interface de comunicação com usuários
- **PostgreSQL**: Banco de dados para persistência de dados
- **Redis**: Cache e gerenciamento de filas para processamento assíncrono
- **Docker Compose**: Orquestração de containers

### Funcionalidades

- 🌡️ Consulta de previsão do tempo por cidade
- 💬 Interface amigável via Telegram
- ⚡ Processamento assíncrono de requisições
- 📊 Histórico de consultas persistido em banco de dados
- 🔄 Execução em modo fila para melhor escalabilidade

---

## 🚀 Primeiros Passos

### Pré-requisitos

- Docker e Docker Compose instalados
- Conta no Telegram (para criar o bot)
- Chave de API do OpenWeather
- Git (para clonar o repositório)

### Instalação

1. **Clone o repositório:**
- Considerando que você está no diretorio que quer clonar o projeto, faça:
```bash
git clone ttps://github.com/robsonfx/chatbot-telegram

```

2. **Crie o arquivo `.env` na raiz do projeto:**
```bash
cp .env.example .env
```

3. **Configure as variáveis de ambiente** (veja seção abaixo)

4. **Inicie os containers:**
```bash
docker-compose up -d
```

5. **Acesse o N8N:**
```
http://localhost:5678
```

---

## 🔐 Configuração de Credenciais

### 1. Pré-requisitos para o Telegram

Antes de começar, você precisa ter uma conta no Telegram. Se ainda não tiver:

1. Baixe o aplicativo [Telegram](https://telegram.org/) para seu dispositivo
2. Crie uma conta com seu número de telefone
3. Verifique a conta pelo código enviado por SMS
4. Pronto! Agora você está pronto para criar seu bot

### 2. Criando o Bot no Telegram

- Abra o aplicativo **Telegram** e procure por **@BotFather** no campo de busca. Este é o bot oficial do Telegram para gerenciar bots.

- Inicie uma conversa com o BotFather clicando em "Iniciar" ou "Start"

- Envie o comando `/newbot` no campo de mensagem e siga as instruções do BotFather:
  - **Escolha um nome** para seu bot (exemplo: "Weather Bot", "Bot de Previsão")
  - **Escolha um nome de usuário** (username) para seu bot. Este nome deve ser único e **obrigatoriamente terminado em `bot`** (exemplo: `weather_forecast_bot`, `meu_weather_bot`)

- Após completar os passos, o BotFather fornecerá um **token de acesso HTTP API**

- **Copie o token de acesso** fornecido pelo BotFather e guarde em local seguro (não suba no seu repositório em hipótese alguma)

**Formato esperado:**
```
123456789:ABCDefGHIjklMNOpqrSTUvwxYZ1234567890
```

### 3. Obter Chave de API do OpenWeather

1. Acesse [OpenWeather](https://openweathermap.org/api)
2. Crie uma conta ou faça login
3. Vá para "API keys" no painel de controle
4. Copie a chave de API padrão (Default key)
5. Salve o token em um local seguro

**Formato esperado:**
```
a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6
```

### 4. Configurar Variáveis de Ambiente

Abra o arquivo `.env` criado na raiz do projeto e adicione:

```env
# Telegram Bot Token
TELEGRAM_BOT_TOKEN=seu_token_telegram_aqui

# OpenWeather API Key
OPENWEATHER_API_KEY=sua_chave_openweather_aqui

# URL do Webhook (será usado pelo N8N para receber mensagens do Telegram)
WEBHOOK_URL=https://seu-dominio.com


# Google AI Studio API Key (usada no nó Message Model)
GOOGLE_API_KEY=sua_chave_google_ai_studio_aqui
```

**⚠️ IMPORTANTE:** Nunca comite o arquivo `.env` com credenciais reais. Adicione à `.gitignore`:
```
.env
.env.local
```

---

## 📥 Importando o Workflow no N8N

### Passo 1: Acessar a Interface do N8N

1. Abra seu navegador e acesse: `http://localhost:5678`
2. Complete o setup inicial se necessário
3. Você será direcionado ao dashboard principal

### Passo 2: Importar o Workflow

1. Na tela inicial do N8N (dashboard), clique em **"Import"** ou procure por um botão de importação
2. Selecione **"Import from file"**
3. Navegue até o arquivo **`workflow-chatbot-telegram.json`** no seu repositório
4. Selecione o arquivo e confirme
5. Clique em **"Import"** para adicionar o workflow

O workflow será importado automaticamente com todos os nós e configurações.

### Passo 3: Criar um Novo Workflow (Alternativa Manual)

Se preferir criar um workflow do zero em vez de importar:

1. Clique em **"New Workflow"** ou use o atalho
2. Nomeie o workflow (exemplo: "Telegram Weather Bot")
3. Clique em **"Create"**
4. Configure os nós manualmente conforme necessário

### Passo 4: Configurar Credenciais no Workflow

#### Configurar Credencial do Telegram

1. No workflow, localize o nó **Telegram Trigger** ou **Telegram Node**
2. Clique no nó
3. No painel à direita, procure por **"Telegram Bot Token"**
4. Clique em **"Create new credential"**
5. Cole seu **TELEGRAM_BOT_TOKEN**
6. Clique em **"Save"**

**Alternativa - Via Variáveis de Ambiente:**

Se preferir usar variáveis de ambiente:

1. No N8N, vá para **Settings** → **Environment Variables**
2. Adicione:
```
TELEGRAM_BOT_TOKEN=seu_token_aqui
```

#### Configurar Credencial do OpenWeather

1. No workflow, localize o nó **OpenWeather** ou **HTTP Request**
2. Clique no nó
3. Se for HTTP Request, configure:
   - **URL:** `https://api.openweathermap.org/data/2.5/weather`
   - **Query Parameters:**
     - `q`: nome da cidade
     - `appid`: seu OPENWEATHER_API_KEY
     - `units`: `metric` (para Celsius)
     - `lang`: `pt_br` (para português)

4. Clique em **"Save"**

---

## 🎛️ Configurar Credenciais via N8N UI

### Acessar Gerenciador de Credenciais

1. Clique no seu **perfil** (canto superior direito)
2. Selecione **"Credentials"** ou vá para **Settings** → **Credentials**
3. Clique em **"Create New"**
4. Selecione o tipo de credencial (ex: "Telegram")

### Adicionar Credencial do Telegram

1. Nome: `Telegram Bot`
2. Campo **Bot Token**: `seu_TELEGRAM_BOT_TOKEN`
3. Clique em **"Save"**

### Adicionar Credencial do OpenWeather

1. Nome: `OpenWeather API`
2. Campo **API Key**: `seu_OPENWEATHER_API_KEY`
3. Clique em **"Save"**

### Adicionar Credencial do Google AI Studio

1. Nome: `Google AI Studio`
2. Campo **API Key**: cole sua `GOOGLE_API_KEY`
3. Clique em **"Save"**

Uso no nó `Message Model`:

- No workflow, adicione o nó **Message Model**
- No campo de credenciais do nó, selecione a credencial `Google AI Studio` criada acima, ou configure `GOOGLE_API_KEY` como variável de ambiente em **Settings → Environment Variables**
- Configure o modelo, entradas e parâmetros conforme a documentação do Google AI Studio
- Salve o nó e ative o workflow

---

## ▶️ Executando o Chatbot

### 1. Ativar o Workflow

1. Abra o workflow no N8N
2. Clique no botão **"Activate"** (canto superior direito)
3. Você verá uma confirmação de que o workflow está ativo

### 2. Enviar Mensagem de Teste

1. Abra o Telegram
2. Procure pelo seu bot (o nome que você deu no BotFather)
3. Inicie uma conversa clicando em **"Start"**

### 3. Consultar Previsão do Tempo

**Exemplos de mensagens para enviar:**

```
São Paulo, SP
Rio de Janeiro, RJ
Brasília, AP (simulando um erro)
Macapa, AP 
Belem,PA
Manaus no Norte, DF (simulando um erro)
```

### 4. Resposta Esperada

O bot deverá responder com um mensagem contendo:

```
🌡️ Previsão do Tempo para São Paulo Temperatura: 28°C. Dia lindo hoje (algo parecido)


### 5. Monitorar Execução

1. No N8N, vá para a aba **"Executions"**
2. Você verá um histórico de todas as mensagens processadas
3. Clique em qualquer execução para ver os detalhes

---

## 🛠️ Estrutura do Projeto

```
SEU_PROJETO/
├── docker-compose.yml       # Configuração dos containers
├── .env                     # Variáveis de ambiente (não commitar)
├── .env.example            # Template das variáveis de ambiente
├── README.md               # Este arquivo
├── n8n-data/               # Dados do N8N (workflows, credenciais)
├── postgres-data/          # Dados do PostgreSQL
└── redis-data/             # Dados do Redis
```

---

## 📊 Workflows e Nós Principais

### Estrutura Típica do Workflow

```
Telegram Trigger
    ↓
Extract Message Text
    ↓
HTTP Request (OpenWeather API)
    ↓
Format Response
    ↓
Send Telegram Message
```

---

## 🔍 Troubleshooting

### O bot não responde no Telegram

- ✅ Verifique se o `TELEGRAM_BOT_TOKEN` está correto
- ✅ Verifique se o workflow está **Activated**
- ✅ Verifique os logs em **Executions** para erros
- ✅ Reinicie os containers: `docker-compose restart`

### Erro "Invalid API Key" do OpenWeather

- ✅ Verifique se a chave está correta em `.env`
- ✅ Aguarde alguns minutos se acabou de criar a chave
- ✅ Verifique o plano da API (free tier tem limitações)

### Conexão com banco de dados recusada

- ✅ Verifique se o PostgreSQL está rodando: `docker ps`
- ✅ Verifique credenciais do banco em `docker-compose.yml`
- ✅ Verifique logs: `docker-compose logs postgres`

### Webhook não funciona

- ✅ Verifique a variável `WEBHOOK_URL` em `.env`
- ✅ Certifique-se que a URL é acessível externamente
- ✅ Use ngrok ou tunnel similar se estiver em localhost

---

## 📈 Monitoramento

### Ver logs do N8N

```bash
docker-compose logs -f n8n-editor
```

### Ver logs do Worker

```bash
docker-compose logs -f n8n-worker
```

### Ver logs do PostgreSQL

```bash
docker-compose logs -f postgres
```

---

## 🔄 Parar e Reiniciar

```bash
# Parar containers
docker-compose down

# Reiniciar containers
docker-compose restart

# Ver status
docker-compose ps
```

---

## 📚 Recursos Úteis

- [Documentação N8N](https://docs.n8n.io)
- [Telegram Bot API](https://core.telegram.org/bots/api)
- [OpenWeather API](https://openweathermap.org/api)
- [Docker Compose](https://docs.docker.com/compose/)
- [Google AI Studio](https://aistudio.google.com/api-keys)

---

## ⚙️ Variáveis de Ambiente Resumidas

| Variável | Descrição | Exemplo |
|----------|-----------|---------|
| `TELEGRAM_BOT_TOKEN` | Token do bot Telegram | `123456789:ABCDefGHIjklMNOpqrSTUvwxYZ1234567890` |
| `OPENWEATHER_API_KEY` | Chave de API do OpenWeather | `a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6` |
| `GOOGLE_API_KEY` | Chave de API do Google AI Studio (Message Model) | `AIza...sua_chave_aqui` |
| `WEBHOOK_URL` | URL do webhook para Telegram | `https://seu-dominio.com` |


---

## 📝 Licença

Este projeto é fornecido como está, sem garantias de qualquer tipo.

---

## 💬 Suporte

Para dúvidas ou problemas, verifique:
1. Os logs dos containers
2. A documentação oficial do N8N
3. A documentação da API do Telegram
4. A documentação da API do OpenWeather

---

**Última atualização:** 22 de Fevereiro de 2026
