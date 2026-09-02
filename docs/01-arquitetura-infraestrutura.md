# 01 — Arquitetura e Infraestrutura

## 1. Requisitos

### VPS

Referência documentada:

- Ubuntu 22.04+ ou Debian 12+;
- 4 GB RAM como mínimo prático;
- 2 GB pode causar OOM no build do OpenClaw;
- 8 GB+ é mais confortável;
- modelos locais maiores exigem mais RAM;
- pelo menos 20 GB de disco;
- SSH configurado.

A imagem pode ocupar aproximadamente 3 GB; LM Studio pode adicionar aproximadamente 1 GB, sem contar modelos.

### Docker Desktop

O build do OpenClaw é pesado. Menos de aproximadamente 8 GB disponíveis ao Docker pode causar OOM. Aproximadamente 12 GB é uma configuração mais confortável.

## 2. Instalação

### Automática

```bash
./install.sh
```

O instalador documentado:

- verifica/instala Docker;
- detecta o sistema operacional;
- resolve diretórios persistentes;
- cria/atualiza `.env`;
- pergunta quais backends locais instalar;
- gera segredos vazios automaticamente quando aplicável;
- cria diretórios;
- normaliza `entrypoint.sh` para LF;
- prepara a stack.

### Manual

```bash
git clone https://github.com/ericorenato/vibestack-openclaw.git
cd vibestack-openclaw

mkdir -p ~/.openclaw ~/.ollama ~/.hermes ~/.lmstudio
cp .env.example .env
```

Gerar segredos com:

```bash
openssl rand -hex 32
```

Segredos citados no contexto-base:

- `OPENCLAW_GATEWAY_TOKEN`
- `GOG_KEYRING_PASSWORD`
- `HERMES_API_SERVER_KEY`

Subida:

```bash
docker compose build
docker compose up -d
```

No Windows, comandos Unix como `git`, `mkdir -p`, `openssl`, `cp` e `tr` devem ser executados em Git Bash ou WSL quando necessário.

## 3. Persistência

Diretórios principais:

- `OPENCLAW_DATA_DIR`
- `OLLAMA_DATA_DIR`
- `HERMES_DATA_DIR`
- `LMSTUDIO_DATA_DIR`
- `HIGGSFIELD_DATA_DIR`
- `EVOLUTION_DATA_DIR`

Em VPS, normalmente apontam para caminhos em `/root/...`.

Em Mac/Windows, devem apontar para diretórios dentro do HOME do usuário. Não usar `/root/...` diretamente nesses sistemas, pois pode causar `mounts denied`.

A persistência mantém configurações, modelos, tokens, sessões e arquivos após restart/rebuild.

## 4. OpenClaw

OpenClaw é o processo principal quando instalado.

Gateway padrão documentado:

`http://127.0.0.1:18789`

Em VPS, preferir SSH tunnel:

```bash
ssh -N -L 18789:127.0.0.1:18789 root@YOUR_VPS_IP
```

Depois acessar localmente:

`http://127.0.0.1:18789`

Autenticação via `OPENCLAW_GATEWAY_TOKEN`.

Na UI, as áreas relevantes incluem modelos, MCP Servers e Agents.

O projeto utiliza perfil global de tools relacionado ao perfil `coding`, permitindo ferramentas básicas e MCPs registrados.

## 5. Ollama

Instalação condicional:

```env
INSTALL_OLLAMA=true
```

Porta:

`11434`

Comandos documentados:

```bash
docker compose exec openclaw-vibestack models-status
docker compose exec openclaw-vibestack start-ollama
docker compose exec openclaw-vibestack ollama pull llama3.2:3b
docker compose exec openclaw-vibestack ollama pull qwen2.5:7b
docker compose exec openclaw-vibestack ollama list
```

Referências de tamanho de modelo:

- cerca de 3 GB RAM: `llama3.2:3b`, `phi3:mini`;
- cerca de 8 GB RAM: `qwen2.5:7b`, `mistral:7b`;
- 16 GB+: `qwen2.5:14b`, `llama3.1:8b-instruct`.

Modelos persistem no volume do Ollama.

## 6. LM Studio

Instalação condicional:

```env
INSTALL_LMSTUDIO=true
```

Porta:

`1234`

Usado em modo headless. O build instala runtime e CLI `lms`.

Comandos:

```bash
docker compose exec openclaw-vibestack start-lmstudio
docker compose exec openclaw-vibestack models-status
```

Modelos persistem em `LMSTUDIO_DATA_DIR`.

## 7. Hermes Agent

Hermes é uma alternativa/plataforma de agente no mesmo container. Não deve ser tratado como MCP.

Combinações possíveis:

- OpenClaw + Hermes;
- somente OpenClaw;
- somente Hermes.

