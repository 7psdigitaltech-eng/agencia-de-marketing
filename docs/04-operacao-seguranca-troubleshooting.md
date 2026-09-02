# 04 — Operação, Segurança e Troubleshooting

## 1. Atualização da stack

Na VPS:

```bash
cd ~/vibestack-openclaw
git pull
docker compose build
docker compose up -d --force-recreate openclaw-vibestack
```

Nem sempre rebuild é necessário.

### Rebuild normalmente necessário quando mudam

- `Dockerfile`;
- `entrypoint.sh`;
- arquivos de `middleware/` incorporados à imagem;
- correções que patcham `/app/dist` no boot e dependem do novo `entrypoint.sh`.

### Pode dispensar rebuild quando mudam apenas

- `.env.example`;
- `docker-compose.yml`, dependendo da alteração;
- configuração externa não incorporada à imagem.

Sempre conferir o diff/README do repositório-fonte antes de decidir.

## 2. Atualização da versão do OpenClaw

Exemplo:

```env
OPENCLAW_REF=v1.2.3
```

Pode apontar para tag, branch ou commit, conforme suporte do Dockerfile atual.

Depois:

```bash
docker compose build --no-cache
docker compose up -d
```

## 3. Segurança operacional — princípios

### Meta Ads

- criações começam pausadas;
- duplicações começam pausadas;
- ativação deve ser explícita;
- deletes exigem confirmação/force conforme tool;
- dados de audience sensíveis devem ser tratados de acordo com o hashing previsto pelo middleware.

### Agentes

- Analista não escreve em plataformas;
- Estrategista decide;
- Gestor executa;
- Diretor intermedeia humano e aprovação;
- Gestor não deve possuir cron autônomo de execução.

### WhatsApp

- usar whitelist quando possível;
- ignorar grupos/status quando esse tráfego não fizer parte do caso de uso;
- sessões são por contato no fluxo documentado;
- não expor tokens em mensagens, prompts ou logs compartilhados.

### MCPs e credenciais

- credenciais ficam no `.env`/configuração segura;
- não colocar secrets em prompts;
- não versionar tokens;
- não inventar credenciais;
- diferenciar MCP registrado de MCP funcional: registro sem credencial não garante funcionamento.

## 4. Governança de execução

Fluxo preferencial:

```text
Dados → análise → estratégia → aprovação → execução → auditoria
```

Ações mutáveis devem ter:

- origem identificável;
- autorização válida;
- escopo definido;
- resultado registrado;
- possibilidade de auditoria.

Ações que ultrapassem alçada devem voltar ao humano antes da execução.

## 5. Debug — Meta Ads

Exemplos documentados:

```bash
docker compose exec openclaw-vibestack meta auth status
```

```bash
docker compose exec openclaw-vibestack meta --output json ads campaign list
```

## 6. Debug — MCPs

OpenClaw:

```bash
docker compose exec openclaw-vibestack openclaw mcp list
```

Hermes:

```bash
docker compose exec openclaw-vibestack hermes mcp list
```

## 7. Debug — Hermes

Logs:

```bash
docker compose logs openclaw-vibestack | grep hermes
```

Dashboard/logs auxiliares:

```bash
docker compose exec openclaw-vibestack tail -f /var/log/hermes-web.log
```

```bash
docker compose exec openclaw-vibestack tail -f /var/log/hermes-web-socat.log
```

## 8. Debug — WhatsApp bridge

```bash
docker compose exec openclaw-vibestack tail -f /var/log/whatsapp-bridge.log
```

## 9. Erro: `prepared model runtime publication timed out`

### Sintoma

Gateway não chega a ready quando existem vários agentes, especialmente em Docker Desktop/WSL.

Pode deixar a TUI sem os agentes disponíveis.

### Causa documentada no snapshot atual

OpenClaw 2026.7.x possui timeout hardcoded de aproximadamente 30 segundos para publicação de cada prepared model runtime.

### Correção incorporada ao projeto-fonte

`entrypoint.sh` aplica patch usando:

