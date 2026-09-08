# Provisionamento — Reserva Corporativa

Registro de capacidades institucionais evidenciadas para o produto.  
**Não** versionar senhas, `client_secret`, tokens ou dumps.

| Campo | Conteúdo |
| --- | --- |
| Atualizado | 2026-08-21 |
| Fontes | Confirmação humana (`client_id`, `RESREC`); padrões extraídos dos templates Smart em `-web` / `-api` |

## PROV-001 — Cliente SSO / Portal

### Valores do produto (humanos)

| Item | Valor | Status |
| --- | --- | --- |
| `client_id` (OIDC) | `m_GQOlhtD9BhZgo_sbERgfemr0Ua` | **Informado** (atualizado 2026-08-26) |
| Sigla do sistema no Portal (`idSistemaPortal`) | `RESREC` | **Informado** |

### Issuer / discovery (padrão Smart — evidência no template)

Montagem no Web (`oauth2.config.ts`): `issuer = sso.serverUrl + sso.issuer`.

| Ambiente | `serverUrl` (Web) | Discovery OIDC (Web) | `issuer-uri` Spring (API) | Evidência |
| --- | --- | --- | --- | --- |
| desenv | `https://ssodesenv.go.gov.br` | `https://ssodesenv.go.gov.br/oauth2/oidcdiscovery` | `https://ssodesenv.go.gov.br:443/oauth2/token` | `environment.desenv.ts`; `application-desenv.yaml` / `application-local.yaml` |
| homolog | `https://ssohomolog.go.gov.br` | `https://ssohomolog.go.gov.br/oauth2/oidcdiscovery` | `https://ssohomolog.go.gov.br:443/oauth2/token` | `environment.homolog.ts`; `application-homolog.yaml` |
| prod | `https://sso.go.gov.br` | `https://sso.go.gov.br/oauth2/oidcdiscovery` | `https://sso.go.gov.br:443/oauth2/token` | `environment.prod.ts`; `application-prod.yaml` |

Path relativo de discovery no Web: `/oauth2/oidcdiscovery` (constante do template).

**Nota:** o `issuer-uri` da API aponta para `/oauth2/token` (padrão Spring Security OAuth2 no template). O discovery OIDC do SPA usa `/oauth2/oidcdiscovery`. Ambos vêm do Smart; não inventados para RESREC.

### `redirect_uri` e silent refresh (padrão Smart)

Evidência: `environment*.ts` + `oauth2.config.ts` + `public/silent-refresh.html`.

| Item | Valor no template | Significado para RESREC |
| --- | --- | --- |
| `redirectUri` | `window.location.origin` | Origem da SPA em execução (ex.: `http://localhost:4200` em desenv local) |
| `silentRefreshRedirectUri` | `window.location.origin + '/silent-refresh.html'` | Mesma origem + `/silent-refresh.html` |
| `postLogoutRedirectUri` | igual a `redirectUri` | Mesma origem |

Não há URL fixa de redirect no template: depende de onde a aplicação estiver hospedada. No cadastro SSO do `client_id` informado, as redirect URIs oficiais do ambiente (desenv/homolog/prod) precisam estar alinhadas ao host real de RESREC (**PROV-004** / operação).

### URL do PDP (entitlement) — padrão Smart aplicado a RESREC

O path do PDP **não** inclui a sigla do sistema. A sigla `RESREC` entra como atributo de ambiente nas chamadas (Web: `environment.idSistemaPortal` em `authorization.service.ts`), não na URL.

| Ambiente | URL PDP (API / entitlement decision) | Evidência |
| --- | --- | --- |
| desenv | `https://ssodesenv.go.gov.br/api/identity/entitlement/decision` | `application-desenv.yaml` `xacml.pdp…service`; Web `authorizationUrl` em `environment.desenv.ts` |
| homolog | `https://ssohomolog.go.gov.br/api/identity/entitlement/decision` | `application-homolog.yaml`; `environment.homolog.ts` |
| prod | `https://sso.go.gov.br/api/identity/entitlement/decision` | `application-prod.yaml`; `environment.prod.ts` |

**Web (detalhe do template):** `AuthorizationService` faz `POST` em ``${authorizationUrl}/pdp`` — ou seja, concatena `/pdp` ao `authorizationUrl` que já termina em `/decision` (ex. desenv: `…/entitlement/decision/pdp`). A API usa diretamente `…/entitlement/decision`. Ao encaixar RESREC, manter o mesmo padrão do template Smart por ambiente; não inventar host novo.

### Status PROV-001

