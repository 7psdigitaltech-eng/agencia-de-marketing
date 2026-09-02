# 00 — Contexto Mestre

## 1. Objetivo desta documentação

Este repositório registra a base de conhecimento do projeto de agência de marketing com IA acompanhado a partir do tutorial/repositório `ericorenato/vibestack-openclaw`.

A documentação deve permitir:

- instalar e operar a stack;
- entender a arquitetura;
- configurar agentes e fluxos;
- configurar OpenClaw, Hermes, Ollama e LM Studio;
- configurar MCPs e integrações;
- operar Meta Ads e Google Ads com segurança;
- configurar WhatsApp;
- produzir e preparar mídia;
- adicionar CLIs e MCPs;
- configurar subagentes e cron;
- diagnosticar erros;
- evoluir a arquitetura sem perder as regras operacionais;
- responder dúvidas futuras com base documental e verificação no repositório-fonte.

## 2. Fonte técnica principal

Fonte primária:

`https://github.com/ericorenato/vibestack-openclaw`

Snapshot validado em 2026-09-02:

`f1d5329c4f30ac28cb10a8a93bf3b41e43147217`

Esse commit contém, entre outros pontos, o patch para aumentar o timeout de publicação do model runtime em ambientes multiagente.

### Regra de autoridade

Quando houver divergência entre esta documentação e o código atual do repositório-fonte, o código atual do `vibestack-openclaw` prevalece.

Não assumir que nomes de tools, variáveis `.env`, versões, paths ou comportamentos continuam iguais sem verificar o repositório-fonte quando a resposta depender disso.

## 3. Visão geral da stack

O `vibestack-openclaw` é uma stack Docker self-hosted voltada à operação de uma agência de tráfego/marketing por agentes de IA.

O ecossistema pode combinar:

- OpenClaw;
- Hermes Agent;
- Ollama;
- LM Studio;
- MCPs customizados;
- Meta Ads;
- Google Ads;
- edição de imagem e vídeo;
- Backblaze B2;
- Higgsfield;
- AtlasCloud;
- WhatsApp via Evolution Go;
- subagentes;
- cron/scheduler;
- storage persistente;
- execução local ou em VPS.

O projeto é uma infraestrutura completa de agentes + ferramentas + serviços + canais + persistência. Ele não deve ser tratado apenas como um MCP.

## 4. Arquitetura conceitual da agência

```text
HUMANO
  ↓
DIRETOR
  ↓
ANALISTA
  ↓
ESTRATEGISTA
  ├── COPYWRITER
  ├── CRIATIVO
  └── GESTOR DE TRÁFEGO
          ↓
        MCPs
          ↓
META ADS / GOOGLE ADS / MÍDIA / WHATSAPP / SERVIÇOS EXTERNOS
```

A separação central é entre:

- raciocínio;
- dados;
- ferramentas;
- execução;
- aprovação;
- memória;
- infraestrutura.

## 5. Componentes principais

### Container principal

Nome documentado:

`openclaw-vibestack`

Pode executar:

- OpenClaw;
- Ollama, se instalado;
- LM Studio, se instalado;
- Hermes Agent, se instalado;
- middlewares MCP;
- bridge de WhatsApp;
- CLIs auxiliares.

### Serviços auxiliares

- `evolution-go`: API de WhatsApp baseada em Go/whatsmeow, porta interna 8080.
- `postgres`: banco utilizado pelo Evolution Go.

### Portas principais

- OpenClaw gateway: `18789`
- Ollama: `11434`
- LM Studio: `1234`
- Hermes API: `8642`
- Hermes dashboard: `9119`
- Evolution Go: `8080`
- WhatsApp bridge: `8765` internamente

Em VPS, o projeto prioriza exposição em loopback e acesso via SSH tunnel.

## 6. Estrutura do repositório-fonte

```text
.
├── agency/
├── middleware/
├── postgres/
├── scripts/
├── .dockerignore
├── .env.example
├── .gitattributes
├── .gitignore
├── Dockerfile
├── README.md
├── docker-compose.yml
├── entrypoint.sh
└── install.sh
```

