# Roteiro local — Reserva Corporativa (RESREC)

Guia para **replicar em casa** o que foi feito para a aplicação funcionar no PDA2.  
Atualizado: 2026-08-27.

| Repo | Papel |
| --- | --- |
| `recursos-corporativos-tati-pda` | Produto, ADRs, PROV, planos |
| `recursos-corporativos-tati-api` | Backend Spring Boot |
| `recursos-corporativos-tati-web` | Frontend Angular |

**Produto:** Reserva Corporativa · **Portal:** `RESREC` · **SSO desenv:** `ssodesenv.go.gov.br`

---

## 1. O que foi construído (visão geral)

### PDA / produto
- Specs e planos FEAT-000…003 (categorias, recursos, reservas, admin D09).
- ADRs (intervalo de reserva, auth blueprint).
- PROV-001 (SSO/client/RESREC) e PROV-002 (actionIds → grupos Portal).
- Catálogo: `docs/architecture/CATALOGO_ACOES_USUARIO_GESTOR.md`.

### API (domínio)
- CRUD **Categoria**, **Recurso**, **Reserva** (+ administração de reservas).
- OpenAPI canônico em `openapi/openapi.yaml`.
- Segurança: JWT + `@PreAuthorize` com actionIds de **grupo**:
  - `GrupoCategorias`
  - `GrupoRecursoAdm`
  - `GrupoReservas`
- Profile **`local`**: PostgreSQL (embutido zonky **ou** Docker), **sem Oracle** no dia a dia.

### Web
- Telas de catálogo, consulta, nova reserva, minhas reservas, admin-reservas.
- OIDC Authorization Code + PKCE.
- Config **`local`** do Angular (importante — ver §5).
- Proxy `/smart-api` → API local; Portal via proxy (quando não há fallback).
- `portalFallbackLocal`: se Portal/API Manager der 403, entra com roles locais após SSO.

---

## 2. Pré-requisitos na máquina de casa

| Ferramenta | Observação |
| --- | --- |
| **JDK 21** | Ex.: Microsoft OpenJDK 21 |
| **Maven 3.9+** | Ou usar `.tools/apache-maven-3.9.9` se copiar a pasta do PDA2 |
| **Node.js 20+** + npm | Para o Angular |
| **Git** | Clonar/atualizar os 3 repos |
| **VPN / rede GOV** | SSO desenv e `apidesenv` **só funcionam na rede institucional**. Em casa sem VPN: use `skipAuth: true` (só UI) ou só API + UI sem login real |
| Rede livre nas portas | **8080** (API), **8088** (Web), **55432** (Postgres embutido, se zonky) |

**Não versionar:** senhas, `client_secret`, tokens.

---

## 3. Estrutura de pastas sugerida

```text
PDA2/
  recursos-corporativos-tati-pda/
  recursos-corporativos-tati-api/
  recursos-corporativos-tati-web/
  .tools/apache-maven-3.9.9/   (opcional)
```

Branches típicas de trabalho local (podem variar):
- API: `feature/local-postgres` (ou a que tiver o profile local + Postgres)
- Web: branch com FEAT + config `local` / `portalFallbackLocal`

---

## 4. API — subir backend

### 4.1 Config local (já no repo)

- `application.yaml` → `server.port: **8080**`, context-path `/smart/clientes/v1.0`
- `application-local.yaml` → PostgreSQL + `ddl-auto: update` + JWT issuer desenv
- `EmbeddedPostgresLocalConfig` → Postgres embutido (zonky), porta **55432**, ligado por padrão (`app.local.embedded-postgres=true`)
- Opcional: `docker-compose.local.yml` + SQL em `db/schema/` se preferir Postgres externo (`-Dapp.local.embedded-postgres=false`)

### 4.2 Comando (PowerShell)

```powershell
cd C:\CAMINHO\PDA2\recursos-corporativos-tati-api
$env:JAVA_HOME = "C:\Program Files\Microsoft\jdk-21.0.12.8-hotspot"  # ajuste
# Maven do PATH ou:
C:\CAMINHO\PDA2\.tools\apache-maven-3.9.9\bin\mvn.cmd spring-boot:run -DskipTests
```

