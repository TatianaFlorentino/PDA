# Changelog — ambiente local RESREC (PDA2)

Registro do que foi feito para a aplicação funcionar em desenvolvimento local.  
Sem senhas, tokens ou `client_secret`.

## 2026-08-21 — Fundações (FEAT-000)

- ADRs 002–004 no PDA; ADR-004 **Aceita** (auth blueprint corporativo).
- OpenAPI mínimo + schema Oracle V001 versionado.
- Catálogo documental Usuário×Gestor (PROV-002).
- Identidade: sigla Portal **`RESREC`**, client OIDC informado; encaixe API/Web.
- Smokes `mvn test` / build Web.

## 2026-08-24 — Catálogo e reservas (FEAT-001 / FEAT-002)

- FEAT-001: módulos categoria/recurso API + UI gestor + consulta recursos.
- FEAT-002: reserva usuário (conflito ADR-003, D10, UI nova/minhas/alterar).
- OpenAPI evoluiu (catálogo → reserva usuário).

## 2026-08-25..26 — Admin reservas (FEAT-003) + Postgres local

- FEAT-003: `/reservas/administracao*`, service gestor D09, UI admin-reservas.
- Profile API **`local`**: PostgreSQL (zonky embutido ou Docker), sem Oracle no dia a dia.
- Porta API ajustada para conviver com o front (evoluiu para **8080**).

## 2026-08-26..27 — SSO, portas, actionIds de grupo

- Redirects SSO alinhados a **`http://localhost:8088`** (+ silent-refresh).
- Web em **8088**; proxy `/smart-api` → API **8080**.
- ClientId desenv: `m_GQOlhtD9BhZgo_sbERgfemr0Ua` (client antigo rejeitado pelo SSO).
- ActionIds de produto passaram a grupos Portal:
  - `GrupoCategorias`, `GrupoRecursoAdm`, `GrupoReservas`
  - Controllers `@PreAuthorize` + `ngxPermissionsOnly` + seed local.
- Bug: replace case-insensitive em HTML corrompeu `recurso.ativo` etc. — corrigido.

## 2026-08-27 — Config Angular `local` e fallback Portal

- Causa raiz do 403/`apidesenv.../actions`: `ng serve` default **`dev`** trocava `environment.ts` por `environment.desenv.ts`.
- Criada configuration **`local`** (sem fileReplacements); `npm start` usa `--configuration local`.
- `portalFallbackLocal: true`: após SSO válido, não depende de `/actions` (403 API Manager); roles locais dos 3 grupos.
- Menu estático em modo fallback (evita 403 em menus/unidades).
- Documentado em `docs/implementation/ROTEIRO_LOCAL_CASA.md`.

## 2026-09-01 — Ambiente levantado novamente

- API health UP em `:8080`; Web `:8088` 200.
- Lembrete: raiz `:8080/` = 404 Tomcat esperado; app só em `:8088`.

## 2026-09-04 — Backup documental neste GitHub

- README agregador + este changelog + cópia dos docs PDA (roteiro, PROJECT_STATE, PROVISIONING, catálogo, ADR-003/004, specs FEAT-001..003).
- Escopo: **só documentação** (código API/Web permanece no GitLab GOV).

---

## Pendências conhecidas (institucionais)

| ID | Situação |
| --- | --- |
| PROV-001 | Redirects oficiais host RESREC além de localhost |
| PROV-002 | Permit real; `/actions` 200 com token do client no API Manager |
| PROV-003 | Apply Oracle institucional |
| PRD-RVW-003 / 007, TP-RVW-004, PRD 19.2 | Decisões isoladas / não inventadas |
