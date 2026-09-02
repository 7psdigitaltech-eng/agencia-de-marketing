# 05 — Regras de Consulta e Evolução

## 1. Função deste repositório

`7psdigitaltech-eng/agencia-de-marketing` é a memória documental oficial do nosso projeto de agência de marketing com IA.

Ele deve concentrar:

- contexto consolidado;
- decisões do projeto;
- arquitetura adotada;
- configurações relevantes;
- aprendizados do tutorial;
- diferenças em relação ao projeto-fonte;
- problemas encontrados;
- soluções confirmadas;
- estado atual;
- próximos passos relevantes.

## 2. Fonte externa de referência

Repositório técnico do tutorial:

`ericorenato/vibestack-openclaw`

A fonte externa deve ser consultada quando a dúvida depender de:

- implementação exata;
- versão atual;
- nome de tool;
- assinatura/parâmetros de tool;
- variável `.env`;
- path;
- comportamento de middleware;
- Dockerfile;
- docker-compose;
- entrypoint;
- agente/template atual;
- bug ou correção recente.

## 3. Protocolo obrigatório para responder dúvidas

### Etapa 1 — Classificar a dúvida

Identificar se ela pertence a:

- arquitetura;
- Docker/VPS;
- OpenClaw;
- Hermes;
- Ollama/LM Studio;
- agentes;
- subagentes;
- cron;
- Meta Ads;
- Google Ads;
- mídia;
- B2;
- Higgsfield;
- AtlasCloud;
- WhatsApp/Evolution;
- MCP/middleware;
- segurança/alçada;
- troubleshooting;
- decisão específica do nosso projeto.

### Etapa 2 — Consultar este repositório

Buscar primeiro a resposta na documentação local.

Arquivos principais:

- `docs/00-contexto-mestre.md`
- `docs/01-arquitetura-infraestrutura.md`
- `docs/02-agentes-e-fluxos.md`
- `docs/03-mcps-integracoes.md`
- `docs/04-operacao-seguranca-troubleshooting.md`
- `docs/estado-atual.md`

### Etapa 3 — Validar no projeto-fonte quando necessário

Se houver dependência de implementação, consultar o `vibestack-openclaw` atual.

Não assumir que um detalhe congelado nesta documentação continua verdadeiro indefinidamente.

### Etapa 4 — Responder por nível de certeza

Sempre distinguir quando relevante:

- **Confirmado no nosso repositório**;
- **Confirmado no código-fonte atual**;
- **Decisão do nosso projeto**;
- **Configuração opcional**;
- **Inferência/hipótese**;
- **Sugestão de melhoria**.

## 4. Hierarquia de autoridade

Em caso de conflito:

1. decisão explícita mais recente do usuário para o nosso projeto;
2. estado/configuração real do nosso ambiente, quando comprovado;
3. código atual do `ericorenato/vibestack-openclaw` para comportamento da stack-base;
4. documentação deste repositório;
5. contexto histórico do tutorial;
6. conhecimento geral/inferência.

A divergência deve ser registrada em vez de escondida.

## 5. Regra de não invenção

Nunca inventar:

- tools;
- parâmetros;
- variáveis `.env`;
- portas;
- caminhos;
- credenciais;
- comportamento de serviços;
- suporte de provider/modelo;
- capacidades de MCP;
- status de execução.

Quando não estiver confirmado, dizer que precisa ser verificado.

## 6. Regra de segurança

Nunca registrar no GitHub público:

- access tokens;
- API keys;
- refresh tokens;
- passwords;
- instance tokens;
- dados pessoais de audiences;
- secrets de gateway;
- credenciais de B2;
- segredos de OAuth.

Documentar apenas nomes das variáveis e procedimento seguro de configuração.

## 7. Regra para mudanças de arquitetura

Antes de alterar algo, perguntar internamente:

1. existe suporte na arquitetura atual?
2. dá para encaixar a mudança sem criar sistema paralelo?
3. qual arquivo/configuração é responsável?
4. afeta persistência?
5. exige rebuild ou apenas recreate?
6. cria nova superfície de segurança?
7. altera alçada/aprovação?
8. altera custos?
9. precisa de migração/rollback?

## 8. Registro de decisões

Quando uma decisão relevante for tomada, registrar pelo menos:

- data;
- decisão;
- motivo;
- impacto;
- arquivos/serviços afetados;
- status: proposta, aprovada, implementada, revertida ou substituída.

Decisões que mudem o comportamento do projeto devem também refletir em `docs/estado-atual.md`.

## 9. Registro de problemas

Para problemas técnicos, registrar quando relevante:

- sintoma;
- ambiente;
- logs/erro;
- hipótese;
- diagnóstico confirmado;
- correção;
- comando executado;
- resultado;
- necessidade de rollback;
- referência ao commit/arquivo do projeto-fonte.

## 10. Aprendizados do tutorial

Conforme novos trechos do tutorial forem discutidos:

1. identificar se o conteúdo é explicação geral ou comportamento real do repositório;
2. validar no repositório-fonte quando o ponto for técnico;
3. documentar no arquivo temático adequado;
4. registrar decisão própria separadamente se optarmos por adaptar o tutorial;
5. não confundir o projeto do autor com o nosso projeto.

## 11. Relação entre os dois repositórios

### `ericorenato/vibestack-openclaw`

Função:

- referência técnica;
- exemplo do vídeo/tutorial;
- fonte de implementação da stack-base.

Não é o nosso repositório de trabalho.

### `7psdigitaltech-eng/agencia-de-marketing`

Função:

- documentação oficial;
- memória do nosso projeto;
- decisões;
- contexto;
- estado atual;
- futuras adaptações e implementação própria.

## 12. Política de evolução

A documentação não deve permanecer como simples cópia do tutorial.

À medida que o projeto evoluir, separar claramente:

- **BASE ORIGINAL** — como funciona o `vibestack-openclaw`;
- **NOSSA DECISÃO** — como decidimos implementar;
- **ESTADO REAL** — o que efetivamente está instalado/configurado;
- **PENDÊNCIA** — o que ainda falta decidir ou executar.

Essa separação evita assumir que algo mostrado no vídeo já foi implementado no nosso ambiente.
