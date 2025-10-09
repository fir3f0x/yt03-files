# 🚀 Tutorial: Docker local com n8n + WAHA (Api do Whatsapp)

## 🐳 Passo 1 — Instalar o Docker

1. Acesse <a href="https://docker.com" target="_blank">docker.com</a>
2. Clique em **Download Docker Desktop**
3. Baixe o instalador para seu sistema operacional
4. 🧰 Execute o instalador e siga as instruções

---

## 📥 Passo 2 — Baixar o arquivo de configuração

1. Acesse o repositório oficial do WUZAPI no github  <a href="https://github.com/asternic/wuzapi" target="_blank">Baixar WUZAPI no github</a>
2. Fazer download dos arquivos
3. 📁 Crie uma pasta no seu computador (ex.: `n8n_whatsapp_Local`)
4. Descompacte o arquivo baixado dentro da pasta e renomei para **wuzapi**
5. Acesse o meu repositorio <a href="https://github.com/fir3f0x/yt03-files" target="_blank">Arquivos de Configuração</a>
6. Baixe o arquivo .env e o docker-compose.yml e coloque na pasta **wuzapi** e substitua os já existentes.

---

## ▶️ Passo 3 — Executar os contêineres com Docker

1. Abra a pasta criada (`n8n_whatsapp_Local`)
2. Execute o docker desktop e deixe ele rodando.
3. Abra o terminal na pasta (ou navegue até ela pelo terminal)
    1. Botao direito do mouse (dentro da pasta) > Abri no Terminal
4. Execute:
```
docker-compose up -d
```
5. ⏳ O Docker vai baixar e iniciar os serviços

---

## 🔗 Passo 4 — Conectar o WhatsApp ao Wuazpi

1. No Docker Desktop, localize o contêiner **wuzapi**
2. Clique no link da porta (normalmente `http://localhost:3000`)
3. Vá em **Dashboard**, marque Admin Mode, coloque o token que está no arquivo .env (WUZAPI_ADMIN_TOKEN)
4. Clique em +Add Instance e deixe como na imagem abaixo e clique em **submit**
<img width="70%" alt="image" src="https://github.com/user-attachments/assets/64b29daa-c383-4597-91c1-63294e77fe54" />

5. 📸 Clique em Open e em seguida em Connect. Um QRCODE irá aparecer.
6. No celular: WhatsApp > ⚙️ Configurações > 📡 Aparelhos conectados > **Conectar um aparelho** > escaneie o QR code
<img width="392" height="129" alt="image" src="https://github.com/user-attachments/assets/adb371c2-4819-420c-88a4-d8f02fd00dd2" />


---

## ⚙️ Passo 5 — Configurar o n8n

1. No Docker Desktop, localize o contêiner **n8n**
2. Clique no link da porta (normalmente `http://localhost:5678`)
3. ✍️ Crie a conta de administrador (e-mail, nome, senha)
4. Você pode pular a pesquisa de customização

### 🎁 Ativar funções pagas gratuitamente

1. Clique em **Send me a free license key**
2. 📬 Verifique seu e-mail e copie a chave recebida
3. No n8n: 👤 Ícone do perfil → **Settings** → **Usage and plan**
4. Cole a chave e clique em **Enter activation key** ✅

### 🔌 Instalar o conector WUZAPI

1. Em **Settings**, vá para **Community nodes**
2. Clique em **Install a community node**
3. Digite `n8n-nodes-wuzapi`
4. ☑️ Marque a caixa de confirmação e clique em **Install**

---

## 🤖 Passo 6 — Criar o workflow de automação

1. Volte ao meu repositório <a href="https://github.com/fir3f0x/yt03-files" target="_blank">Baixar fluxo do n8n</a>
2. Baixe agora ou copie o conteudo do arquivo `fluxo_whatsapp_atendimento.json`
3. Volte ao n8n, clique em Create Workflow (normalmente `http://localhost:5678`)
4. Se baixou o arquivo `fluxo_whatsapp_atendimento.json` você importa
<img width="604" height="332" alt="image" src="https://github.com/user-attachments/assets/c54e1a49-a70a-45f2-9a9c-4cc984f29ff3" />

5. Se copiou basta colar e o fluxo irá aparecer.

### 🔁 Configurar o Webhook no WUZAPI

1. Volte ao Dashboard do WUZAPI (`http://localhost:3000/dashboard`)
2. ⚙️ Clique em webhook events
<img width="60%" alt="image" src="https://github.com/user-attachments/assets/e18fac7f-6df6-4408-af23-e2e283682f12" />

3. ➕ Deixe All Events e cole o link do webhook copiado do n8n
<img width="60%" alt="image" src="https://github.com/user-attachments/assets/78c65072-6abd-4f38-b9b9-04e687364879" />

4. 📩 Em **Events**, escolha apenas **message**
5. 💾 Clique em **Set**

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
