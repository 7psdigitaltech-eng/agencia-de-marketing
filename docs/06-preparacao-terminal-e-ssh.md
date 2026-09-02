# 06 — Preparação do Terminal e SSH

## Objetivo

Este documento explica qual terminal deve ser usado para seguir o tutorial do `vibestack-openclaw` e diferencia o **terminal local do computador** do **shell remoto da VPS**.

Fonte técnica principal: `ericorenato/vibestack-openclaw`, README atual consultado em 2026-09-02.

## 1. O que o repositório original realmente exige

O projeto não exige uma customização visual específica de terminal. Não há requisito documentado de tema, fonte, prompt visual ou aplicativo de terminal específico.

O requisito funcional é:

- Linux/macOS: terminal normal com shell compatível;
- Windows: **Git Bash ou WSL**, e não PowerShell/CMD para executar `install.sh` e comandos bash do tutorial;
- Docker Desktop rodando no Windows/macOS quando a stack for executada localmente;
- chave SSH configurada no laptop quando a instalação for feita numa VPS;
- conexão com a VPS via `ssh root@IP_DA_VPS`;
- comandos de administração Linux executados **depois de entrar na VPS**.

O README é explícito ao dizer que, no Windows, `install.sh`, `mkdir -p`, `cp`, `openssl`, `tr` e outros comandos de shell bash devem ser usados no **Git Bash ou WSL**, não no PowerShell/CMD.

## 2. Preparação recomendada no Windows

### Opção A — Git Bash (mais simples para acompanhar o tutorial)

Instalar Git for Windows:

```powershell
winget install --id Git.Git -e
```

Depois abrir **Git Bash** pelo menu Iniciar.

Verificações úteis:

```bash
git --version
ssh -V
bash --version
```

### Opção B — WSL

No PowerShell como administrador:

```powershell
wsl --install
```

Após reiniciar e concluir a configuração da distribuição Linux, usar o terminal Ubuntu/WSL para os comandos bash.

Se Docker Desktop for usado junto com WSL, habilitar a integração em:

`Docker Desktop > Settings > Resources > WSL Integration`.

## 3. Docker Desktop

Para execução local, o README recomenda Docker Desktop no Windows/macOS.

O build do OpenClaw pode consumir bastante memória. A documentação alerta que abaixo de aproximadamente 8 GB disponíveis ao Docker podem ocorrer erros de OOM; cerca de 12 GB é uma configuração confortável.

No Docker Desktop:

`Settings > Resources > Memory`

## 4. SSH: terminal local x terminal da VPS

Há dois ambientes diferentes.

### Terminal local

É o Git Bash/WSL no computador. Exemplo:

```bash
ssh root@IP_DA_VPS
```

### Terminal remoto

Depois que o comando acima conecta, o prompt passa a representar a VPS. A partir daí, comandos como estes são executados **na VPS**:

```bash
apt-get update && apt-get upgrade -y
apt-get install -y git curl ca-certificates nano
```

Docker na VPS:

```bash
curl -fsSL https://get.docker.com | sh
docker --version
docker compose version
```

Clone do projeto na VPS:

```bash
cd ~
git clone https://github.com/ericorenato/vibestack-openclaw.git
cd vibestack-openclaw
```

## 5. Chave SSH

O repositório original estabelece como pré-requisito ter uma **SSH key configurada no laptop**, mas não apresenta, na seção consultada, um tutorial completo de geração da chave.

Como procedimento recomendado para o nosso projeto, quando ainda não existir uma chave:

```bash
ssh-keygen -t ed25519
```

Aceitar o caminho padrão normalmente cria:

```text
~/.ssh/id_ed25519
~/.ssh/id_ed25519.pub
```

Para visualizar a chave pública no Git Bash/WSL:

```bash
cat ~/.ssh/id_ed25519.pub
```

A **chave pública** (`.pub`) pode ser cadastrada no provedor da VPS. A chave privada `id_ed25519` nunca deve ser compartilhada ou enviada ao repositório.

Depois testar:

```bash
ssh root@IP_DA_VPS
```

> A geração da chave acima é recomendação operacional nossa; o requisito de usar chave SSH vem do README do projeto original.

## 6. SSH tunnel para acessar o OpenClaw

Depois que a stack estiver rodando na VPS, o gateway OpenClaw fica em loopback. No laptop:

```bash
ssh -N -L 18789:127.0.0.1:18789 root@IP_DA_VPS
```

Esse terminal deve permanecer aberto enquanto a interface for usada.

No navegador do laptop:

```text
http://127.0.0.1:18789
```

Opcionalmente, para Ollama:

```bash
ssh -N -L 11434:127.0.0.1:11434 root@IP_DA_VPS
```

## 7. Fluxo recomendado para acompanhar o tutorial

```text
WINDOWS
  ↓
Docker Desktop instalado/aberto
  ↓
Git Bash (mais simples) ou WSL
  ↓
SSH para a VPS
  ↓
Ubuntu/Debian da VPS
  ↓
Docker + projeto vibestack-openclaw
  ↓
OpenClaw/Hermes/MCPs
```

## 8. Regra prática

Antes de colar um comando do tutorial, identificar onde ele deve rodar:

- **PowerShell (Administrador):** somente tarefas específicas do Windows, como `winget` ou `wsl --install`;
- **Git Bash/WSL local:** `git`, `ssh`, scripts bash e comandos locais compatíveis;
- **VPS via SSH:** `apt`, instalação do Docker, clone, `.env`, `docker compose`, logs e configuração da stack;
- **Container:** somente comandos precedidos por algo como `docker compose exec openclaw-vibestack ...`.

Essa separação deve ser preservada nas próximas instruções do projeto.