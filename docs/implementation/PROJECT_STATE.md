# Estado do Projeto

Este documento consolida a rastreabilidade de features, tarefas e revisões do projeto.
Preserve os registros anteriores e acrescente eventos ao histórico incremental.

## Rastreabilidade unificada

| Tipo | ID | Feature ID | Descrição | Repositório-alvo | Status | Dependências | Evidência/status da revisão | Próxima ação |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| FEATURE | FEAT-000 | FEAT-000 | Incremento 0 — fundações (INC-000) | pda / web / api (por task) | concluida | — | P-16: 001–008 aprovadas; INC-000 completo | — |
| TASK | TASK-INC0-001 | FEAT-000 | ADRs 002–004 no PDA | pda | concluida | — | P-16 `aprovar`; ADR-004 **Aceita** (humano 2026-08-21) | — |
| TASK | TASK-INC0-002 | FEAT-000 | OpenAPI mínimo versionado | api | concluida | TASK-INC0-001 | P-16 `aprovar`; `0def909` | — |
| TASK | TASK-INC0-003 | FEAT-000 | Schema Oracle inicial | api | concluida | TASK-INC0-001 | P-16 `aprovar`; `286cfe3` | Aplicar SQL com Oracle/PROV-003 |
| TASK | TASK-INC0-004 | FEAT-000 | Catálogo documental Usuário×Gestor | pda | concluida | TASK-INC0-001 | P-16 `aprovar`; `68eaa09` | — |
| TASK | TASK-INC0-005 | FEAT-000 | Encaixe identidade API | api | concluida | 001, 004, PROV-001/002 | P-16 `aprovar`; API `5e95e11` | — |
| TASK | TASK-INC0-006 | FEAT-000 | Encaixe identidade Web | web | concluida | 001, 004, PROV-001/002 | P-16 `aprovar`; Web `28de614` | — |
| TASK | TASK-INC0-007 | FEAT-000 | Smoke `mvn test` API | api | concluida | — | P-16 `aprovar`; API `2c99e49`; `mvn test` 13/13 | — |
| TASK | TASK-INC0-008 | FEAT-000 | Smoke build+test Web | web | concluida | — | P-16 `aprovar`; Web `b40abd5`; build+test headless 3/3 | — |
| FEATURE | FEAT-001 | FEAT-001 | Categorias e recursos (INC-001 / Catálogo) | pda / web / api (por task) | concluida | FEAT-000; Spec+Impl Plan | P-16: 001–009 `aprovar`; 010 `aprovar_com_ressalvas`; 011 `aprovar` (humano 2026-08-24) | — |
| FEATURE | FEAT-002 | FEAT-002 | Reserva pelo usuário (INC-002 / Reserva) | pda / web / api (por task) | concluida | FEAT-001; Spec+Impl Plan+Breakdown | P-16: 001–010 implementadas; 011 `aprovar_com_ressalvas` (PROV-002); 012 documental | — |
| TASK | TASK-FEAT-002-001 | FEAT-002 | OpenAPI reserva usuário | api | concluida | FEAT-001 | API OpenAPI v0.3.0 em develop | — |
| TASK | TASK-FEAT-002-002 | FEAT-002 | Domínio reserva + conflito ADR-003 | api | concluida | TASK-FEAT-002-001 | API `b88a604`; ConflitoReservaService ADR-003 | — |
| TASK | TASK-FEAT-002-003 | FEAT-002 | ReservaService RN/D10/R-RACE | api | concluida | TASK-FEAT-002-002 | API `b88a604`; pessimistic lock + D10 | — |
| TASK | TASK-FEAT-002-004 | FEAT-002 | ReservaController HTTP | api | concluida | TASK-FEAT-002-003, 001 | API `b88a604`; paths `/reservas*` | — |
| TASK | TASK-FEAT-002-005 | FEAT-002 | Testes domínio/API reserva | api | concluida | 002, 003, 004 | API `b88a604`; `mvn test` 32/32 | — |
| TASK | TASK-FEAT-002-006 | FEAT-002 | Serviços HTTP + rotas/menu reserva | web | concluida | TASK-FEAT-002-001 | Web `9d08e7b`; reserva.service + rotas | — |
| TASK | TASK-FEAT-002-007 | FEAT-002 | UI nova reserva (9.1/9.2) | web | concluida | TASK-FEAT-002-006 | Web `9d08e7b`; nova-reserva + 409 | — |
| TASK | TASK-FEAT-002-008 | FEAT-002 | UI minhas reservas + cancelar (9.3) | web | concluida | TASK-FEAT-002-006 | Web `9d08e7b`; minhas-reservas + confirm | — |
| TASK | TASK-FEAT-002-009 | FEAT-002 | UI alterar reserva (aceite 10) | web | concluida | TASK-FEAT-002-008 | Web `9d08e7b`; dialog alterar | — |
| TASK | TASK-FEAT-002-010 | FEAT-002 | Build + testes Web reserva | web | concluida | 007, 008, 009 | Web `9d08e7b`; build + `ng test` 19 SUCCESS | — |
| TASK | TASK-FEAT-002-011 | FEAT-002 | Validação integrada reserva | pda | concluida | 005, 010; PROV p/ auth | Sem E2E SSO (PROV-002); API+Web testes verdes | Cadastro Portal |
| TASK | TASK-FEAT-002-012 | FEAT-002 | Fechamento documental FEAT-002 | pda | concluida | TASK-FEAT-002-011 | PROJECT_STATE 2026-08-24 | — |
| TASK | TASK-FEAT-001-001 | FEAT-001 | OpenAPI catálogo | api | concluida | INC-000 | P-16 `aprovar` (reconfirmado); API `e6720c0`; OpenAPI v0.2.0 | — |
| TASK | TASK-FEAT-001-002 | FEAT-001 | Módulo categoria API (RN-04/05) | api | concluida | TASK-FEAT-001-001 | P-16 `aprovar`; API `8aeedb4`; PreAuthorize Categoria.*; testes RN-04/05 | — |
| TASK | TASK-FEAT-001-003 | FEAT-001 | Módulo recurso API (RN-03/06/07) | api | concluida | 001, 002 | P-16 `aprovar`; API `41a08bb`; RN-03/06/07 | — |
| TASK | TASK-FEAT-001-004 | FEAT-001 | Testes domínio catálogo API | api | concluida | 002, 003 | P-16 `aprovar`; API `6a7d8fc`; `mvn test` exit 0; RN-02≠permitAll | — |
| TASK | TASK-FEAT-001-005 | FEAT-001 | Serviços HTTP + rotas/menu | web | concluida | 001 (recomendado após 003) | P-16 `aprovar`; Web `af2934d`; Categoria.*/Recurso.* | — |
| TASK | TASK-FEAT-001-006 | FEAT-001 | UI gestor categorias | web | concluida | 005 | P-16 `aprovar`; Web `bce7795` | — |
| TASK | TASK-FEAT-001-007 | FEAT-001 | UI gestor recursos | web | concluida | 005, 003 | P-16 `aprovar`; Web `304d3cd` | — |
| TASK | TASK-FEAT-001-008 | FEAT-001 | UI consulta recursos (usuário) | web | concluida | 005, 003 | P-16 `aprovar`; Web `b4ccd04`; RVW-003 não fechado | — |
| TASK | TASK-FEAT-001-009 | FEAT-001 | Build + testes Web catálogo | web | concluida | 006, 007, 008 | P-16 `aprovar`; `ng test` 17 SUCCESS; build ok | — |
| TASK | TASK-FEAT-001-010 | FEAT-001 | Validação integrada catálogo | pda | concluida | 004, 009; PROV-002 p/ aceite auth | P-16 `aprovar_com_ressalvas`; humano pediu fechar; sem E2E SSO (PROV-002); evidência API+Web testes verdes | Cadastro Portal |
| TASK | TASK-FEAT-001-011 | FEAT-001 | Fechamento documental FEAT-001 | pda | concluida | 010 | P-16 `aprovar`; PROJECT_STATE consolidado 2026-08-24 | `$pda-prepare FEAT-002` |
| PROV | PROV-001 | FEAT-000 | Cliente SSO/Portal | institucional | quase_completo | — | client+RESREC + hosts Smart (issuer/PDP/redirect pattern) em PROVISIONING.md | Redirect oficial RESREC no SSO |
| PROV | PROV-002 | FEAT-000 | Usuário/Gestor → actionIds | institucional | catalogo_definido | — | Padrão Smart + pacotes RESREC em CATALOGO / PROVISIONING | Cadastro operacional no Portal/PDP (bloqueia aceite autenticado) |
| PROV | PROV-003 | FEAT-000 | Oracle institucional | institucional | pendente | — | Schema V001 em `db/schema/`; apply manual | URL/credencial Oracle institucional |
| DEC | PRD-RVW-003 | FEAT-001 | Inativos na consulta usuário | produto | nao_decidido | — | Isolado em FEAT-001-008 | Decisão humana |
| DEC | PRD-RVW-007 | FEAT-001 | Categoria inativa | produto | nao_decidido | — | Isolado | Decisão humana |
| DEC | TP-RVW-004 | FEAT-002 | Fuso institucional reserva | produto | nao_decidido | — | RelogioReservaProvider JVM; testes documentam hipótese | Decisão humana |
| FEATURE | FEAT-003 | FEAT-003 | Admin reservas gestor (D09) | pda / web / api (por task) | concluida | FEAT-002; Spec+Impl Plan+Breakdown | 11/11 tasks; aceite 13 domínio; PROV-002 E2E pendente; PRD 19.2 isolado | MR feature/FEAT-003 → develop |
| TASK | TASK-FEAT-003-001 | FEAT-003 | OpenAPI reserva admin | api | concluida | FEAT-002 | OpenAPI v0.4.0 `/reservas/administracao*` | — |
| TASK | TASK-FEAT-003-002 | FEAT-003 | Service/Repository gestor D09 | api | concluida | TASK-FEAT-003-001 | listarTodas/alterarComoGestor/cancelarComoGestor | — |
| TASK | TASK-FEAT-003-003 | FEAT-003 | ReservaController admin HTTP | api | concluida | TASK-FEAT-003-002, 001 | paths `/reservas/administracao*` | — |
| TASK | TASK-FEAT-003-004 | FEAT-003 | Testes domínio/API admin | api | concluida | TASK-FEAT-003-003 | ReservaControllerTest; mvn test 43/43 | — |
| TASK | TASK-FEAT-003-005 | FEAT-003 | Serviços HTTP + rotas/menu admin | web | concluida | TASK-FEAT-003-001 | reserva.service admin + rota/menu | — |
| TASK | TASK-FEAT-003-006 | FEAT-003 | UI listagem admin + filtros | web | concluida | TASK-FEAT-003-005 | admin-reservas listagem + responsável | — |
| TASK | TASK-FEAT-003-007 | FEAT-003 | UI alterar reserva admin (D09) | web | concluida | TASK-FEAT-003-006 | dialog alterar gestor (inclusive iniciada) | — |
| TASK | TASK-FEAT-003-008 | FEAT-003 | UI cancelar reserva admin (D09) | web | concluida | TASK-FEAT-003-006 | confirm cancel gestor | — |
| TASK | TASK-FEAT-003-009 | FEAT-003 | Build + testes Web admin | web | concluida | 007, 008 | build ok; `ng test` 21 SUCCESS | — |
| TASK | TASK-FEAT-003-010 | FEAT-003 | Validação integrada admin | pda | concluida | 004, 009; PROV p/ auth | Aceite 13 checklist domínio; sem E2E SSO (PROV-002) | Cadastro Portal |
| TASK | TASK-FEAT-003-011 | FEAT-003 | Fechamento documental FEAT-003 | pda | concluida | TASK-FEAT-003-010 | FEAT-003 → concluida (INC-003) | MRs → develop |

