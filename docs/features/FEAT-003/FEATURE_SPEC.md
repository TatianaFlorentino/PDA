# FEATURE_SPEC — FEAT-003

| Campo | Conteúdo |
| --- | --- |
| ID | FEAT-003 |
| Nome | Consultar e administrar todas as reservas (gestor) |
| Produto | Reserva Corporativa (nome provisório) |
| Incremento | INC-003 |
| Status deste artefato | Gerado via P-12 (`pda-create-feature-spec`) |
| Data | 2026-08-24 |
| Fontes | `DEVELOPMENT_PLAN.md`; `PRD.md` v0.2.0; `PRD_REVIEW.md`; `TECHNICAL_PLAN.md` §§8–9; `QUALITY_PLAN.md`; `PROJECT_STATE.md`; `PROVISIONING.md`; `CATALOGO_ACOES_USUARIO_GESTOR.md`; `FEAT-002/FEATURE_SPEC.md`; OpenAPI v0.3.0; código `-api` módulo `reserva` (FEAT-002) |
| Conclusão P-12 | **pronta para IMPLEMENTATION_PLAN** |

---

## 1. ID e nome

**FEAT-003 — Consultar e administrar todas as reservas (gestor)**

Capacidade vertical da **área administrativa de reservas**: o gestor consulta **todas** as reservas (com filtros), visualiza o responsável e aplica **D09** — alterar ou cancelar **qualquer** reserva, inclusive já iniciada ou em andamento — com recálculo de conflito (RN-10 / ADR-003) e enforcement de autorização na API (RN-02).

---

## 2. Trilha

**Reserva / gestão** (Development Plan §7, INC-003).

Trilha de complexidade **média-alta**: Feature Spec **obrigatória**; IMPLEMENTATION_PLAN **recomendado** (D09 + conflito na alteração de reserva já iniciada + distinção de escopo Usuário×Gestor na mesma actionId).

---

## 3. Repositórios afetados

| Papel | Repositório | Recorte nesta feature |
| --- | --- | --- |
| PDA | `recursos-corporativos-tati-pda` | Spec, Implementation Plan, Task Breakdown canônicos em `docs/features/FEAT-003/` |
| API | `recursos-corporativos-tati-api` | Listagem de **todas** as reservas com filtros; obter qualquer reserva; alterar/cancelar com regras **RN-11a/RN-13** (sem restrição “não iniciada”); recusa de escopo admin para Usuário (D10/RN-02); OpenAPI dos endpoints administrativos; `@PreAuthorize` `Reserva.C` / `Reserva.A` / `Reserva.E` |
| Web | `recursos-corporativos-tati-web` | Tela(s) gestor: consulta administrativa de reservas, filtros, alteração e cancelamento (confirmação); ocultar área para Usuário (UX); exibir responsável |

Não misturar alteração Web + API na mesma `TASK-*`. Validação integrada exercita o fluxo ponta a ponta sem unificar patch entre repositórios.

---

## 4. Objetivo

Permitir que:

1. o **gestor** **consulte todas as reservas** do sistema, com filtros úteis (recurso, situação, responsável, período — conforme Implementation Plan);
2. o **gestor** **altere qualquer reserva** (RN-11a / D09), inclusive já iniciada, desde que o período permaneça válido (RN-08) e sem conflito (RN-10);
3. o **gestor** **cancele qualquer reserva** (RN-13 / D09), inclusive já iniciada;
4. o **usuário comum** **não** acesse listagem/administração global nem mute reservas alheias ou já iniciadas (RN-02, D10) — a API recusa mesmo com URL direta;
5. o sistema atenda o **aceite 13** (PRD §18) e feche o núcleo administrativo de reservas da primeira versão.

---

## 5. Contexto

