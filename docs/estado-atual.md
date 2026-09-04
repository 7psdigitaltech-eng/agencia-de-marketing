# Estado Atual do Projeto

Data do snapshot: **2026-09-04**

## 1. Situação geral

O projeto está na fase de **preparação do ambiente local Windows** para executar a stack baseada em `ericorenato/vibestack-openclaw` sem VPS.

Ainda não deve ser assumido que toda a stack do tutorial está instalada ou configurada no nosso ambiente. A documentação registra o funcionamento da base técnica e o estado real já validado na máquina local.

## 2. Repositórios definidos

### Repositório-fonte do tutorial

`ericorenato/vibestack-openclaw`

Função: referência técnica e implementação-base.

Snapshot de referência previamente validado:

`f1d5329c4f30ac28cb10a8a93bf3b41e43147217`

### Repositório oficial do nosso projeto

`7psdigitaltech-eng/agencia-de-marketing`

Função:

- memória documental;
- decisões;
- aprendizados;
- arquitetura do nosso projeto;
- acompanhamento de implementação;
- troubleshooting;
- estado atual.

## 3. Acesso confirmado

A integração utilizada nesta conversa possui no repositório `7psdigitaltech-eng/agencia-de-marketing`:

- leitura: confirmada;
- escrita/push: confirmada;
- administração via integração: reportada como disponível pelo GitHub connector.

## 4. Base documental criada

- `README.md`
- `docs/00-contexto-mestre.md`
- `docs/01-arquitetura-infraestrutura.md`
- `docs/02-agentes-e-fluxos.md`
- `docs/03-mcps-integracoes.md`
- `docs/04-operacao-seguranca-troubleshooting.md`
- `docs/05-regras-de-consulta-e-evolucao.md`
- `docs/06-preparacao-terminal-e-ssh.md`
- `docs/07-execucao-local-windows.md`
- `docs/08-troubleshooting-docker-wsl-windows.md`
- `docs/estado-atual.md`
- `CHANGELOG.md`

## 5. Arquitetura-base absorvida

Fluxo conceitual:

```text
Humano
  ↓
Diretor
  ↓
Analista
  ↓
Estrategista
  ├── Copywriter
  ├── Criativo
  └── Gestor de Tráfego
          ↓
        MCPs
          ↓
Serviços externos
```

Tecnologias e integrações documentadas:

- Docker;
- OpenClaw;
- Hermes Agent;
- Ollama;
- LM Studio;
- Meta Ads;
- Google Ads;
- Media Editor/ffmpeg;
- Backblaze B2;
- Higgsfield;
- AtlasCloud;
- Evolution Go/WhatsApp;
- PostgreSQL;
- subagentes;
- cron.

## 6. Regras já definidas para o nosso projeto

1. Antes de responder dúvidas técnicas, consultar este repositório.
2. Quando a resposta depender da implementação real, validar também no `vibestack-openclaw`.
3. Não inventar tools, variáveis, paths ou comportamentos.
4. Separar comportamento da base original de decisão própria do nosso projeto.
5. Não armazenar secrets neste repositório público.
6. Registrar decisões e mudanças relevantes conforme avançarmos no tutorial.
7. Preservar governança humana para ações que possam gerar gasto ou mudanças relevantes.
8. Para o teste atual, usar execução **local no Windows**, sem VPS e sem túnel SSH.
9. Comandos bash do instalador devem rodar no Git Bash/WSL, não diretamente no PowerShell.

## 7. Ambiente local confirmado

No Windows do usuário, foi confirmado:

- Git `2.55.0.windows.3` instalado e funcional;
- OpenSSH for Windows `9.5p1` instalado e funcional;
- GNU Bash `5.3.15` via Git for Windows instalado e funcional;
- PowerShell em uso como terminal principal;
- chave SSH `ed25519` criada corretamente em `C:\Users\cristhyan\.ssh` (não necessária no teste local atual);
- Docker Desktop instalado;
- Docker CLI `29.7.2` funcionando;
- Docker Compose `v5.5.0` funcionando;
- WSL `2.7.12.0` com kernel `6.18.33.2-2`;
- virtualização de hardware habilitada no firmware;
- `hypervisorlaunchtype` configurado como `Auto`;
- componentes `Microsoft-Windows-Subsystem-Linux` e `VirtualMachinePlatform` habilitados.

### Estado do Docker em 2026-09-04

Após o troubleshooting de WSL/virtualização, o Docker Desktop passou a abrir normalmente e a interface exibiu **`Engine running`**.

Isso confirma visualmente que o Docker Engine iniciou. A validação final via terminal ainda deve ser feita com:

```powershell
docker info
```

Critério de aceite: `docker info` deve retornar uma seção `Server:` preenchida, sem erro de conexão com `dockerDesktopLinuxEngine`.

## 8. O que ainda não está confirmado como implementado

Neste momento, ainda não foi confirmado:

- validação terminal final do Docker Engine via `docker info`;
- repositório `vibestack-openclaw` clonado no diretório local definitivo;
- `install.sh` executado com sucesso;
- imagem Docker da stack buildada;
- containers da stack em execução;
- OpenClaw configurado;
- Hermes configurado;
- Ollama/LM Studio operacional;
- agentes configurados;
- Meta Ads autenticado;
- Google Ads autenticado;
- B2 configurado;
- Higgsfield autenticado;
- AtlasCloud configurado;
- Evolution Go/WhatsApp configurado;
- crons ativos;
- alçadas numéricas definidas;
- produtos/clientes reais configurados.

Esses itens devem mudar de status somente após confirmação prática.

## 9. Próximo princípio de trabalho

A partir deste ponto, cada nova dúvida, etapa do tutorial ou decisão deve ser tratada como uma oportunidade de:

1. consultar a base existente;
2. validar no repositório-fonte quando necessário;
3. responder;
4. registrar a nova informação/decisão quando ela alterar ou ampliar o projeto.
