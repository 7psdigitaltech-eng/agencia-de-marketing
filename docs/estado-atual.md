# Estado Atual do Projeto

Data do snapshot: **2026-09-02**

## 1. Situação geral

O projeto está na fase de **absorção, estudo e documentação da arquitetura** apresentada no tutorial baseado em `ericorenato/vibestack-openclaw`.

Ainda não deve ser assumido que toda a stack do tutorial está instalada ou configurada no nosso ambiente. A documentação registra o funcionamento da base técnica e servirá para orientar as próximas decisões e implementações.

## 2. Repositórios definidos

### Repositório-fonte do tutorial

`ericorenato/vibestack-openclaw`

Função: referência técnica e implementação-base.

Snapshot validado em 2026-09-02:

`f1d5329c4f30ac28cb10a8a93bf3b41e43147217`

Data do commit: 2026-07-21.

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

A criação/atualização de arquivos foi testada com sucesso em 2026-09-02.

## 4. Base documental criada

Criados nesta primeira consolidação:

- `README.md`
- `docs/00-contexto-mestre.md`
- `docs/01-arquitetura-infraestrutura.md`
- `docs/02-agentes-e-fluxos.md`
- `docs/03-mcps-integracoes.md`
- `docs/04-operacao-seguranca-troubleshooting.md`
- `docs/05-regras-de-consulta-e-evolucao.md`
- `docs/06-preparacao-terminal-e-ssh.md`
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

## 7. Ambiente local confirmado

No Windows do usuário, em 2026-09-02, foi confirmado:

- Git instalado e funcional;
- OpenSSH instalado e funcional;
- Bash do Git instalado e funcional;
- PowerShell em uso como terminal principal;
- pasta `C:\Users\cristhyan\.ssh` criada;
- chave SSH `ed25519` criada corretamente em `id_ed25519` e `id_ed25519.pub`;
- chave pública visualizada com sucesso.

**Não registrar nem versionar o conteúdo da chave privada.** A chave pública pode ser usada para cadastro em VPS/provedor.

Próxima etapa local/remota: cadastrar a chave pública no provedor/VPS e validar o acesso SSH ao servidor.

## 8. O que ainda não está confirmado como implementado

Neste momento, a documentação **não comprova** que o nosso ambiente já possui:

- VPS configurada para esta agência;
- stack Docker instalada;
- OpenClaw instalado;
- Hermes instalado;
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
