# 03 — MCPs e Integrações

## 1. MCPs

Os MCPs são registrados automaticamente pelo `entrypoint.sh`.

No contexto-base:

- OpenClaw usa `openclaw mcp set`;
- Hermes incorpora servidores ao `config.yaml`.

MCPs principais documentados:

1. `meta-ads`
2. `google-ads`
3. `media-editor`
4. `whatsapp`
5. `higgsfield`
6. `atlascloud`

Um MCP pode estar registrado sem credenciais e ainda assim não funcionar corretamente até que as respectivas configurações externas sejam fornecidas.

## 2. Meta Ads MCP

Arquivo de referência:

`middleware/meta_ads_cli_mcp.py`

O middleware envolve a CLI oficial da Meta e também usa Graph API direta para funcionalidades não cobertas pela CLI.

O contexto documenta aproximadamente 70 tools, organizadas nas categorias abaixo.

### Ad Accounts

- `list_ad_accounts`
- `get_ad_account`
- `current_ad_account`

### Campaigns

- `list_campaigns`
- `get_campaign`
- `create_campaign`
- `update_campaign`
- `pause_campaign`
- `resume_campaign`
- `archive_campaign`
- `delete_campaign`

### Ad Sets

- `list_ad_sets`
- `get_ad_set`
- `create_ad_set`
- `update_ad_set`
- `pause_ad_set`
- `resume_ad_set`
- `delete_ad_set`

### Ads

- `list_ads`
- `get_ad`
- `create_ad`
- `update_ad`
- `pause_ad`
- `resume_ad`
- `delete_ad`

### Creatives

- `list_creatives`
- `get_creative`
- `create_creative`
- `create_creative_dco`
- `update_creative`
- `delete_creative`

### Insights

- `get_insights`

Parâmetros/capacidades citados:

- date preset;
- since/until;
- breakdown;
- fields;
- filtros.

### Catalogs

- `list_catalogs`
- `get_catalog`
- `create_catalog`
- `update_catalog`
- `delete_catalog`

### Pages

- `list_pages`
- `get_page`

### Datasets/Pixels

- `list_datasets`
- `get_dataset`
- `create_dataset`
- `connect_dataset`
- `disconnect_dataset`
- `assign_user_to_dataset`

### Product Sets

- `list_product_sets`
- `get_product_set`
- `create_product_set`
- `update_product_set`
- `delete_product_set`

### Product Items

- `list_product_items`
- `get_product_item`
- `create_product_item`
- `update_product_item`
- `delete_product_item`

### Product Feeds

- `list_product_feeds`
- `get_product_feed`
- `create_product_feed`
- `update_product_feed`
- `delete_product_feed`

### Custom Audiences

- `list_custom_audiences`
- `get_custom_audience`
- `create_custom_audience`
- `create_lookalike_audience`
- `add_users_to_audience`
- `remove_users_from_audience`
- `delete_custom_audience`

### Duplicação

- `duplicate_campaign`
- `duplicate_ad_set`
- `duplicate_ad`

Duplicações usam Graph API `/copies`.

Padrões documentados:

- duplicações começam `PAUSED`;
- `deep_copy=True` por padrão;
- pode usar `new_name`;
- pode usar `rename_suffix`.

### Output

Tools baseadas em CLI aceitam `output_format`:

- `json` — default;
- `table`;
- `plain`;
- `none`.

### Segurança Meta Ads

- `create_*` começa pausado;
- `duplicate_*` começa pausado;
- ativação exige `resume_*` explícito;
- deletes exigem `--force`;
- e-mails/telefones de audiences são hasheados localmente com SHA256;
- `already_hashed=True` pode ser usado quando os dados já estiverem hashados.

Variáveis principais:

```env
META_ACCESS_TOKEN=
META_AD_ACCOUNT_ID=
```

A conta pode ser informada com ou sem `act_`; o entrypoint normaliza quando necessário no comportamento documentado.

## 3. Google Ads MCP

Arquivo:

`middleware/google_ads_cli_mcp.py`

Não há uma CLI oficial equivalente à CLI Meta usada pelo projeto. O middleware usa diretamente o SDK oficial `google-ads`.

Leitura:

- GAQL.

Escrita:

- `mutate`.

Capacidades documentadas:

- campanhas;
- budgets;
- ad groups;
- anúncios RSA;
- keywords;
- insights;
- GAQL;
- search terms;
- segmentação geográfica;
- idioma;
- negativas;
- conversões;
- Keyword Planner;
- URL final;
- UTM.

