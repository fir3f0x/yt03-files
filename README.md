# 🚀 Tutorial: n8n + WAHA (Local com Docker)

## 🛠️ Pré-requisitos

* 💻 Computador com Windows, macOS ou Linux
* 🌐 Conexão com a internet
* 📧 Conta Google
* 📱 Celular com WhatsApp

---

## 🐳 Passo 1 — Instalar o Docker

1. Acesse [docker.com](https://docker.com)
2. Clique em **Download Docker Desktop**
3. Baixe o instalador para seu sistema operacional
4. 🧰 Execute o instalador e siga as instruções

---

## 📥 Passo 2 — Baixar o arquivo de configuração

1. Acesse o repositório: [https://github.com/fir3f0x/yt03-files/](https://github.com/fir3f0x/yt03-files/)
2. Localize e baixe o arquivo `docker-compose.yml`
3. 📁 Crie uma pasta no seu computador (ex.: `n8n_whatsapp_Local`)
4. Mova `docker-compose.yml` para dentro dessa pasta

---

## ▶️ Passo 3 — Executar os contêineres com Docker

1. Abra a pasta criada (`n8n_whatsapp_Local`)
2. Abra o terminal na pasta (ou navegue até ela pelo terminal)

   1. Botao direito do mouse > Abri no Terminal
3. Execute:

```bash
docker-compose up -d
```

4. ⏳ O Docker vai baixar e iniciar os serviços (n8n, WAHA, Redis, Postgres). 

---

## 🔗 Passo 4 — Conectar o WhatsApp ao WAHA

1. No Docker Desktop, localize o contêiner **waha-1**
2. Clique no link da porta (normalmente `http://localhost:3000`)
3. Em WAHA, vá em **Dashboard**
4. Na seção **Sessions**, clique em **Start** ▶️ na sessão `default`
5. 📸 Clique no ícone de **QR Code (login)**
6. No celular: WhatsApp > ⚙️ Configurações > 📡 Aparelhos conectados > **Conectar um aparelho** > escaneie o QR code

---

## ⚙️ Passo 5 — Configurar o n8n

1. No Docker Desktop, localize o contêiner **n8n-1**
2. Clique no link da porta (normalmente `http://localhost:5678`)
3. ✍️ Crie a conta de administrador (e-mail, nome, senha)
4. Você pode pular a pesquisa de customização

### 🎁 Ativar funções pagas gratuitamente

1. Clique em **Send me a free license key**
2. 📬 Verifique seu e-mail e copie a chave recebida
3. No n8n: 👤 Ícone do perfil → **Settings** → **Usage and plan**
4. Cole a chave e clique em **Enter activation key** ✅

### 🔌 Instalar o conector WAHA

1. Em **Settings**, vá para **Community nodes**
2. Clique em **Install a community node**
3. Digite `n8n-nodes-waha`
4. ☑️ Marque a caixa de confirmação e clique em **Install**

---

## 🤖 Passo 6 — Criar o workflow de automação

1. Na tela principal do n8n, clique em **Start from scratch**

### 📡 Adicionar gatilho (Webhook)

1. ➕ Clique no **+** e selecione **On webhook call**
2. **HTTP Method**: `POST`
3. **Path**: `webhook`
4. 📋 Copie a **Production URL** exibida

### 🔁 Configurar o Webhook no WAHA

1. Volte ao Dashboard do WAHA (`http://localhost:3000/dashboard`)
2. ⚙️ Clique na engrenagem da sua sessão
3. ➕ Clique em **+ Webhook** e cole a URL do n8n
4. 📩 Em **Events**, desmarque tudo exceto **message**
5. 💾 Clique em **Update**

### 🧪 Testar o Webhook no n8n

1. No n8n, clique em **Listen for test event** 🧠
2. 📲 Envie uma mensagem de teste (usando outro número de WhatsApp)
3. O n8n receberá os dados. 📌 Clique no ícone de **pin** para salvar os dados

### 🧠 Estrutura mínima do workflow (nós)

* 🔀 **Switch**: filtrar apenas eventos do tipo `message`
* 🤖 **AI Agent**: configurar *System Message* com persona e objetivo do chatbot
* 💬 **Google Gemini Chat Model**: conectar ao **Chat Model** do AI Agent

  * 🔑 Criar credencial com sua API Key do Gemini
* 🧠 **Redis Chat Memory**: conectar ao **Memory** do AI Agent

  * ⚙️ Credencial: `host = host.docker.internal`, `password = default`
* 👁️‍🗨️ **WAHA (Send Seen)**: marcar a mensagem como lida
* 📤 **WAHA (Send Text)**: enviar a resposta

  * 🔗 Mapear `output` do **AI Agent** para o campo **Text**
* 🗺️ Mapear campos essenciais (`chatId`, `session`, etc.) conforme necessário

💾 Salve o workflow e clique em **Ativar**.

---

⚠️⚠️ Como nem tudo são flores, a estrutura de automação rodando localmente através do Docker possui algumas limitações importantes:

1.  🚫 **Não Recebe Webhooks Externos:** A principal restrição é que, por estar rodando na rede local do seu computador (`localhost`), a instalação do n8n não é acessível pela internet. Isso impede que outros serviços ou plataformas online (como gateways de pagamento, CRMs, plataformas de e-commerce, etc.) enviem notificações via webhook para iniciar suas automações.

2.  🎯 **Foco Exclusivo em WhatsApp via WAHA**: Consequentemente, este setup é projetado especificamente para automações que são iniciadas e contidas dentro da interação com o WhatsApp (através do WAHA), onde o próprio n8n busca as informações ou é acionado por um evento que já está em sua rede local.

3.  💻 **Dependência do Computador Ligado:** A automação só funcionará enquanto o seu computador estiver ligado e o Docker Desktop estiver em execução. Se o computador for desligado ou entrar em modo de suspensão, o seu agente de IA ficará offline. 😴