- FEAT-002 entregou reserva pelo **usuário**: criar, minhas reservas (D10), alterar/cancelar **própria não iniciada** (RN-11/12), conflito RN-10 (ADR-003), RN-14.
- FEAT-003 completa a **área administrativa de reservas** prevista no PRD §7 (área administrativa) e §5.2 (capacidades do gestor), sem repetir jornadas 9.1–9.3 (usuário).
- D09 e D10 estão **fechados** no PRD 0.2.0; esta feature **aplica** D09 sem reabrir a decisão.
- Catálogo `CATALOGO_ACOES_USUARIO_GESTOR.md`: mesmos actionIds `Reserva.C`, `Reserva.A`, `Reserva.E`; **escopo** (próprio vs todas; não iniciada vs qualquer) é regra de negócio na API após `Permit` — **sem** sufixo `.Admin`.
- Módulo `gov.goias.smart.reserva.*` já existe (FEAT-002); FEAT-003 **estende** serviço, repositório, controller e contrato — não duplica domínio.
- OpenAPI v0.3.0 documenta escopo usuário; endpoints administrativos estão explicitamente **fora** de FEAT-002 — entram nesta feature (ADR-002).
- PROV-002 (cadastro Portal) pendente: domínio e testes unitários/integração podem avançar; homologação autenticada do aceite 13 depende Portal.

---

## 6. Escopo e fora de escopo

### Em escopo

**Backend / contrato (`-api`)**

- Operação de **listar todas** as reservas (paginada), com filtros (mínimo: situação, recurso, identificador do responsável, intervalo de datas — detalhar no Implementation Plan).
- Operação de **obter** reserva por id **sem** restrição D10 (gestor).
- Operações de **alterar** e **cancelar** reserva com ramo **gestor** (RN-11a, RN-13): **sem** `validarNaoIniciada`; recálculo de conflito RN-10; RN-08; RN-14; recurso ativo quando aplicável (alteração que troca recurso ou cria elegibilidade — alinhar ao FEAT-002 / RN-06).
- Para alteração gestor de reserva **já iniciada**: **não** exigir início futuro (RN-09 aplica-se à **criação** e à alteração usuário; gestor pode ajustar término/finalidade/recurso conforme RN-11a — detalhar validações no Implementation Plan).
- Enforcement: Usuário com `Reserva.C` lista **somente minhas**; Usuário não lista todas; Usuário não altera/cancela alheia ou iniciada; Gestor acessa escopo ampliado.
- OpenAPI canônico descrevendo paths/schemas/erros administrativos (evoluir versão minor/patch acordada).
- `@PreAuthorize` `Reserva.C` / `Reserva.A` / `Reserva.E` nos endpoints entregues.

**Frontend (`-web`)**

- Área gestora: **consulta administrativa de reservas** (listagem com responsável visível).
- Filtros coerentes com API (situação, recurso, responsável, período — conforme contrato).
- Ações: **alterar** e **cancelar** com confirmação (PRD §10 — cancelamento exige confirmação); suporte a reserva **já iniciada** (D09).
- Estados de UI: carregamento, vazio, erro, sucesso.
- Menu/rota gestor; oculto para Usuário (`ngxPermissionsOnly` / padrão FEAT-001).

**Validação integrada**

- Gestor lista todas, altera e cancela reserva iniciada; conflito recalculado; Usuário não conclui operações admin na API (quando PROV permitir). Sem patch multi-repo na mesma task.

### Fora de escopo

- Criar reserva pelo gestor em nome de terceiro (PRD 9.1: responsável = autenticado) — gestor **cria** como qualquer autenticado com `Reserva.I` (FEAT-002); **não** reimplementar criação aqui salvo reuso de navegação.
- Minhas reservas, D10, jornadas 9.1–9.3 usuário (FEAT-002).
- CRUD categoria/recurso (FEAT-001).
- Itens PRD §8 (recorrência, e-mail, calendário, etc.).
- Login local; administração de permissões na aplicação.
- **Autoria visível** na UI quando gestor altera/cancela reserva de terceiro (PRD 19.2 — **não decidida**; **não inventar** registro de “quem operou”).
- Decidir TP-RVW-004 (fuso); PRD-RVW-003/007.
- Novos actionIds ou policy fora do catálogo Smart.
- Delete físico de reserva (RN-14).

