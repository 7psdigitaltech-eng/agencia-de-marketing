# Agência de Marketing com IA — Base de Conhecimento

Este repositório é a **base oficial de contexto, decisões e documentação operacional** do projeto de agência de marketing com IA acompanhado neste chat.

## Fonte técnica de referência

Repositório-base do tutorial/vídeo:

- `ericorenato/vibestack-openclaw`
- https://github.com/ericorenato/vibestack-openclaw
- snapshot de referência validado em 2026-09-02: commit `f1d5329c4f30ac28cb10a8a93bf3b41e43147217`

O repositório-fonte é a autoridade para comportamento real de código, nomes de tools, variáveis, paths, versões e detalhes de implementação. Se houver divergência entre esta documentação e o código atual do `vibestack-openclaw`, prevalece o código atual do repositório-fonte e a divergência deve ser registrada aqui.

## Regra de uso deste repositório

Antes de responder dúvidas técnicas sobre este projeto, deve-se:

1. consultar primeiro este repositório (`7psdigitaltech-eng/agencia-de-marketing`);
2. verificar se a resposta já está documentada;
3. quando a resposta depender da implementação real da stack, conferir também `ericorenato/vibestack-openclaw`;
4. não inventar funcionalidades, tools, variáveis `.env`, paths ou comportamentos;
5. diferenciar claramente fato confirmado, configuração opcional, decisão do nosso projeto e sugestão de melhoria;
6. registrar aqui novas decisões, mudanças de arquitetura, aprendizados relevantes e estado atual do projeto.

## Estrutura da documentação

- [`docs/00-contexto-mestre.md`](docs/00-contexto-mestre.md) — visão consolidada do projeto e regras de interpretação.
- [`docs/01-arquitetura-infraestrutura.md`](docs/01-arquitetura-infraestrutura.md) — Docker, serviços, portas, persistência, OpenClaw, Hermes, Ollama e LM Studio.
- [`docs/02-agentes-e-fluxos.md`](docs/02-agentes-e-fluxos.md) — Diretor, Analista, Estrategista, Copywriter, Criativo, Gestor, alçadas, subagentes e cron.
- [`docs/03-mcps-integracoes.md`](docs/03-mcps-integracoes.md) — Meta Ads, Google Ads, mídia, B2, Higgsfield, AtlasCloud e WhatsApp.
- [`docs/04-operacao-seguranca-troubleshooting.md`](docs/04-operacao-seguranca-troubleshooting.md) — instalação, atualização, segurança operacional, debug e troubleshooting.
- [`docs/05-regras-de-consulta-e-evolucao.md`](docs/05-regras-de-consulta-e-evolucao.md) — protocolo para responder dúvidas e evoluir o projeto.
- [`docs/estado-atual.md`](docs/estado-atual.md) — snapshot do estado atual desta base.
- [`CHANGELOG.md`](CHANGELOG.md) — histórico das mudanças relevantes.

## Arquitetura conceitual principal

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
      MCPs / APIs
          ↓
META ADS / GOOGLE ADS / MÍDIA / WHATSAPP / STORAGE / MODELOS
```

Princípio central: separar **raciocínio, dados, ferramentas, execução, aprovação, memória e infraestrutura**.