Exemplos de tools citadas:

- `add_geo_language`
- `add_campaign_negative_keywords`
- `create_conversion_action`
- `list_conversion_actions`
- `keyword_ideas`
- `update_ad`
- `update_campaign`
- `get_insights`
- `gaql_search`
- `search_terms_report`

Por segurança, criações começam `PAUSED` no desenho documentado.

Credenciais:

```env
GOOGLE_ADS_DEVELOPER_TOKEN=
GOOGLE_ADS_CLIENT_ID=
GOOGLE_ADS_CLIENT_SECRET=
GOOGLE_ADS_REFRESH_TOKEN=
GOOGLE_ADS_LOGIN_CUSTOMER_ID=
GOOGLE_ADS_CUSTOMER_ID=
```

Arquivos auxiliares:

- `middleware/googleads_cli.py`
- `middleware/google_ads_auth.py`

A CLI `googleads` é usada para autenticação, leituras e escritas conforme implementação.

## 4. Media Editor MCP

Arquivo:

`middleware/media_editor_mcp.py`

Tecnologias:

- Python;
- ffmpeg;
- Backblaze B2.

Serve principalmente ao agente Criativo.

### Estrutura B2

Prefixos:

```text
inbox/
seeds/
work/
final/
requests/
meta/
```

As tools recebem chaves B2 puras, sem `b2://`.

Operações são desenhadas para idempotência. Quando `output_key` não é informado, a saída pode ser derivada de hash dos parâmetros/input em `work/<slug>/...`.

Repetir a mesma operação pode retornar:

`was_cached=true`

### Seeds / Inbox

- `list_seeds`
- `request_human_media`
- `list_inbox`
- `claim_inbox_item`
- `b2_list`
- `b2_get_info`
- `b2_upload_local`
- `b2_delete`

### Imagem

- `image_fit`
- `image_overlay`

`image_fit` suporta:

- `cover`
- `contain`
- `crop`
- `stretch`

Exemplos de formatos:

- 1:1 → `1080x1080`
- 9:16 → `1080x1920`

### Vídeo

- `video_trim`
- `video_fit`
- `video_overlay`
- `video_audio`
- `video_loop`
- `video_speed`
- `video_concat`
- `video_transcode`
- `video_extract_frame`

### Validação

- `probe`

Perfis citados:

- `meta_image_feed`
- `meta_image_story`
- `meta_video_feed`
- `meta_video_reels`

### Finalização

- `finalize_for_meta`

Transforma arquivo do B2 em arquivo local persistente em:

`/root/.openclaw/workspace/_shared/creatives/`

Esse path local pode ser usado pelo Gestor ao criar creative no Meta Ads.

Pipeline típico:

```text
list_seeds
  ↓
image_fit
  ↓
image_overlay
  ↓
probe
  ↓
finalize_for_meta
  ↓
meta-ads create_creative
```

## 5. Backblaze B2

Variáveis:

```env
B2_KEY_ID=
B2_APP_KEY=
B2_BUCKET=
B2_ENDPOINT_URL=
```

Endpoint S3-compatible.

Estrutura documentada:

- `inbox/` — uploads humanos pendentes;
- `seeds/image/` — imagens-base;
- `seeds/video/` — vídeos-base;
- `seeds/audio/` — áudios-base;
- `work/` — derivações intermediárias;
- `final/` — mídia pronta antes da finalização/localização;
- `requests/` — pedidos de gravação humana em JSON;
- `meta/` — sidecars de lineage.

## 6. Higgsfield

O projeto não depende de um MCP oficial funcional do Higgsfield no contexto documentado.

Instala:

`@higgsfield/cli`

Wrapper MCP:

`middleware/higgsfield_cli_mcp.py`

Capacidades citadas:

- geração de imagem;
- geração de vídeo;
- soul-id;
- upload;
- outras ferramentas expostas pelo wrapper.

Autenticação:

- OAuth via navegador;
- sem API key no fluxo documentado;
- login uma vez;
- token persistido em volume.

Login:

```bash
docker compose exec openclaw-vibestack higgsfield auth login
```

Status:

```bash
docker compose exec openclaw-vibestack higgsfield auth status
```

Token persiste em `HIGGSFIELD_DATA_DIR`, normalmente `/root/.higgsfield`.

Mídias geradas são documentadas em:

`/root/.openclaw/workspace/_shared/assets/`

Soul-ID pode manter pessoa/rosto consistente em criativos.

## 7. AtlasCloud