API OpenAI-compatible:

`http://127.0.0.1:8642/v1`

Health:

```bash
curl http://127.0.0.1:8642/health
```

Modelos:

```bash
curl http://127.0.0.1:8642/v1/models \
  -H "Authorization: Bearer $HERMES_API_SERVER_KEY"
```

Modelo exposto documentado:

`hermes-agent`

Dashboard:

`http://127.0.0.1:9119`

Tunnels em VPS:

```bash
ssh -N -L 8642:127.0.0.1:8642 root@YOUR_VPS_IP
ssh -N -L 9119:127.0.0.1:9119 root@YOUR_VPS_IP
```

O provider/modelo do Hermes **não é configurado no build**.

Configuração posterior:

```bash
docker compose exec -it openclaw-vibestack hermes model
```

ou editar:

`${HERMES_DATA_DIR}/config.yaml`

Providers podem incluir OpenRouter, Anthropic, Nous Portal, Ollama e outros suportados pelo Hermes.

Sem provider/modelo configurado, a API pode subir, mas completions falham.

Variáveis citadas:

```env
HERMES_REF=main
HERMES_DATA_DIR=/root/.hermes
HERMES_API_PORT=8642
HERMES_WEB_PORT=9119
HERMES_API_SERVER_KEY=
GATEWAY_ALLOW_ALL_USERS=true
HERMES_APPROVALS_MODE=off
```

Em canais headless como WhatsApp/API, aprovação manual pode travar o fluxo. Por isso o projeto documenta `HERMES_APPROVALS_MODE=off` por padrão.

## 8. Entrypoint

`entrypoint.sh` é responsável por:

1. detectar quais plataformas estão instaladas;
2. iniciar Ollama/LM Studio quando instalados;
3. registrar MCPs;
4. preparar Hermes;
5. preparar WhatsApp bridge;
6. criar diretórios persistentes;
7. aplicar patch do timeout de model runtime do OpenClaw;
8. iniciar o processo principal.

Variáveis de instalação:

- `INSTALL_OPENCLAW`
- `INSTALL_HERMES`

Fallback de bridge documentado:

- se Hermes não está instalado: `WA_BRIDGE_AGENT=openclaw`;
- se OpenClaw não está instalado: `WA_BRIDGE_AGENT=hermes`.

## 9. Patch de timeout do OpenClaw

O snapshot atual contém correção para o erro:

`prepared model runtime publication timed out`

OpenClaw 2026.7.x possui timeout hardcoded de aproximadamente 30 segundos para publicação do model runtime. Em ambiente com vários agentes, especialmente Docker Desktop/WSL, esse limite pode ser excedido antes do gateway chegar a ready.

O `entrypoint.sh` aplica patch em `/app/dist` usando:

```env
OPENCLAW_MODEL_RUNTIME_BUILD_TIMEOUT_MS=120000
```

O valor padrão documentado é 120000 ms.

O patch:

- é aplicado a cada boot;
- procura o bundle preparado do model runtime;
- valida se o valor é inteiro;
- tolera mudança do hash do chunk via fallback por grep;
- emite aviso se o símbolo não for encontrado.

Como a correção vive no `entrypoint.sh`, alterações relacionadas exigem rebuild da imagem.

## 10. Dockerfile

O Dockerfile:

- instala dependências;
- pode instalar Ollama;
- pode instalar LM Studio;
- instala CLIs;
- instala OpenClaw condicionalmente;
- instala Hermes condicionalmente;
- instala middlewares.

OpenClaw é clonado, usa `pnpm`, instala dependências, executa build e cria wrapper `/usr/local/bin/openclaw`.

Hermes é clonado, usa Python/venv, fica em `/opt/hermes-agent` e persiste dados em `/root/.hermes`.

CLIs customizadas citadas:

- `gog`;
- `goplaces`;
- `wacli`.

### Adicionar uma CLI

Padrão documentado:

```dockerfile
ARG MEUBIN_VERSION=1.0.0

RUN curl -fL "https://github.com/org/meubin/releases/download/v${MEUBIN_VERSION}/meubin_linux_amd64.tar.gz" \
    | tar -xzO meubin > /usr/local/bin/meubin \
 && chmod +x /usr/local/bin/meubin
```

Depois: commit, pull, rebuild e recreate.

### Adicionar um MCP

Criar, por exemplo:

`middleware/seu_mcp.py`

Registrar no `entrypoint.sh`:

```bash
register_mcp seu-server '{"command":"/caminho/binario","args":["arg1"],"env":{"VAR":"val"}}'
```

Depois:

```bash
git pull
docker compose up -d --force-recreate
```

Rebuild é necessário se a mudança afetar Dockerfile, `entrypoint.sh` ou middleware incorporado à imagem.