### `agency/`

Templates dos agentes:

- analista;
- copywriter;
- criativo;
- diretor;
- estrategista;
- gestor;
- exemplos e documentação relacionada.

Os agentes podem usar arquivos como:

- `IDENTITY.md`: identidade do agente;
- `SOUL.md`: personalidade e princípios;
- `USER.md`: contexto do usuário, idioma e tom;
- `TOOLS.md`: ferramentas permitidas e regras;
- `AGENTS.md`: papel operacional, fluxo e limites.

### `middleware/`

Arquivos documentados:

- `google_ads_auth.py`
- `google_ads_cli_mcp.py`
- `googleads_cli.py`
- `higgsfield_cli_mcp.py`
- `media_editor_mcp.py`
- `meta_ads_cli_mcp.py`
- `requirements.txt`
- `whatsapp_bridge.py`
- `whatsapp_evolution_mcp.py`

### `postgres/`

- `init-evolution-dbs.sql`

### `scripts/`

Scripts auxiliares de inicialização e status de modelos, entre outros.

## 7. Princípios operacionais

1. O Analista lê e interpreta dados objetivos, sem executar alterações.
2. A Estrategista decide dentro de sua alçada.
3. O Gestor executa alterações em mídia paga apenas quando autorizado.
4. O Diretor é a porta principal para o humano e intermediador de aprovações.
5. Criações e duplicações em mídia paga devem começar pausadas.
6. O Gestor não deve possuir cron autônomo que possa gerar gastos sem revisão.
7. Credenciais ficam no ambiente/configuração, nunca em prompts ou documentação pública.
8. Estado operacional deve preferencialmente ser persistido em memória/artefatos estruturados, não despejado integralmente no system prompt.
9. Quando possível, registrar snapshots, decisões, recomendações, execuções e auditoria.
10. Alterações devem preservar a arquitetura existente sempre que possível.

## 8. Padrão de automação recomendado

```text
Coletor
  ↓
Analista
  ↓
Estrategista
  ↓
Aprovador/Humano
  ↓
Executor
  ↓
Auditor
```

Princípios:

- Coletor: read-only.
- Analista: gera insights.
- Estrategista: propõe/decide.
- Humano: aprova ações fora da alçada.
- Executor: único agente com tools mutáveis quando aplicável.
- Auditor: registra/valida o que foi feito.

## 9. Memory > prompt

O estado operacional deve privilegiar armazenamento persistente como:

- snapshots;
- decisions;
- recommendations;
- executions;
- audit;
- shared memory.

Não concentrar todo o histórico e estado do negócio diretamente no prompt de sistema.

## 10. Regras para responder dúvidas futuras

Ao responder qualquer dúvida deste projeto:

1. consultar primeiro este repositório;
2. identificar o domínio da dúvida;
3. localizar a documentação correspondente;
4. se a resposta depender de implementação real, verificar o `vibestack-openclaw` atual;
5. distinguir:
   - comportamento confirmado;
   - configuração opcional;
   - decisão específica do nosso projeto;
   - hipótese;
   - sugestão de melhoria;
6. nunca inventar tools, variáveis, paths ou recursos;
7. para comandos, informar onde executar:
   - host local;
   - VPS;
   - dentro do container;
   - navegador/interface;
8. ao diagnosticar problemas, classificar primeiro a origem provável:
   - Docker;
   - volume;
   - `.env`;
   - OpenClaw;
   - Hermes;
   - MCP;
   - middleware;
   - serviço externo;
   - WhatsApp;
   - Meta Ads;
   - Google Ads;
   - storage B2.

## 11. Objetivo final do projeto

Construir uma agência de marketing/tráfego operada com IA, mantendo governança humana e capacidade de:

- análise periódica;
- geração de estratégia;
- criação de copy;
- criação/edição de peças;
- execução controlada em mídia paga;
- WhatsApp como canal operacional;
- integração com plataformas externas;
- automações programadas;
- auditoria das ações;
- evolução gradual da stack.