O projeto usa o MCP oficial da AtlasCloud.

Nome:

`atlascloud`

Executável:

`atlascloud-mcp`

É descrito como hub de 300+ modelos de:

- imagem;
- vídeo;
- LLM.

Autenticação:

```env
ATLASCLOUD_API_KEY=
```

Não usa login interativo e não depende de volume para autenticação.

Após mudar a key, o contexto recomenda:

```bash
docker compose up -d --force-recreate openclaw-vibestack
```

## 8. WhatsApp

Canal usa Evolution Go baseado em `whatsmeow`.

Não tratar Evolution Go como MCP. O MCP `whatsapp` é o middleware que conversa com Evolution Go.

Arquivos:

- `middleware/whatsapp_evolution_mcp.py`
- `middleware/whatsapp_bridge.py`

Fluxo inbound/outbound:

```text
WhatsApp
  ↓
Evolution Go
  ↓ webhook
WhatsApp bridge
  ↓
agente
  ↓
Evolution Go /send/text
  ↓
WhatsApp
```

Webhook interno:

`http://openclaw-vibestack:8765/webhook`

No fluxo interno documentado, não é necessária URL pública.

O bridge:

- ignora mensagens próprias;
- pode ignorar grupos;
- pode ignorar status;
- mantém sessão por contato;
- processa em background;
- responde HTTP rapidamente;
- pode processar imagem e áudio;
- pode repassar mídia ao agente quando o modelo suporta multimodalidade.

Agente de resposta:

```env
WA_BRIDGE_AGENT=hermes
```

ou OpenClaw.

Para OpenClaw pode existir:

`WA_BRIDGE_OPENCLAW_AGENT`

Porta bridge:

`8765`

Timeout:

```env
WA_BRIDGE_UPSTREAM_TIMEOUT=0
```

No contexto-base, `0` significa ilimitado.

Aviso de processamento:

```env
WA_BRIDGE_ACK_AFTER=20
```

Whitelist:

```env
WA_BRIDGE_ALLOWED_NUMBERS=
```

É recomendado limitar aos números autorizados quando apropriado.

## 9. Evolution Go

Variáveis principais documentadas:

```env
EVOLUTION_IMAGE=
EVOLUTION_PORT=
EVOLUTION_API_KEY=
EVOLUTION_INSTANCE_TOKEN=
EVOLUTION_INSTANCE=
EVOLUTION_DATA_DIR=
EVOLUTION_SAVE_MESSAGES=
EVOLUTION_IGNORE_GROUPS=
EVOLUTION_IGNORE_STATUS=
```

Evolution exige ativação de licença e conexão com internet no contexto descrito.

Após subir:

- acessar Manager;
- ativar licença;
- criar instância;
- obter QR;
- parear WhatsApp.

Tools citadas:

- `wa_create_instance`
- `wa_get_qr`
- `wa_instance_status`
- `wa_send_text`

Exemplo:

```text
wa_send_text(number="...", text="...")
```

## 10. Storage de mídia do Evolution

Pode usar S3/MinIO opcionalmente para mídia recebida.

Variáveis:

```env
EVOLUTION_MINIO_ENABLED=
EVOLUTION_MINIO_ENDPOINT=
EVOLUTION_MINIO_ACCESS_KEY=
EVOLUTION_MINIO_SECRET_KEY=
EVOLUTION_MINIO_BUCKET=
EVOLUTION_MINIO_USE_SSL=
EVOLUTION_MINIO_REGION=
```

Pode reutilizar B2 quando compatível com a configuração.

Quando habilitado, mídia recebida pode ser armazenada externamente e o webhook pode receber URL em vez de base64 inline.

## 11. Proxy do WhatsApp

Variáveis:

```env
EVOLUTION_PROXY_PROTOCOL=
EVOLUTION_PROXY_HOST=
EVOLUTION_PROXY_PORT=
EVOLUTION_PROXY_USERNAME=
EVOLUTION_PROXY_PASSWORD=
```

O contexto recomenda proxy residencial estático/IP fixo quando proxy for utilizado.

Evitar proxy rotativo para a sessão do WhatsApp Web.

## 12. Regras de verificação

Antes de usar qualquer tool ou variável listada neste arquivo em produção:

1. verificar se ela ainda existe no código atual do `vibestack-openclaw`;
2. verificar assinatura/parâmetros reais;
3. confirmar credenciais exigidas;
4. confirmar se a ação é read-only ou mutável;
5. respeitar aprovação humana e alçadas do projeto.