---

## 7. Usuários / perfis

| Perfil | Capacidades nesta feature | actionIds (catálogo) |
| --- | --- | --- |
| **Usuário** | Sem acesso à listagem administrativa; sem alterar/cancelar alheia ou iniciada | `Reserva.C` (próprias), `Reserva.A`/`E` (própria não iniciada) — FEAT-002 |
| **Gestor** | Listar **todas**; obter qualquer; alterar/cancelar **qualquer** inclusive iniciada (D09) | `Reserva.C`, `Reserva.A`, `Reserva.E` (escopo ampliado na API) |
| **Anônimo** | Nenhuma | — |

Autorização **obrigatória** na API (RN-02). Ocultar menu no Web **não** substitui enforcement.

---

## 8. Regras de negócio

| ID | Aplicação em FEAT-003 |
| --- | --- |
| RN-01 | Autenticação corporativa obrigatória |
| RN-02 | Usuário não executa operações administrativas na API |
| RN-06 | Recurso inativo não recebe **nova** reserva; alteração que troca para inativo recusa (alinhado FEAT-002) |
| RN-08 | Fim posterior ao início |
| RN-10 | Conflito entre reservas **ativas**; recálculo na alteração gestor |
| RN-11a | Gestor altera **qualquer** reserva, inclusive iniciada, se período válido e sem conflito |
| RN-12 | **Não** aplica ao gestor (apenas usuário comum) |
| RN-13 | Gestor cancela **qualquer** reserva, inclusive iniciada |
| RN-14 | Cancelamento lógico; histórico preservado |
| D09 | Gestor altera/cancela inclusive iniciada/em andamento |
| D10 | Usuário vê só próprias — listagem global **gestor only** |

ADR-003: intervalo semiaberto `[início, fim)` na verificação de conflito — **reutilizar** `ConflitoReservaService` (FEAT-002).

---

## 9. Fluxos

### 9.1 Consultar reservas (gestor)

1. Gestor autenticado acessa área administrativa de reservas.
2. Sistema lista **todas** as reservas (paginada), exibindo recurso, período, finalidade, situação e **responsável**.
3. Gestor aplica filtros (recurso, situação, responsável, período).
4. Sistema atualiza a listagem conforme filtros.

*Nota:* PRD não descreve passo a passo esta jornada (cf. 9.4); capacidades estão em §5.2 e aceite 13.

### 9.2 Alterar reserva (gestor — D09 / RN-11a)

1. Gestor seleciona uma reserva (própria, de terceiro, futura ou **já iniciada**).
2. Informa alterações (recurso, início, fim, finalidade — conforme contrato).
3. Sistema valida RN-08, elegibilidade de recurso (RN-06 quando couber), conflito RN-10.
4. Reserva é atualizada; gestor recebe confirmação.

### 9.3 Cancelar reserva (gestor — D09 / RN-13)

1. Gestor seleciona reserva (inclusive **já iniciada**).
2. Solicita cancelamento; sistema pede **confirmação**.
3. Gestor confirma.
4. Situação passa a **Cancelada** (RN-14); período fica disponível para novas reservas ativas.

### 9.4 Tentativa de acesso indevido (usuário comum)

1. Usuário tenta listar todas ou alterar/cancelar reserva alheia/iniciada (UI oculta ou chamada direta à API).
2. API recusa (403 / escopo D10) — RN-02.

---

## 10. Casos de borda