```env
OPENCLAW_MODEL_RUNTIME_BUILD_TIMEOUT_MS=120000
```

Se ainda for insuficiente, pode-se aumentar o valor no `.env` e recriar o serviço.

Exemplo:

```bash
docker compose up -d --force-recreate openclaw-vibestack
```

Verificar logs do patch:

```bash
docker compose logs openclaw-vibestack | grep "model runtime timeout"
```

### Atenção

Se o `entrypoint.sh` foi atualizado via `git pull`, a correção nova pode exigir rebuild da imagem para entrar no container.

## 10. Porta 18789 em uso

Se outro processo estiver usando a porta do gateway, alterar `OPENCLAW_GATEWAY_PORT` no `.env` e recriar conforme configuração atual.

Antes disso, verificar se o problema realmente é conflito de porta e não falha do processo principal.

## 11. Hermes sobe mas completions falham

Verificar:

1. API health;
2. `HERMES_API_SERVER_KEY`;
3. provider configurado;
4. modelo configurado;
5. conectividade com provider;
6. logs do Hermes.

O build não configura automaticamente provider/modelo.

## 12. WhatsApp inbound não funciona

Checklist inicial:

1. Evolution Go está healthy?
2. Instância está criada e pareada?
3. `EVOLUTION_INSTANCE_TOKEN` está correto?
4. bridge subiu?
5. webhook interno aponta para `http://openclaw-vibestack:8765/webhook`?
6. `WA_BRIDGE_AGENT` aponta para backend instalado?
7. no modo Hermes, a chave de API necessária está configurada?
8. whitelist permite o número?
9. grupos/status estão sendo ignorados por configuração?
10. logs do bridge mostram erro de upstream?

## 13. MCP aparece registrado, mas tool falha

Verificar:

1. credenciais;
2. variáveis `.env`;
3. dependências/binários dentro do container;
4. paths do middleware;
5. autenticação do serviço externo;
6. assinatura atual da tool;
7. logs;
8. conectividade externa.

Registro do MCP não prova que a integração externa está autenticada.

## 14. Diagnóstico por camada

Antes de sugerir correção, classificar o erro.

### Docker

- build falha;
- container reinicia;
- porta/mount;
- recursos insuficientes.

### Volume

- dados somem;
- mount denied;
- path incorreto;
- permissões.

### `.env`

- variável ausente;
- valor inválido;
- segredo vazio;
- path incompatível com SO.

### OpenClaw

- gateway;
- agents;
- subagents;
- cron;
- MCP registration;
- prepared model runtime.

### Hermes

- provider;
- model;
- API;
- approvals;
- dashboard.

### MCP / Middleware

- servidor registrado mas indisponível;
- tool inexistente;
- assinatura mudou;
- dependência quebrada.

### Serviço externo

- autenticação;
- rate limit;
- licença;
- API indisponível;
- permissões da conta.

### WhatsApp

- Evolution;
- QR/instância;
- webhook;
- bridge;
- whitelist;
- sessão.

### Meta Ads

- access token;
- account ID;
- permissões;
- status pausado;
- regra de force/delete.

### Google Ads

- developer token;
- OAuth;
- customer/login customer IDs;
- GAQL/mutate.

### B2 / mídia

- credenciais;
- bucket;
- endpoint;
- key/path;
- ffmpeg;
- arquivo local finalizado.

## 15. Regra para comandos futuros

Toda orientação operacional deve indicar onde o comando deve rodar:

- **host/local**;
- **VPS**;
- **dentro do container**;
- **navegador/UI**.

Não misturar contextos de execução.

## 16. Regra para alterações de código

Ao propor alteração, indicar exatamente quais arquivos são afetados.

Arquivos frequentes:

- `Dockerfile`;
- `docker-compose.yml`;
- `.env.example`;
- `entrypoint.sh`;
- `middleware/*.py`;
- `agency/<agente>/*.md`;
- configurações persistidas do OpenClaw/Hermes.

Preservar a arquitetura atual sempre que possível e explicar quando uma mudança cria uma arquitetura paralela.
