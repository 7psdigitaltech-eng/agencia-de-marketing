# 02 — Agentes e Fluxos

## 1. Estrutura de agentes

A pasta `agency/` do repositório-fonte contém templates para seis agentes principais:

1. Diretor
2. Analista
3. Estrategista
4. Copywriter
5. Criativo
6. Gestor de Tráfego

Cada agente pode usar arquivos como:

- `IDENTITY.md` — quem o agente é;
- `SOUL.md` — personalidade e princípios;
- `USER.md` — com quem fala, idioma e tom;
- `TOOLS.md` — ferramentas permitidas e regras;
- `AGENTS.md` — papel operacional, fluxo e limites.

Os templates usam placeholders `{{...}}`.

## 2. Diretor 🎯

Função:

- porta única de comunicação com o humano;
- recebe demandas;
- roteia e delega;
- consolida e devolve resultados;
- intermedeia aprovações fora da alçada.

O Diretor não deve executar diretamente ações de Meta Ads quando o desenho de governança atribui isso ao Gestor.

Fluxo principal:

```text
Humano → Diretor
```

## 3. Analista 📊

Função:

- leitura de dados de mídia;
- coleta de números;
- insights;
- relatórios.

Deve ser essencialmente read-only.

Exemplos de métricas:

- campanhas;
- spend;
- impressions;
- clicks;
- conversions;
- CPA.

O Analista não é o executor e, quando configurado para papel estritamente analítico, não deve tomar ações mutáveis.

## 4. Estrategista ♟️

Função:

- interpretar números;
- decidir ações;
- trabalhar dentro de alçada;
- escalar ao Diretor quando ultrapassar a alçada.

Pode delegar para:

- Analista;
- Copywriter;
- Criativo;
- Gestor.

A decisão deve anteceder a execução do Gestor.

## 5. Copywriter ✍️

Função:

- criar variações de copy;
- headlines;
- corpos de anúncio;
- CTAs;
- adaptar tom ao produto/oferta.

É normalmente acionado a partir de briefing ou decisão estratégica.

## 6. Criativo 🎬

Função:

- produção de imagem;
- produção de vídeo;
- edição;
- preparação de mídia para plataformas de anúncio.

Pode usar:

- `media-editor`;
- Higgsfield;
- AtlasCloud;
- Backblaze B2;
- seeds;
- soul-id;
- geração de mídia;
- edição;
- validação;
- finalização.

## 7. Gestor de Tráfego 🛠️

É o agente de execução de mídia paga.

Pode, quando autorizado:

- criar;
- editar;
- pausar;
- retomar;
- duplicar;
- executar outras ações suportadas pelas tools reais.

### Regra de governança

O Gestor não deve agir autonomamente.

Recebe ordem:

- da Estrategista quando a ação está dentro da alçada;
- do Diretor quando a ação exige aprovação humana.

Não criar cron autônomo para o Gestor. Isso pode burlar o processo de aprovação e gerar gastos sem revisão.

## 8. Alçadas

Placeholders documentados:

### `{{ALCADA_BUDGET_PCT}}`

Percentual de alteração de budget que a Estrategista pode aprovar/executar sem nova aprovação humana, conforme regras definidas pelo projeto.

### `{{ALCADA_GASTO_DIA}}`

Teto de gasto incremental diário permitido sem aprovação humana.

Quando a ação ultrapassa a alçada:

```text
Estrategista
   ↓
Diretor
   ↓
Humano aprova
   ↓
Gestor executa
```

## 9. Placeholders dos agentes

- `{{DONO}}` — humano decisor.
- `{{DONO_EMAIL}}` — e-mail do dono.
- `{{CANAL}}` — canal principal, como WhatsApp ou Telegram.
- `{{PRODUTO_1}}` — produto principal.
- `{{TOM_PRODUTO_1}}` — tom do produto principal.
- `{{PRODUTO_2}}` — segundo produto.
- `{{TOM_PRODUTO_2}}` — tom do segundo produto.
- `{{ALCADA_BUDGET_PCT}}` — alçada percentual.
- `{{ALCADA_GASTO_DIA}}` — alçada financeira.
- `{{PESSOA_DA_MARCA}}` — rosto fixo da marca, opcional.
- `{{SLUG_DA_PESSOA}}` — slug para seed/soul-id.