| Caso | Comportamento esperado |
| --- | --- |
| Gestor altera reserva **iniciada** apenas estendendo o fim | Permitido se RN-08 e RN-10 OK; início no passado **não** bloqueia (D09) |
| Gestor altera reserva iniciada mudando início para futuro | Validar RN-08/RN-10; RN-09 **não** impede gestor em alteração (Implementation Plan) |
| Gestor cancela reserva **iniciada** | Permitido (RN-13); confirmação na UI |
| Usuário chama endpoint de listagem global | 403 ou equivalente escopo (RN-02/D10) |
| Alteração gestor gera conflito | 409 `RESERVA_CONFLITO` (padrão FEAT-002) |
| Reserva já **Cancelada** | Alterar/cancelar recusa (RN-14 / FEAT-002) |
| Filtro sem resultados | UI estado vazio |
| Paginação grande | Paginação Smart (padrão template) |
| TP-RVW-004 fuso | Relógio injetável; hipótese documentada nos testes — não decidir produto |

---

## 11. Critérios testáveis

Alinhados ao aceite **13** e Quality Plan:

1. Gestor consulta **todas** as reservas com responsável visível.
2. Gestor altera reserva **já iniciada** com sucesso quando regras OK.
3. Gestor cancela reserva **já iniciada** com sucesso.
4. Alteração gestor **reexecuta** verificação de conflito (RN-10).
5. Usuário comum **não** lista todas na API.
6. Usuário comum **não** altera/cancela reserva alheia ou iniciada na API.
7. Filtros retornam subconjunto correto (domínio/repositório).
8. Cancelamento exige confirmação na UI.
9. Reserva cancelada permanece consultável (RN-14).

Homologação E2E autenticada (aceite 13 completo) **bloqueada** sem PROV-002.

---

## 12. Impactos técnicos

- **Estender** `ReservaService`, `ReservaRepository`, `ReservaController` — ramos gestor vs usuário (detecção de escopo via identidade + autorização ou método dedicado — Implementation Plan).
- **Novos** paths OpenAPI (ex.: `/reservas/admin` ou query `escopo=todas` — fechar no Implementation Plan sem inventar coleção aqui).
- **Web:** nova página `admin-reservas` (ou equivalente kebab-case), serviço HTTP, rotas/menu gestor.
- **Sem** alteração de schema V001 (campos existentes suficientes).
- **Sem** novo actionId; distinção na lógica de serviço pós-Permit.

---

## 13. Segurança, privacidade, auditoria e permissões

- RN-02: barreira na API; testes de recusa para Usuário.
- D10: dados de terceiros **não** expostos ao Usuário na listagem.
- Gestor vê identificador do **responsável** (PRD aceite 13 / minimização — só o necessário).
- `@Historico` Smart nos métodos de mutação (padrão FEAT-002) — auditoria institucional do template; **não** substitui decisão PRD 19.2 sobre autoria visível na UI.
- PROV-002: perfil Gestor com `Reserva.C/I/A/E` no Portal.

---

## 14. Contratos entre frontend e backend

| Operação | Ator | Notas |
| --- | --- | --- |
| Listar todas (paginada + filtros) | Gestor | `Reserva.C` escopo ampliado |
| Obter reserva por id | Gestor | Qualquer reserva |
| Alterar reserva | Gestor | RN-11a; D09; 409 conflito |
| Cancelar reserva | Gestor | RN-13; confirmação UI |
| Listar minhas | Usuário | FEAT-002 — inalterado |
| Alterar/cancelar própria não iniciada | Usuário | FEAT-002 — inalterado |

Erros distinguíveis: 403 escopo, 409 conflito, 400 negócio, 404 não encontrado (QHML-007).

Contrato canônico no `-api` (ADR-002); Web consome snapshot publicado.

---

## 15. Testes iniciais

| Camada | Obrigatório (FEAT-003) | Observação |
| --- | --- | --- |
| Domínio | RN-11a, RN-13; gestor altera iniciada; conflito; cancelada recusa | Relógio injetável |
| API HTTP | D09; D10/RN-02 recusa Usuário; filtros | QRSK-003, QRSK-007 |
| Web | Listagem gestor; filtros; alterar/cancelar iniciada; confirmação | QHML-006..011 |
| E2E aceite 13 | Gestor admin reservas | **Bloqueado** PROV-002 |
| Regressão FEAT-002 | Minhas reservas usuário intactas | Suíte existente |