| Leitura | Conteúdo |
| --- | --- |
| Status | **Quase completo para desenv** (client + sigla + hosts SSO/PDP do padrão Smart) |
| Lacuna | Redirect URIs **oficiais** cadastradas no SSO para os hosts reais de RESREC (não só `window.location.origin` em local) |
| Ainda bloqueia 005/006 | **PROV-002** (mapeamento Usuário/Gestor → actionIds no PDP) |

## PROV-002 — Mapeamento Usuário / Gestor → ações

**Fonte:** padrão do template Smart + matriz do PRD. Detalhe: [CATALOGO_ACOES_USUARIO_GESTOR.md](CATALOGO_ACOES_USUARIO_GESTOR.md).

### O que o Smart traz (evidência)

| Item | Valor |
| --- | --- |
| Policy | `perfil_portal-policy` |
| Convenção | `Dominio.C` / `.I` / `.A` / `.E` |
| Exemplo no template | `Cliente.C`, `Cliente.I`, `Cliente.A`, `Cliente.E` |
| Perfis Usuário/Gestor no código Smart | **Não existem** (perfis vêm do Portal) |

### Pacotes RESREC (grupos Portal)

| Perfil Portal | actionIds |
| --- | --- |
| **Usuário** | `GrupoRecursoAdm`, `GrupoReservas` |
| **Gestor** | `GrupoCategorias`, `GrupoRecursoAdm`, `GrupoReservas` |

Escopo D09/D10 (próprias vs todas / já iniciada): **regra na API** após Permit — sem sufixo `.Admin`.

| Status PROV-002 | Conteúdo |
| --- | --- |
| Catálogo / mapeamento documental | **Definido** |
| Cadastro operacional no Portal/PDP | Pendente (necessário para Permit real em homologação) |

## PROV-003 / PROV-004

Sem alteração nesta atualização (Oracle institucional e URLs oficiais da app RESREC).

## Valores a usar no encaixe (resumo desenv)

| Chave | Valor |
| --- | --- |
| `clientId` | `m_GQOlhtD9BhZgo_sbERgfemr0Ua` |
| `idSistemaPortal` | `RESREC` |
| OIDC issuer (Web) | `https://ssodesenv.go.gov.br/oauth2/oidcdiscovery` |
| API `issuer-uri` | `https://ssodesenv.go.gov.br:443/oauth2/token` |
| PDP | `https://ssodesenv.go.gov.br/api/identity/entitlement/decision` |
| Policy | `perfil_portal-policy` |
| redirect / silent | origem da SPA + `/silent-refresh.html` |
| ActionIds | ver pacotes Usuário / Gestor acima |

## Uso nas tasks

| Task | Condição |
| --- | --- |
| `TASK-INC0-005` / `006` | **Desbloqueadas para encaixe** com catálogo PROV-002; Permit real em runtime espera cadastro Portal |
| FEAT-001+ | Pode avançar; homologação autenticada espera cadastro Portal dos actionIds |

## Histórico

| Data | Evento |
| --- | --- |
| 2026-08-21 | ADR-004 aceita; PROV-001 parcial (`client_id` + `RESREC`) |
| 2026-08-21 | Complemento: issuer/discovery, redirect/silent e PDP URL a partir dos templates Smart |
| 2026-08-21 | PROV-002: catálogo Usuário/Gestor → actionIds a partir do padrão Smart + PRD |
| 2026-08-24 | PROV-003: schema V001 versionado; apply Oracle institucional **pendente** |
| 2026-08-24 | Pendências registradas: redirect oficial RESREC (PROV-001), cadastro Portal (PROV-002), TP-RVW-004 fuso, PRD-RVW-003/007 |

## PROV-003 — Oracle institucional

| Item | Status |
| --- | --- |
| Schema SQL V001 (`RC_CATEGORIA`, `RC_RECURSO`, `RC_RESERVA`) | **Artefato pronto** em `recursos-corporativos-tati-api/db/schema/` |
| URL / credencial / schema Oracle corporativo | **Não evidenciado** — apply manual bloqueado |
| Ambiente local (H2/profile template) | Viável para dev/testes unitários |

## Decisões de produto pendentes (não bloqueiam código local)

| ID | Tema | Status | Nota |
| --- | --- | --- | --- |
| PRD-RVW-003 | Exibir/ocultar recursos inativos na consulta do usuário | **Não decidido** | Web não força default |
| PRD-RVW-007 | Regra categoria inativa | **Não decidido** | Isolado |
| TP-RVW-004 | Fuso horário institucional para reservas | **Não decidido** | API usa `RelogioReservaProvider` JVM |
