# Changelog

Todas as mudanças relevantes de contexto, arquitetura, decisões e estado do projeto devem ser registradas aqui.

## 2026-09-02 — Base inicial

### Adicionado

- definição de `7psdigitaltech-eng/agencia-de-marketing` como repositório oficial de documentação do projeto;
- definição de `ericorenato/vibestack-openclaw` como repositório-fonte do tutorial e referência técnica;
- snapshot de referência do projeto-fonte: `f1d5329c4f30ac28cb10a8a93bf3b41e43147217`;
- visão geral da arquitetura da agência de marketing com IA;
- documentação de Docker, persistência, OpenClaw, Hermes, Ollama e LM Studio;
- documentação dos seis agentes: Diretor, Analista, Estrategista, Copywriter, Criativo e Gestor de Tráfego;
- regras de alçada e governança;
- documentação de subagentes, `sessions_spawn` e cron;
- documentação dos MCPs Meta Ads, Google Ads, Media Editor, WhatsApp, Higgsfield e AtlasCloud;
- documentação de Backblaze B2 e Evolution Go;
- segurança operacional e troubleshooting;
- patch do timeout de prepared model runtime do OpenClaw 2026.7.x;
- protocolo obrigatório para consulta deste repositório antes de responder dúvidas técnicas;
- separação entre base original, decisão do nosso projeto, estado real e pendências.

### Estado

A stack do tutorial está documentada, mas sua instalação/configuração no nosso ambiente ainda não foi confirmada. Nenhum item operacional deve ser marcado como implementado sem evidência prática.