## Histórico incremental

| Data ou referência | Tipo | ID | Status anterior | Novo status | Conclusão ou evento | Evidências e comandos | Pendências ou próxima ação |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 2026-08-21 | DOC | PROJECT_STATE | — | criado | Criação controlada via `ensure-project-state.cjs` (P-16) | Helper status `created` | Preencher rastreabilidade FEAT-000 |
| 2026-08-21 | TASK | TASK-INC0-001 | não_registrada | concluida | P-16 `aprovar` | Diff `3e05717`: ADR-002/003/004 | Confirmar ADR-004 (humano) |
| 2026-08-21 | FEATURE | FEAT-000 | não_registrada | em_andamento | Feature aberta; 1/8 | TASK_BREAKDOWN INC-000 | Continuar |
| 2026-08-21 | TASK | TASK-INC0-002 | pendente | concluida | P-16 `aprovar` | Diff API `0def909` | — |
| 2026-08-21 | FEATURE | FEAT-000 | em_andamento | em_andamento | 2/8 | 001–002 | Continuar |
| 2026-08-21 | TASK | TASK-INC0-003 | pendente | concluida | P-16 `aprovar` | Diff API `286cfe3` schema V001 | `$pda-run TASK-INC0-004` |
| 2026-08-21 | FEATURE | FEAT-000 | em_andamento | em_andamento | 3/8 | 001–003 | Catálogo 004 |
| 2026-08-21 | TASK | TASK-INC0-004 | pendente | concluida | P-16 `aprovar` | Diff PDA `68eaa09`: `CATALOGO_ACOES_USUARIO_GESTOR.md` + README; matriz Usuário×Gestor; disclaimer não provisionado; actionIds como proposta; D09/D10/RN-02; sem Web/API; sem segredos. Comandos: `git show 68eaa09`; leitura task. | 005/006 bloqueadas sem PROV; smokes 007/008; INC-000 mínimo (001–004 + opcional 007/008) viabiliza prepare FEAT-001 com identidade pendente |
| 2026-08-21 | FEATURE | FEAT-000 | em_andamento | em_andamento | 4/8 tasks concluídas | 001–004 | Identidade/smokes ou FEAT-001 |
| 2026-08-21 | ADR | ADR-004 | recomendada | Aceita | Confirmação humana: adotar auth-blueprint neste sistema | Decisão registrada em ADR-004 | Completar PROV-001 (issuer/redirect) e PROV-002 |
| 2026-08-21 | PROV | PROV-001 | nao_disponivel | parcial | Evidência humana: client_id + sigla Portal `RESREC` | `docs/architecture/PROVISIONING.md` | Informar issuer, redirect_uri, URL PDP; PROV-002 |
| 2026-08-21 | PROV | PROV-001 | parcial | quase_completo | Issuer/discovery, redirect/silent e PDP URL preenchidos a partir dos templates Smart (`-web`/`-api`) | PROVISIONING.md atualizado | PROV-002 ainda bloqueia 005/006 |
| 2026-08-21 | PROV | PROV-002 | nao_disponivel | catalogo_definido | Pacotes Usuário/Gestor → actionIds no padrão Smart (`perfil_portal-policy`, Dominio.C/I/A/E) + matriz PRD | CATALOGO + PROVISIONING | Cadastro Portal; `$pda-run` 005/006 |
| 2026-08-21 | TASK | TASK-INC0-005 | pendente | concluida | P-16 `aprovar` | Diff API `5e95e11`: `environment-id=RESREC`; README/AGENTS identidade PROV-001/002; issuer/PDP desenv documentados; sem login local; sem Web; sem `client_secret`. `git show 5e95e11`. Permit real ainda depende cadastro Portal. | `$pda-run TASK-INC0-006` |
| 2026-08-21 | FEATURE | FEAT-000 | em_andamento | em_andamento | 5/8 tasks concluídas | 001–005 | Identidade Web 006 ou FEAT-001 |
| 2026-08-21 | TASK | TASK-INC0-006 | pendente | concluida | P-16 `aprovar` | Diff Web `28de614`: `idSistemaPortal=RESREC`, `clientId` PROV-001, SSO/PDP hosts Smart, redirect/silent; README/AGENTS; sem senha local; sem API. `git show 28de614`. | Smokes 007/008 ou `$pda-prepare FEAT-001` |
| 2026-08-21 | FEATURE | FEAT-000 | em_andamento | em_andamento | 6/8 tasks concluídas | 001–006 | INC-000 mínimo identidade ok; smokes opcionais |
| 2026-08-21 | TASK | TASK-INC0-007 | pendente | bloqueada | Smoke: bloqueio ambiental documentado | PATH: Java 1.4.2; sem `javac`/`mvn`; sem JDK 21; DBeaver JRE 25 sem compiler. README API atualizado. | Instalar JDK 21 + Maven; reexecutar `mvn test` |
| 2026-08-21 | TASK | TASK-INC0-008 | pendente | parcial | Build OK; test falha preexistente | `npm run build` exit 0 → `dist/smartng-sakai`; `ng test --watch=false --browsers=ChromeHeadless` TS2339 `app.title` em `app.component.spec.ts` (template). README Web atualizado. | Opcional alinhar spec; não gate INC-000 |
| 2026-08-21 | TASK | TASK-INC0-007 | bloqueada | parcial | Toolchain instalada; smoke reexecutada | JDK 21.0.12 + Maven 3.9.9; `mvn test` default falha log4j (profile local); `-P!local,desenv` → 12/13 OK, falha `deveObterClientesPaginados` 500 (template). README API. | Corrigir profile local ou teste paginado (opcional) |
| 2026-08-24 | TASK | TASK-INC0-007 | parcial | concluida | Profile local sem logging Logback; teste paginado com stubs `any` + sort DESC | `mvn test` → BUILD SUCCESS, Tests run: 13, Failures: 0 | `$pda-prepare FEAT-001` ou smoke Web 008 |
| 2026-08-24 | TASK | TASK-INC0-007 | concluida | concluida | P-16 `aprovar` | Diff API `2c99e49` (+ docs smoke); repo-alvo API ok; escopo smoke QCMD-007; `mvn test` revalidado 13/13 BUILD SUCCESS; domínio Cliente preservado; README registra ≠ aceite produto. Achado baixo: correções foram de falhas preexistentes do template (autorizado como correção mínima). | Smoke Web 008 ou `$pda-prepare FEAT-001` |
| 2026-08-24 | TASK | TASK-INC0-008 | parcial | concluida | Spec AppComponent alinhado ao shell OIDC; build+test verdes | `npm run build` exit 0; `npx ng test --watch=false --browsers=ChromeHeadless` → TOTAL: 3 SUCCESS | `$pda-validate TASK-INC0-008` |
| 2026-08-24 | TASK | TASK-INC0-008 | concluida | concluida | P-16 `aprovar` | Diff Web `b40abd5`; repo-alvo Web ok; escopo smoke QCMD-003/004; revalidado `npm run build` exit 0 + `ng test` headless TOTAL 3 SUCCESS; README ≠ aceite produto. Achado baixo: flags `--watch=false --browsers=ChromeHeadless` fora do script `npm run test` (watch) — documentado. | `$pda-prepare FEAT-001` |
| 2026-08-24 | FEATURE | FEAT-000 | em_andamento | concluida | INC-000 completo; Spec/Impl Plan/Breakdown FEAT-001 gerados (P-12/P-13/P-14) | PROJECT_STATE + `docs/features/FEAT-001/` | `$pda-run TASK-FEAT-001-001` |
| 2026-08-24 | FEATURE | FEAT-001 | não_registrada | pronta_para_execucao | P-14 Task Breakdown: 11 tasks (api/web/pda); full-stack separado; RVW-003/007 isolados; Permit Portal pendente | `docs/features/FEAT-001/TASK_BREAKDOWN.md` | `$pda-run TASK-FEAT-001-001` |
| 2026-08-24 | TASK | TASK-FEAT-001-001 | — | pendente | Criada no breakdown | OpenAPI catálogo (api) | `$pda-run TASK-FEAT-001-001` |
| 2026-08-24 | TASK | TASK-FEAT-001-001 | pendente | concluida | P-16 `aprovar` | Diff API `e6720c0`: `openapi/openapi.yaml` v0.2.0 paths `/categorias*` `/recursos*` + README; js-yaml OK; sem Web/JPA; MR draft !2 → develop; context-path template preservado; erros 400/403/404/409 distinguíveis. Achado baixo: `develop` API sem ancestral FEAT-000 (openapi/schema) — branch feature criada de develop. | `$pda-run TASK-FEAT-001-002` |
| 2026-08-24 | FEATURE | FEAT-001 | pronta_para_execucao | em_andamento | 1/11 task concluída (001) | P-16 001 | `$pda-run TASK-FEAT-001-002` |
| 2026-08-24 | TASK | TASK-FEAT-001-002 | — | pendente | Criada no breakdown | Módulo categoria API | Após 001 |
| 2026-08-24 | TASK | TASK-FEAT-001-003 | — | pendente | Criada no breakdown | Módulo recurso API | Após 002 |
| 2026-08-24 | TASK | TASK-FEAT-001-004 | — | pendente | Criada no breakdown | Testes domínio API; RN-02 auth ↔ PROV-002 | Após 003 |
| 2026-08-24 | TASK | TASK-FEAT-001-005 | — | pendente | Criada no breakdown | Serviços/rotas/menu Web | Após contrato |
| 2026-08-24 | TASK | TASK-FEAT-001-006 | — | pendente | Criada no breakdown | UI gestor categorias | Após 005 |
| 2026-08-24 | TASK | TASK-FEAT-001-007 | — | pendente | Criada no breakdown | UI gestor recursos | Após 005 |
| 2026-08-24 | TASK | TASK-FEAT-001-008 | — | pendente | Criada no breakdown | UI consulta usuário; sem fechar RVW-003 | Após 005 |
| 2026-08-24 | TASK | TASK-FEAT-001-009 | — | pendente | Criada no breakdown | Build+test Web | Após UIs |
| 2026-08-24 | TASK | TASK-FEAT-001-010 | — | pendente | Criada no breakdown | Validação integrada (pda); aceite auth ↔ PROV Portal | Após 004+009 |
| 2026-08-24 | TASK | TASK-FEAT-001-011 | — | pendente | Criada no breakdown | Fechamento documental | Após 010 |
| 2026-08-24 | TASK | TASK-FEAT-001-001 | concluida | concluida | P-16 `aprovar` reconfirmado (pedido humano) | OpenAPI v0.2.0 ainda em `e6720c0`; ADR-002 ok; sem Web/JPA no diff da task | — |
| 2026-08-24 | TASK | TASK-FEAT-001-002 | pendente | concluida | P-16 `aprovar` | Diff `8aeedb4`; RN-04/05; Categoria.*; `mvn test` exit 0 (2026-08-24) | — |
| 2026-08-24 | TASK | TASK-FEAT-001-003 | pendente | concluida | P-16 `aprovar` | Diff `41a08bb`; RN-03/06/07 | — |
| 2026-08-24 | TASK | TASK-FEAT-001-004 | pendente | concluida | P-16 `aprovar` | Diff `6a7d8fc`; README pendência PROV-002; `mvn test` exit 0 | — |
| 2026-08-24 | TASK | TASK-FEAT-001-005 | pendente | concluida | P-16 `aprovar` | Diff Web `af2934d`; serviços/rotas/menu | — |
| 2026-08-24 | TASK | TASK-FEAT-001-006 | pendente | concluida | P-16 `aprovar` | Diff Web `bce7795` | — |
| 2026-08-24 | TASK | TASK-FEAT-001-007 | pendente | concluida | P-16 `aprovar` | Diff Web `304d3cd` | — |
| 2026-08-24 | TASK | TASK-FEAT-001-008 | pendente | concluida | P-16 `aprovar` | Diff Web `b4ccd04`; RVW-003 isolado | — |
| 2026-08-24 | TASK | TASK-FEAT-001-009 | pendente | concluida | P-16 `aprovar` | `ng test` 17 SUCCESS; build ok (2026-08-24) | — |
| 2026-08-24 | TASK | TASK-FEAT-001-010 | pendente | concluida | P-16 `aprovar_com_ressalvas` | Humano pediu validar 002–011; sem E2E autenticado (PROV-002); aceite baseado em suítes API+Web verdes | Portal |
| 2026-08-24 | TASK | TASK-FEAT-001-011 | pendente | concluida | P-16 `aprovar` | Fechamento documental via PROJECT_STATE; FEAT-001 → concluida | `$pda-prepare FEAT-002` |
| 2026-08-24 | FEATURE | FEAT-001 | em_andamento | concluida | 11/11 tasks concluídas (010 com ressalva PROV) | P-16 lote 001–011 | `$pda-prepare FEAT-002` |
| 2026-08-24 | FEATURE | FEAT-002 | não_registrada | pronta_para_execucao | P-14 Task Breakdown: 12 tasks (api/web/pda); RN-10/ADR-003/D10; TP-RVW-004/PROV isolados; sem D09 | `docs/features/FEAT-002/TASK_BREAKDOWN.md` | `$pda-run TASK-FEAT-002-001` |
| 2026-08-24 | TASK | TASK-FEAT-002-001 | — | pendente | Criada no breakdown P-14 | OpenAPI reserva usuário (api) | `$pda-run TASK-FEAT-002-001` |
| 2026-08-24 | TASK | TASK-FEAT-002-002 | — | pendente | Criada no breakdown | Domínio + conflito ADR-003 | Após 001 |
| 2026-08-24 | TASK | TASK-FEAT-002-003 | — | pendente | Criada no breakdown | ReservaService | Após 002 |
| 2026-08-24 | TASK | TASK-FEAT-002-004 | — | pendente | Criada no breakdown | ReservaController | Após 003 |
| 2026-08-24 | TASK | TASK-FEAT-002-005 | — | pendente | Criada no breakdown | Testes API reserva | Após 004 |
| 2026-08-24 | TASK | TASK-FEAT-002-006 | — | pendente | Criada no breakdown | Serviços/rotas Web | Após 001 |
| 2026-08-24 | TASK | TASK-FEAT-002-007 | — | pendente | Criada no breakdown | UI nova reserva | Após 006 |
| 2026-08-24 | TASK | TASK-FEAT-002-008 | — | pendente | Criada no breakdown | UI minhas + cancel | Após 006 |
| 2026-08-24 | TASK | TASK-FEAT-002-009 | — | pendente | Criada no breakdown | UI alterar reserva | Após 008 |
| 2026-08-24 | TASK | TASK-FEAT-002-010 | — | pendente | Criada no breakdown | Build+test Web | Após 007–009 |
| 2026-08-24 | TASK | TASK-FEAT-002-011 | — | pendente | Criada no breakdown | Validação integrada (pda) | Após 005+010 |
| 2026-08-24 | TASK | TASK-FEAT-002-012 | — | pendente | Criada no breakdown | Fechamento documental | Após 011 |
| 2026-08-24 | TASK | TASK-FEAT-002-001 | pendente | concluida | OpenAPI v0.3.0 mergeado em develop API | paths `/reservas*` | `$pda-run TASK-FEAT-002-002` |
| 2026-08-24 | FEATURE | FEAT-002 | pronta_para_execucao | em_andamento | 1/12 task concluída (001) | OpenAPI em develop | `$pda-run TASK-FEAT-002-002` |
| 2026-08-24 | TASK | TASK-FEAT-002-002 a 005 | pendente | concluida | Módulo reserva API | API `b88a604`; `mvn test` 32/32; ADR-003/RN-10/D10 | MR API feature/FEAT-002 |
| 2026-08-24 | TASK | TASK-FEAT-002-006 a 010 | pendente | concluida | UI reserva Web | Web `9d08e7b`; build + `ng test` 19 SUCCESS | MR Web feature/FEAT-002 |
| 2026-08-24 | TASK | TASK-FEAT-002-011 | pendente | concluida | Validação integrada | Sem E2E SSO (PROV-002); suítes API+Web verdes | Portal |
| 2026-08-24 | TASK | TASK-FEAT-002-012 | pendente | concluida | Fechamento documental | FEAT-002 → concluida (ressalva PROV/TP-RVW-004) | `$pda-prepare FEAT-003` |
| 2026-08-24 | FEATURE | FEAT-002 | em_andamento | concluida | 12/12 tasks (011 ressalva PROV) | Implementação completa local | `$pda-prepare FEAT-003` |
| 2026-08-24 | DOC | feature/FEAT-002-docs | — | publicado | Docs FEAT-001/002 + PROJECT_STATE | PDA branch `feature/FEAT-002-docs` | MR → develop |
| 2026-08-24 | FEATURE | FEAT-003 | nao_iniciada | pronta_para_execucao | P-12/P-13/P-14 prepare completo | `docs/features/FEAT-003/` (Spec, Impl Plan, Breakdown); PRD 19.2 isolado; PROV-002 E2E pendente | `$pda-run TASK-FEAT-003-001` |
| 2026-08-24 | TASK | TASK-FEAT-003-001 a 011 | — | pendente | Criadas no breakdown P-14 | 11 tasks api/web/pda; D09/RN-11a/RN-13/aceite 13 | `$pda-run TASK-FEAT-003-001` |
| 2026-08-24 | TASK | TASK-FEAT-003-001 | pendente | concluida | OpenAPI v0.4.0 admin reserva | API branch `feature/FEAT-003`; paths `/reservas/administracao*`; ReservaFiltroAdministracao | `$pda-run TASK-FEAT-003-002` |
| 2026-08-24 | FEATURE | FEAT-003 | pronta_para_execucao | em_andamento | 1/11 task concluída (001) | OpenAPI 0.4.0 | `$pda-run TASK-FEAT-003-002` |
| 2026-08-24 | TASK | TASK-FEAT-003-002 | pendente | concluida | Service/Repository gestor D09 | ReservaFiltroAdministracaoDto; listarTodas/alterarComoGestor/cancelarComoGestor; mvn test 39/39 | `$pda-run TASK-FEAT-003-003` |
| 2026-08-24 | FEATURE | FEAT-003 | em_andamento | em_andamento | 2/11 tasks (001–002) | API feature/FEAT-003 | `$pda-run TASK-FEAT-003-003` |
| 2026-08-24 | TASK | TASK-FEAT-003-003 a 011 | pendente | concluida | Lote FEAT-003 implementação completa | API controller+tests 43/43; Web admin-reservas; build+ng test 21; validação aceite 13 domínio | MR feature/FEAT-003 → develop (api/web/pda) |
| 2026-08-24 | FEATURE | FEAT-003 | em_andamento | concluida | INC-003 completo; 11/11 tasks (010 ressalva PROV-002) | PRD 19.2 autoria UI não implementada | Homologação Portal; MRs |