Garantir profile **`local`** ativo (no `pom` / `spring.profiles.active` do projeto, ou):

```powershell
$env:SPRING_PROFILES_ACTIVE = "local"
```

### 4.3 Checagens

| URL | Esperado |
| --- | --- |
| `http://localhost:8080/smart/clientes/v1.0/monitor/health` | `UP` |
| `http://localhost:8080/smart/clientes/v1.0/swagger-ui/index.html` | Swagger |
| `http://localhost:8080/` (raiz) | **404** (normal — use o context-path) |

### 4.4 ActionIds nos Controllers

| Controller | actionId |
| --- | --- |
| `CategoriaController` | `GrupoCategorias` |
| `RecursoController` | `GrupoRecursoAdm` |
| `ReservaController` | `GrupoReservas` |

Policy: `perfil_portal-policy`.

---

## 5. Web — subir frontend (ponto crítico)

### 5.1 Por que a config `local` existe

O `ng serve` **antigamente** usava `defaultConfiguration: "dev"`, que **substitui** `environment.ts` por `environment.desenv.ts` (URLs absolutas em `apidesenv` + `portalFallbackLocal: false`).

Isso causava:
1. Chamada direta a `https://apidesenv.../actions` → **403**
2. Toast “Erro interno ao buscar dados da aplicação”
3. ActionIds do Portal não carregavam

**Correção aplicada:**
- Build/serve configuration **`local`** (sem `fileReplacements`)
- `npm start` → `--configuration local --port 8088 --proxy-config src/proxy.conf.js`
- `angular.json` → `serve.defaultConfiguration: "local"`

### 5.2 `environment.ts` local (essencial)

```ts
apiUrl: '/smart-api',                    // proxy → :8080
apiPortalAcessos: '/portal-acessos',
idSistemaPortal: 'RESREC',
skipAuth: false,                         // true = UI sem SSO
portalFallbackLocal: true,               // true = não depende do Portal/403
sso: {
  clientId: 'm_GQOlhtD9BhZgo_sbERgfemr0Ua',
  serverUrl: 'https://ssodesenv.go.gov.br',
  redirectUri: 'http://localhost:8088',
  silentRefreshRedirectUri: 'http://localhost:8088/silent-refresh.html',
  // ...
}
```

### 5.3 Proxy (`src/proxy.conf.js`)

| Path local | Destino |
| --- | --- |
| `/smart-api` | `http://localhost:8080/smart/clientes/v1.0` |
| `/portal-acessos` | `https://apidesenv.go.gov.br/portal/acessos-usuarios/v1.0` |
| `/portal-menus` | menus-sistemas desenv |
| `/portal-unidades` | unidades-acessos desenv |

### 5.4 Comando

```powershell
cd C:\CAMINHO\PDA2\recursos-corporativos-tati-web
npm install
npm start
```

Abrir: **http://localhost:8088** (não 4200).

### 5.5 SSO — redirects no client OIDC

Cadastrar no client `m_GQOlhtD9BhZgo_sbERgfemr0Ua` (desenv):

- `http://localhost:8088`
- `http://localhost:8088/silent-refresh.html`

Sem isso: erro de “URL de callback não corresponde…”.

### 5.6 Portal — itens (PROV-002)

No Portal (Manutenção de Itens), sistema **RESREC**, **Nome** = actionId:

| Nome (actionId) | Exemplo URL item |
| --- | --- |
| `GrupoCategorias` | (categorias) |
| `GrupoRecursoAdm` | (recursos) |
| `GrupoReservas` | `/reservas` |

Ainda assim, em local o endpoint `/actions` pode retornar **403** (token × API Manager). Com `portalFallbackLocal: true` a app **não chama** o Portal e injeta os 3 grupos localmente após SSO válido.

---

## 6. Ordem de subida (checklist casa)

```text
1. JDK 21 + Node + (Maven)
2. Subir API (profile local) → health UP em :8080
3. npm start no Web → :8088 configuration local
4. Abrir http://localhost:8088 (janela anônima se testar SSO)
5. Login SSO desenv (se rede/VPN) OU skipAuth: true
6. Se aviso amarelo "modo local" → esperado com portalFallbackLocal
7. Exercitar Categorias / Recursos / Reservas
```

