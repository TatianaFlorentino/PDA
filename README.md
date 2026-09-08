# Reserva Corporativa (RESREC) — backup documental PDA

Snapshot documental do trabalho no workspace **PDA2** (produto Reserva Corporativa / Portal `RESREC`).

> Este repositório guarda **documentação e roteiros**, não o código-fonte completo da API/Web Smart (origem institucional no GitLab GOV).

**Clone local neste workspace:** `C:\GEREDETIC\PDA2\PDA` → remote [github.com/TatianaFlorentino/PDA](https://github.com/TatianaFlorentino/PDA).

**Produto:** aplicação web interna para cadastro de recursos compartilhados e reservas sem conflito (perfis Usuário e Gestor).  
**SSO desenv:** `ssodesenv.go.gov.br` · **Sigla Portal:** `RESREC`

### Framework PDA (como funciona)

Leia primeiro: **[docs/process/COMO_FUNCIONA_O_PDA.md](docs/process/COMO_FUNCIONA_O_PDA.md)** — processo PDA-Squad, multi-repo, artefatos, ciclos de prompts/skills, gates humanos e o exemplo RESREC.

No workspace PDA2, o kit completo do processo fica em `pda-squad/` (CLI + skills + proposta).

---

## Repositórios de implementação (origem)

| Papel | Pasta / repo | Conteúdo |
| --- | --- | --- |
| PDA | `recursos-corporativos-tati-pda` | PRD, ADRs, PROV, planos, estado |
| API | `recursos-corporativos-tati-api` | Spring Boot 3.3 / Java 21 |
| Web | `recursos-corporativos-tati-web` | Angular 19 / PrimeNG (Smart Sakai) |

Remotes de trabalho: GitLab `codigo.dev.tools.go.gov.br` (não alterar). Este GitHub é **backup pessoal documental**.

---

## O que foi entregue

| Feature | Escopo | Status |
| --- | --- | --- |
| **FEAT-000** | Fundações INC-000 (ADRs, OpenAPI, schema, identidade RESREC, smokes) | concluída |
| **FEAT-001** | Catálogo: categorias e recursos | concluída |
| **FEAT-002** | Reserva pelo usuário (conflito ADR-003, D10) | concluída |
| **FEAT-003** | Administração de reservas (D09 gestor) | concluída |

### Domínio (API)

- CRUD **Categoria**, **Recurso**, **Reserva** (+ `/reservas/administracao*`)
- OpenAPI canônico (v0.4.0 na trilha admin)
- Policy: `perfil_portal-policy`
- ActionIds (grupos Portal):

| Controller | actionId |
| --- | --- |
| `CategoriaController` | `GrupoCategorias` |
| `RecursoController` | `GrupoRecursoAdm` |
| `ReservaController` | `GrupoReservas` |

### Runtime local (ponto crítico)

| Serviço | Porta | Notas |
| --- | --- | --- |
| **Web (Angular)** | **8088** | `--configuration local` (não usar `dev`/`desenv` por padrão) |
| **API** | **8080** | context-path `/smart/clientes/v1.0` |
| **Postgres embutido** | 55432 | profile `local` (zonky); Oracle só institucional |

- Proxy Web: `/smart-api` → API local; Portal via `/portal-acessos` etc.
- Auth local: SSO **ou** `skipAuth: true` / `portalFallbackLocal: true` (ver roteiro)
- Client OIDC desenv (SPA): `m_GQOlhtD9BhZgo_sbERgfemr0Ua`
- Redirects SSO locais: `http://localhost:8088` e `.../silent-refresh.html`

**Não abra** `http://localhost:8080/` no browser (404 Tomcat esperado). Use a 8088.

---

## Como subir (resumo)

Detalhe completo: [docs/implementation/ROTEIRO_LOCAL_CASA.md](docs/implementation/ROTEIRO_LOCAL_CASA.md)

```powershell
# Terminal 1 — API
cd ...\recursos-corporativos-tati-api
$env:JAVA_HOME = "...\jdk-21..."
$env:SPRING_PROFILES_ACTIVE = "local"   # se necessário (Maven profile local já é default)
mvn spring-boot:run -DskipTests
# Health: http://localhost:8080/smart/clientes/v1.0/monitor/health

# Terminal 2 — Web
cd ...\recursos-corporativos-tati-web
npm install
npm start   # --configuration local --port 8088
# App: http://localhost:8088
```

Sem VPN/rede GOV: `skipAuth: true` em `environment.ts` (só UI).  
Com SSO e Portal em 403: manter `portalFallbackLocal: true`.

---

## Documentação neste repo

| Documento | Caminho |
| --- | --- |
| **Como funciona o framework PDA** | [docs/process/COMO_FUNCIONA_O_PDA.md](docs/process/COMO_FUNCIONA_O_PDA.md) |
| Roteiro local (casa/trabalho) | [docs/implementation/ROTEIRO_LOCAL_CASA.md](docs/implementation/ROTEIRO_LOCAL_CASA.md) |
| Estado / rastreabilidade | [docs/implementation/PROJECT_STATE.md](docs/implementation/PROJECT_STATE.md) |
| Changelog do ambiente local | [CHANGELOG_LOCAL.md](CHANGELOG_LOCAL.md) |
| Provisionamento SSO/Portal | [docs/architecture/PROVISIONING.md](docs/architecture/PROVISIONING.md) |
| Catálogo actionIds | [docs/architecture/CATALOGO_ACOES_USUARIO_GESTOR.md](docs/architecture/CATALOGO_ACOES_USUARIO_GESTOR.md) |
| ADR-003 intervalo reserva | [docs/architecture/adr/ADR-003-semantica-intervalo-reserva.md](docs/architecture/adr/ADR-003-semantica-intervalo-reserva.md) |
| ADR-004 auth blueprint | [docs/architecture/adr/ADR-004-adocao-auth-blueprint.md](docs/architecture/adr/ADR-004-adocao-auth-blueprint.md) |
| Spec FEAT-001 | [docs/features/FEAT-001/FEATURE_SPEC.md](docs/features/FEAT-001/FEATURE_SPEC.md) |
| Spec FEAT-002 | [docs/features/FEAT-002/FEATURE_SPEC.md](docs/features/FEAT-002/FEATURE_SPEC.md) |
| Spec FEAT-003 | [docs/features/FEAT-003/FEATURE_SPEC.md](docs/features/FEAT-003/FEATURE_SPEC.md) |

---

## Armadilhas já resolvidas

| Sintoma | Causa | Solução |
| --- | --- | --- |
| Tomcat 404 na 8088 | API na porta do Web | Web **8088**, API **8080** |
| Callback SSO inválido | Redirect não cadastrado | Redirects em `localhost:8088` |
| “chave de consumidor” | `clientId` errado | Usar `m_GQOlhtD9BhZgo_sbERgfemr0Ua` |
| Erro ao buscar dados + 403 `/actions` | Serve em config `dev` (environment.desenv) | `npm start` → **`--configuration local`** |
| Network mostra `apidesenv.../actions` | Mesmo problema | Config **local** + fallback |
| Templates quebrados (`GrupoRecursoAdmtivo`) | Replace case-insensitive em HTML | Restaurar `recurso.ativo`, etc. |

---

## O que NÃO versionar

- Senhas, usuários de teste com credencial, tokens, `client_secret`
- `.env` com segredos
- Dumps de banco

---

## Provisionamento ainda institucional

- **PROV-001:** redirects oficiais do host RESREC (além de localhost)
- **PROV-002:** Permit real / vínculo perfil↔usuário; `/actions` 200 com token do client
- **PROV-003:** Oracle institucional (local usa Postgres)

---

## Licença / uso

Material de estudo e backup pessoal do trabalho documental no PDA. Código de produção permanece nos repositórios institucionais.