## 10. Subagentes

Configuração documentada para permitir spawn cruzado:

```bash
openclaw config set agents.defaults.subagents.maxSpawnDepth 2
openclaw config set agents.defaults.subagents.allowAgents '["*"]'
openclaw config set agents.defaults.subagents.announceTimeoutMs 300000
```

### Significado

`maxSpawnDepth=2`

Permite estrutura como:

```text
orquestrador → agente → trabalhador
```

`allowAgents=["*"]`

Permite que agentes spawnem outros agentes.

`announceTimeoutMs=300000`

Define cinco minutos para entrega/anúncio do resultado do subagente.

Exemplo de registro em `agents.list`:

```json
{
  "id": "analista",
  "name": "Analista",
  "workspace": "/root/.openclaw/workspace/analista",
  "agentDir": "/root/.openclaw/agents/analista/agent"
}
```

Agentes herdam configurações de `agents.defaults` quando aplicável.

## 11. `sessions_spawn`

Ponto crítico do contexto-base:

`sessions_spawn` é bloqueante no runtime documentado.

Fluxo esperado do agente pai:

1. chamar `sessions_spawn`;
2. aguardar o tool-result;
3. processar o resultado;
4. continuar no mesmo turno.

Não usar `sessions_yield` nessa versão, pois ela não possui essa função no contexto documentado.

Uso incorreto pode gerar problemas como:

`Subagent announce give up`

Sempre verificar o repositório-fonte/versão atual antes de assumir que essa limitação continua igual.

## 12. Cron / Scheduler

OpenClaw possui scheduler interno.

Jobs ficam documentados em:

`/root/.openclaw/cron/jobs.json`

Persistem em volume e sobrevivem a restart/down.

### Exemplo de Analista

```bash
openclaw cron add \
  --name "Analista — leitura periódica" \
  --at "6h" \
  --tz "America/Sao_Paulo" \
  --session isolated \
  --agent analista \
  --keep-after-run \
  --message "Use meta-ads para ler as campanhas ativas."
```

### Padrões recomendados

- Analista: pode ter leitura periódica.
- Diretor: pode gerar relatório diário.
- Estrategista: pode executar passada periódica de decisão, desde que respeite governança.
- Copywriter: normalmente reativo a briefing.
- Criativo: normalmente reativo a briefing.
- Gestor: **não deve ter cron autônomo de execução**.

## 13. Sessão do cron

Para agentes que não são o agente default:

```text
--session isolated
```

Evitar `--session main` para agente não-default, pois pode ocorrer:

`sessionTarget "main" is only valid for the default agent`

Se o Diretor for o agente default, ele pode usar `main` quando apropriado.

## 14. Padrão Coletor → Auditor

Arquitetura recomendada para automação com governança:

```text
Coletor
  ↓
Snapshots
  ↓
Analista
  ↓
Insights
  ↓
Estrategista
  ↓
Recommendations
  ↓
Aprovador
  ↓
Approved / Rejected
  ↓
Executor
  ↓
Executions
  ↓
Auditor
  ↓
Audit
```

Princípios:

1. Coletor é read-only.
2. Estrategista propõe.
3. Humano aprova quando necessário.
4. Executor executa.
5. Apenas Executor utiliza mutating tools dentro do desenho governado.
6. Executor precisa de autorização válida.
7. Catálogo de ações pode ser restrito.
8. Memory compartilhada é preferível a carregar todo estado no prompt.
9. Snapshots e decisões persistem.
10. Ações devem ser auditáveis.

Essa arquitetura é composta na UI/OpenClaw e nos prompts/configurações; não deve ser assumida como hardcoded dentro do MCP.