### API sem Maven na PATH

```powershell
$env:JAVA_HOME = "C:\Program Files\Microsoft\jdk-21.0.12.8-hotspot"
C:\CAMINHO\PDA2\.tools\apache-maven-3.9.9\bin\mvn.cmd -f ...\recursos-corporativos-tati-api\pom.xml spring-boot:run -DskipTests
```

---

## 7. Problemas que já apareceram e a solução

| Sintoma | Causa | Solução |
| --- | --- | --- |
| Tomcat 404 em `:8088` | API na porta do Web | Web **8088**, API **8080** |
| Callback SSO inválido | Redirect não cadastrado / porta errada | Redirects em `localhost:8088` |
| “Não foi possível encontrar aplicativo… chave consumidor” | `clientId` errado | Usar `m_GQOlhtD9BhZgo_sbERgfemr0Ua` |
| Erro ao buscar dados + 403 `/actions` | `environment.desenv` + Portal 403 | Config **`local`** + `portalFallbackLocal` |
| Templates quebrados (`GrupoRecursoAdmtivo`) | Replace case-insensitive em HTML | Restaurar `recurso.ativo`, etc. |
| Network mostra `apidesenv.../actions` | Serve em config `dev` | `npm start` com `--configuration local` |
| API assinada no Portal mas 403 no browser | Assinatura ≠ token do SPA liberado no gateway / CORS | Fallback local; validar com identidade |

---

## 8. Modos de autenticação local

| Objetivo | `skipAuth` | `portalFallbackLocal` | Rede |
| --- | --- | --- | --- |
| Só ver UI sem SSO | `true` | (irrelevante) | Qualquer |
| SSO + UI sem depender do Portal | `false` | `true` | VPN/SSO desenv |
| SSO + Portal real (Permit) | `false` | `false` | VPN + `/actions` 200 |

Nunca deixar `skipAuth` / `portalFallbackLocal` em **true** em desenv/homolog/prod deployados.

---

## 9. O que ainda é institucional (não resolve só no código)

- **PROV-001:** redirects oficiais do host RESREC (além de localhost).
- **PROV-002:** cadastro operacional + vínculo perfil↔usuário; `/actions` 200 com o token do client.
- **PROV-003:** Oracle institucional (local usa Postgres).
- Permit real no PDP (`Grupo*`) em runtime autenticado completo.

---

## 10. Arquivos-chave para copiar/conferir em casa

```text
API
  src/main/resources/application.yaml          → port 8080
  src/main/resources/application-local.yaml    → Postgres local
  .../config/EmbeddedPostgresLocalConfig.java
  .../categoria|recurso|reserva/*Controller.java  → Grupo*

Web
  angular.json                                 → serve defaultConfiguration: local
  package.json                                 → start --configuration local --port 8088
  src/environments/environment.ts              → proxy + fallback + clientId + redirects 8088
  src/proxy.conf.js
  src/app/services/app.service.ts              → skip Portal se portalFallbackLocal
  src/app/modules/main/components/menu/app.menu.ts
  src/app/services/acessos-usuario.service.ts  → seed Grupo*

PDA
  docs/architecture/PROVISIONING.md
  docs/architecture/CATALOGO_ACOES_USUARIO_GESTOR.md
  docs/implementation/PROJECT_STATE.md
  este arquivo (ROTEIRO_LOCAL_CASA.md)
```

---

## 11. Comandos rápidos (copiar/colar)

```powershell
# Terminal 1 — API
cd C:\CAMINHO\PDA2\recursos-corporativos-tati-api
$env:JAVA_HOME = "C:\Program Files\Microsoft\jdk-21.0.12.8-hotspot"
$env:SPRING_PROFILES_ACTIVE = "local"
C:\CAMINHO\PDA2\.tools\apache-maven-3.9.9\bin\mvn.cmd spring-boot:run -DskipTests

# Terminal 2 — Web
cd C:\CAMINHO\PDA2\recursos-corporativos-tati-web
npm start

# Browser
# http://localhost:8088
```

Boa sorte em casa — ao voltar, comece pelo §6 e pelo §7 se algo falhar.