Comandos: `mvn test` (api); `npm run build` / `ng test` (web).

---

## 16. Dependências

| Dependência | Estado | Efeito em FEAT-003 |
| --- | --- | --- |
| FEAT-002 | **Concluída** | Módulo reserva, ADR-003, D10 usuário — **gate atendido** |
| FEAT-001 | Concluída | Filtro por recurso / nomes na UI |
| ADR-002/003/004 | Aceitos | OpenAPI; intervalo; auth blueprint |
| PROV-002 | Catálogo definido; Portal pendente | Permit real pendente |
| PROV-001 | Quase completo | SSO homologação |

---

## 17. Restrições

- Não implementar código nesta execução P-12.
- Não inventar autoria visível na UI (PRD 19.2).
- Não decidir TP-RVW-004, PRD-RVW-003/007.
- Não criar actionIds `.Admin` ou login local.
- Não fragmentar spec por camada; recortes backend, frontend e validação integrada na mesma FEAT-003.
- Não definir task única multi-repositório.
- Reutilizar templates Smart e módulo reserva FEAT-002.

---

## 18. Dúvidas

Pendências **isoladas** (Implementation Plan / decisão humana):

1. **PRD 19.2** — Registro visível de quem (gestor) alterou/cancelou reserva alheia: **não decidido**; implementação **sem** campo/trilha de “operador” até decisão.
2. **TP-RVW-004** — Fuso e “já iniciou” para D09; usar `RelogioReservaProvider` como FEAT-002.
3. **Shape exato dos filtros** — fechar no OpenAPI (Implementation Plan); mínimo alinhado a aceite 13 e operação gestor.
4. **Path HTTP admin** — prefixo dedicado vs parâmetro de escopo — fechar no Implementation Plan (ADR-002).
5. PROV-002 cadastro Portal para aceite 13 autenticado.

*Nota de rastreabilidade:* Development Plan cita “7.7–7.9”; o PRD vigente não possui essas subseções — capacidades mapeiam a **§5.2 Gestor**, **§7 área administrativa** e **aceite 13**.

---

## 19. Necessidade de IMPLEMENTATION_PLAN

**Sim — recomendado/obrigatório** (Development Plan §7 FEAT-003).

Motivos:

- Ramificação **D09** vs regras usuário no mesmo serviço.
- Alteração de reserva **iniciada** (RN-09 vs RN-11a).
- Paths OpenAPI admin, filtros, códigos de erro.
- Estratégia de detecção gestor (escopo API) sem novos actionIds.
- Integração Web listagem + dialogs reutilizando padrão `minhas-reservas`.

---

## 20. Notas técnicas (trilha média)

- Reutilizar `ConflitoReservaService`, `RelogioReservaProvider`, `IdentidadeReservaProvider`.
- Considerar métodos `alterarComoGestor` / `cancelarComoGestor` / `listarTodas` no `ReservaService` para clareza de regras — decisão de implementação no Plan.
- Web pode **reutilizar** componentes/dialogs de alteração/cancelamento de FEAT-002 com flag `modoGestor` (sem autoria inventada).
- Paginação e Problem responses: padrão `CategoriaController` / `ReservaController` FEAT-002.

---

## Conclusão P-12

| Veredito | **Pronta para IMPLEMENTATION_PLAN** |
| --- | --- |
| Ressalvas | PRD 19.2 autoria UI não decidida; PROV-002 pendente para E2E; TP-RVW-004 isolado |
| Bloqueios | Nenhum para prepare documental |
| Próximo passo | P-13 → `IMPLEMENTATION_PLAN.md`; P-14 → `TASK_BREAKDOWN.md` + `PROJECT_STATE.md` |
