# 07 — Execução Local no Windows

## Objetivo

Executar a stack `vibestack-openclaw` localmente no Windows para testes, sem VPS e sem túnel SSH.

## 1. O que não é necessário no modo local

Para o teste local no Windows, não são necessários:

- VPS;
- IP público;
- cadastro da chave SSH em provedor;
- `ssh root@IP_DA_VPS`;
- túnel `ssh -N -L ...` para acessar o OpenClaw.

A chave SSH criada pode ser mantida para uso futuro, mas não participa do teste local.

## 2. Pré-requisitos confirmados

Ambiente já confirmado em 2026-09-02/03:

- Git 2.55.0.windows.3;
- OpenSSH for Windows 9.5p1;
- GNU Bash 5.3.15 via Git for Windows;
- chave SSH local criada (não necessária para execução local);
- Docker CLI 29.7.2 instalado;
- Docker Compose v5.5.0 instalado.

Ainda deve ser confirmado antes do build:

- Docker Desktop aberto e Engine ativo;
- `docker info` retornando a seção Server sem erro;
- memória suficiente alocada ao Docker Desktop.

O README do projeto original alerta que o build do OpenClaw é pesado; abaixo de aproximadamente 8 GB disponíveis ao Docker podem ocorrer erros de OOM/exit 137. Aproximadamente 12 GB é uma configuração confortável quando o computador dispõe dessa memória.

## 3. Terminal

No Windows, o projeto original recomenda executar o instalador e comandos bash no **Git Bash ou WSL**, não diretamente no PowerShell/CMD.

O PowerShell pode continuar sendo usado para tarefas do Windows e para verificar Docker/Git. Para o `install.sh`, preferir Git Bash ou invocar explicitamente o Bash do Git a partir do PowerShell.

Exemplo equivalente no PowerShell:

```powershell
curl.exe -fsSL https://raw.githubusercontent.com/ericorenato/vibestack-openclaw/main/install.sh | & "C:\Program Files\Git\bin\bash.exe"
```

## 4. Verificar Docker

No PowerShell:

```powershell
docker --version
docker compose version
docker info
```

Se `docker` não for reconhecido, instalar o Docker Desktop.

Se `docker --version` e `docker compose version` funcionarem, mas `docker info` falhar por não conseguir conectar ao daemon/engine, o CLI está instalado porém o Engine está parado. Abrir o Docker Desktop e aguardar o Engine ficar pronto.

## 5. Pasta recomendada

Para manter o teste organizado:

```powershell
New-Item -ItemType Directory -Force -Path C:\Projetos
Set-Location C:\Projetos
```

Não é obrigatório usar `C:\Projetos`; é apenas uma convenção do nosso ambiente.

## 6. Clonar a stack original

A stack de execução vem do repositório-fonte:

```text
https://github.com/ericorenato/vibestack-openclaw
```

No Git Bash:

```bash
cd /c/Projetos
git clone https://github.com/ericorenato/vibestack-openclaw.git
cd vibestack-openclaw
```

O repositório `7psdigitaltech-eng/agencia-de-marketing` continua sendo nossa memória/documentação e não substitui o repositório da stack.

## 7. Forma recomendada de instalação local

Dentro de `vibestack-openclaw`, no Git Bash:

```bash
./install.sh
```

O instalador original:

- detecta o Windows/Docker Desktop;
- cria/atualiza `.env`;
- resolve data dirs dentro do HOME do usuário, evitando `/root/...` no Windows;
- pergunta qual backend local instalar (Ollama, LM Studio ou ambos);
- gera segredos vazios necessários;
- cria diretórios persistentes;
- normaliza `entrypoint.sh` para LF;
- executa `docker compose build`;
- para antes do `docker compose up -d`.

Para um primeiro teste, usar apenas um backend local reduz consumo e complexidade. A base original usa Ollama como padrão (`INSTALL_OLLAMA=true`, `INSTALL_LMSTUDIO=false`).

## 8. Subir a stack

Ao final bem-sucedido do `install.sh`, ainda dentro da pasta do projeto:

```bash
docker compose up -d
```

Verificar containers:

```bash
docker compose ps
```

Ver logs do container principal:

```bash
docker compose logs -f openclaw-vibestack
```

Sair da visualização de logs com `Ctrl+C`; isso não para o container.

## 9. Configuração inicial do OpenClaw

O README original exige um wizard inicial:

```bash
docker compose exec openclaw-vibestack openclaw configure
```

Depois da configuração:

```bash
docker compose up -d --force-recreate openclaw-vibestack
```

Listar MCPs:

```bash
docker compose exec openclaw-vibestack openclaw mcp list
```

## 10. Acesso local

Sem VPS, não há túnel SSH. Se a stack estiver saudável, acessar diretamente no navegador do Windows:

```text
http://127.0.0.1:18789
```

O token solicitado pela UI é `OPENCLAW_GATEWAY_TOKEN`, armazenado no `.env` local. Não salvar esse segredo no repositório público.

## 11. Integrações opcionais

Para o primeiro teste não é obrigatório configurar todas as integrações externas.

Podem ficar sem credenciais inicialmente:

- Meta Ads;
- Google Ads;
- Backblaze B2;
- Higgsfield;
- AtlasCloud;
- Evolution Go/WhatsApp.

Alguns MCPs podem aparecer registrados, mas operações que dependem de credenciais falharão até a configuração correspondente.

## 12. Sequência recomendada do nosso teste

```text
1. Confirmar Docker Desktop
2. Confirmar docker + docker compose + docker info
3. Trabalhar a partir de C:\Projetos
4. Rodar o instalador via Git Bash ou Bash invocado pelo PowerShell
5. Rodar ./install.sh / bootstrap
6. Escolher inicialmente Ollama (LM Studio desligado)
7. Subir com docker compose up -d
8. Conferir docker compose ps e logs
9. Rodar openclaw configure
10. Recriar container principal
11. Abrir http://127.0.0.1:18789
12. Só depois configurar integrações externas
```

## 13. Regra de diagnóstico

Se algo falhar, não avançar cegamente. Identificar primeiro se o erro está em:

- Docker Desktop/Engine;
- memória do Docker;
- paths/volumes do Windows;
- `.env`;
- build da imagem;
- container principal;
- OpenClaw;
- backend local (Ollama/LM Studio);
- MCP específico.

Registrar no repositório o estado real somente após cada etapa ser confirmada na máquina.

## 14. Diagnóstico real observado — 2026-09-03

O bootstrap foi executado a partir do PowerShell chamando explicitamente `curl.exe` e o Bash do Git. O script:

- detectou Windows corretamente (`MINGW64_NT-10.0-19045`);
- detectou Git 2.55.0.windows.3;
- detectou Docker 29.7.2;
- detectou Docker Compose v5.5.0;
- parou corretamente porque `docker info` não conseguiu falar com o daemon;
- exibiu a orientação do próprio instalador: abrir o Docker Desktop, esperar ficar `running` e executar novamente.

Como o PowerShell estava em `C:\WINDOWS\system32`, o bootstrap clonou automaticamente o repositório em:

```text
C:\WINDOWS\system32\vibestack-openclaw
```

Esse local não deve ser adotado como pasta oficial de trabalho. Para o nosso teste local, usar:

```text
C:\Projetos\vibestack-openclaw
```

Antes de repetir o instalador, confirmar `docker info` funcionando. Depois remover o clone acidental de `C:\WINDOWS\system32` e repetir a instalação a partir de `C:\Projetos`.