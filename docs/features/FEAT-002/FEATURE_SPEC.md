# FEATURE_SPEC — FEAT-002

| Campo | Conteúdo |
| --- | --- |
| ID | FEAT-002 |
| Nome | Reservar recurso e gerir minhas reservas |
| Produto | Reserva Corporativa (nome provisório) |
| Incremento | INC-002 |
| Status deste artefato | Gerado via P-12 (`pda-create-feature-spec`) |
| Data | 2026-08-24 |
| Fontes | `DEVELOPMENT_PLAN.md`; `PRD.md` v0.2.0; `TECHNICAL_PLAN.md`; `QUALITY_PLAN.md`; `PROJECT_STATE.md`; `ADR-003`; `PROVISIONING.md`; `CATALOGO_ACOES_USUARIO_GESTOR.md`; schema V001 `RC_RESERVA`; FEAT-001 entregue |
| Conclusão P-12 | **pronta para IMPLEMENTATION_PLAN** |

---

## 1. ID e nome

**FEAT-002 — Reservar recurso e gerir minhas reservas**

Capacidade vertical de reserva pelo usuário: criar reserva pontual em recurso ativo, recusar conflito, consultar somente as próprias reservas (D10), alterar e cancelar reservas próprias ainda não iniciadas, preservando canceladas no histórico (RN-14).

---

## 2. Trilha

**Reserva** (Development Plan §7, INC-002).

Trilha de complexidade **média-alta** no sentido do plano: Feature Spec **obrigatória**; IMPLEMENTATION_PLAN **obrigatório** (conflito RN-10, corrida R-RACE, intervalo ADR-003, fuso TP-RVW-004).

---

## 3. Repositórios afetados

| Papel | Repositório | Recorte nesta feature |
| --- | --- | --- |
| PDA | `recursos-corporativos-tati-pda` | Spec, Implementation Plan e Task Breakdown canônicos em `docs/features/FEAT-002/` |
| API | `recursos-corporativos-tati-api` | Módulo reserva: criar, listar minhas, alterar, cancelar; RN-08–12, RN-14; conflito atômico (RN-10 / ADR-003); D10 no servidor; responsável = autenticado |
| Web | `recursos-corporativos-tati-web` | Jornadas usuário 9.1–9.3: criar reserva, minhas reservas, alterar/cancelar com confirmação |

Não misturar alteração Web + API na mesma `TASK-*`. Validação integrada exercita conflito e D10 ponta a ponta sem unificar patch entre repositórios.

---

## 4. Objetivo

Permitir que o **usuário autenticado** (perfil Usuário; gestor também pode executar estas jornadas, mas **sem** listar reservas de terceiros nesta feature):

1. **crie** reserva pontual em recurso **ativo**, informando início, término e finalidade — responsável = autenticado, sem seleção de outro usuário;
2. **receba recusa** quando o período conflitar com outra reserva **Ativa** do mesmo recurso (RN-10), aceitando reservas **consecutivas** (adjacentes);
3. **consulte somente as próprias** reservas (D10) — a API é a barreira, não só a UI;
4. **altere** reserva própria ainda **não iniciada**, se o período continuar válido e sem conflito (RN-11);
5. **cancele** reserva própria ainda **não iniciada**, com confirmação, mantendo registro **Cancelada** consultável (RN-12, RN-14);
6. **não crie** reserva em recurso inativo (RN-06 / aceite 14 — catálogo FEAT-001 + validação na criação).

---

## 5. Contexto

- PRD §7 define a primeira entrega com criação, alteração e cancelamento de reservas pelo usuário, além de consulta de recursos (FEAT-001) e minhas reservas (D10).
- **FEAT-001 concluída** (`PROJECT_STATE.md`, 2026-08-24): catálogo operacional; recurso inativo exposto e impedido para nova reserva na API de catálogo; OpenAPI v0.2.0 com paths de categoria/recurso.
- Schema V001 (`RC_RESERVA`) já modela reserva: `ID_RECURSO`, `DH_INICIO`, `DH_FIM`, `FINALIDADE`, `ID_RESPONSAVEL`, `SITUACAO` (`ATIVA`/`CANCELADA`); constraint de período (`DH_FIM > DH_INICIO`); índice por recurso+período; **sem** constraint de overlap — detalhe FEAT-002 (R-RACE).
- **ADR-003** aceita semântica de intervalo semiaberto `[início, fim)` para RN-10; fuso de “já iniciou” permanece hipótese (TP-RVW-004).
- **D09** (gestor altera/cancela qualquer reserva, inclusive iniciada) e listagem administrativa de **todas** as reservas ficam em **FEAT-003** — não implementar aqui, embora RN-11a/RN-13 existam no PRD.
- PROV-001 quase completo (identidade do responsável); PROV-002 catálogo definido com `Reserva.*`, cadastro Portal **pendente** — homologação autenticada e Permit real ficam pendentes; domínio pode avançar de forma reversível.

---

## 6. Escopo e fora de escopo

### Em escopo

**Backend / contrato (`-api`)**

- Criar reserva: recurso ativo, período válido (RN-08, RN-09), responsável = identificador do autenticado (D08), situação inicial **Ativa**.
- Verificação de conflito RN-10 com semântica ADR-003: intervalo semiaberto; canceladas excluídas; adjacência permitida; implementação **atômica** (transação/constraint) — mitigar R-RACE.
- Listar **minhas** reservas (D10): filtro pelo identificador do responsável autenticado — Usuário **não** obtém reservas alheias por API.
- Alterar reserva própria não iniciada (RN-11): pode mudar recurso, início, término, finalidade; reexecuta conflito e validações de período.
- Cancelar reserva própria não iniciada (RN-12): transição para **Cancelada**; **sem** delete físico (RN-14); período volta a ficar disponível para conflito.
- Recusar criação/alteração em recurso inativo (RN-06).
- OpenAPI canônico no `-api` descrevendo operações de reserva do usuário entregues.
- Autorização via actionIds `Reserva.C/I/A/E` conforme catálogo; escopo D10 aplicado **na API** após Permit (mesmo actionId, regra de negócio no servidor).

**Frontend (`-web`)**

- Jornada 9.1 — reservar recurso: seleção de recurso (consumindo catálogo FEAT-001), formulário início/término/finalidade, feedback de sucesso/erro.
- Jornada 9.2 — conflito: mensagem de indisponibilidade no período; erro distinguível de autorização.
- Jornada 9.3 — cancelar: lista minhas reservas, seleção de futura, **confirmação** antes de cancelar.
- Alteração de reserva futura própria (aceite 10).
- Estados de UI: carregamento, vazio, erro, sucesso.
- Rotas/menu na área do usuário; gestor vê as mesmas telas de usuário para **próprias** reservas — listagem de terceiros = FEAT-003.

**Validação integrada**

- Conflito e D10 exercitados ponta a ponta (API + Web); sem patch multi-repo na mesma task.
- Aceites 6–11 e 15 quando PROV permitir; até lá, domínio e testes com relógio injetável.

### Fora de escopo

- Listar **todas** as reservas; filtros administrativos; alterar/cancelar reserva de terceiro ou **já iniciada** pelo gestor (D09, RN-11a, RN-13, aceite 13) — **FEAT-003**.
- Itens da seção 8 do PRD (recorrência, e-mail, calendário, mobile, fila, aprovação, etc.).
- Administração de permissões na aplicação; login/senha local.
- Decidir PRD-RVW-003 (lista de recursos inativos para usuário) ou PRD-RVW-007 (categoria inativa × nova reserva) — **só referenciar** se impacto na elegibilidade do recurso; não inventar regra extra.
- Decidir TP-RVW-004 (fuso institucional) — isolar como pendência; hipótese ADR-003: relógio do servidor da API no fuso do ambiente.
- Constraint SQL de overlap em V001 (nota do schema: FEAT-002); nova migração só se Implementation Plan justificar.
- Seed obrigatório de reservas; carga de produção.
- Paths HTTP finais e códigos de status exatos além do nível de produto — fechar no OpenAPI / Implementation Plan.

---

## 7. Usuários / perfis

| Perfil | Nesta feature |
| --- | --- |
| **Usuário** | `Reserva.I` criar; `Reserva.C` consultar **próprias**; `Reserva.A` alterar **própria não iniciada**; `Reserva.E` cancelar **própria não iniciada**. Consulta recursos via `Recurso.C` (FEAT-001). |
| **Gestor** | Mesmos actionIds de reserva para jornadas de usuário; **não** usa nesta feature listagem de todas nem D09 — escopo na API distingue perfil quando necessário, mas capacidades administrativas de reserva = FEAT-003. |
| Anônimo | Sem uso de funcionalidades protegidas (RN-01). |

Referência de actionIds: `docs/architecture/CATALOGO_ACOES_USUARIO_GESTOR.md` e `PROVISIONING.md` (PROV-002). Escopo próprio vs todos **não** usa sufixo `.Admin` — regra na API após Permit.

**D10:** usuário comum **só vê/edita/cancela próprias** reservas; tentativa de acessar reserva alheia por ID ou listagem ampla deve ser recusada na **API**.

---

## 8. Regras de negócio

Regras do PRD §10 aplicáveis a FEAT-002 (não inventadas):

| ID | Regra aplicável a FEAT-002 |
| --- | --- |
| RN-01 | Utilização autenticada; responsável derivado da identidade corporativa. |
| RN-02 | Usuário não executa operações administrativas de reserva (listar todas, D09) — FEAT-003; API recusa. |
| RN-06 | Recurso inativo **não recebe novas reservas**; reservas anteriores consultáveis. FEAT-001 expõe elegibilidade; FEAT-002 valida na criação/alteração de recurso. |
| RN-08 | Data/hora final posteriores à inicial. |
| RN-09 | Não criar reserva com horário inicial já ocorrido. |
| RN-10 | Mesmo recurso: duas reservas **Ativas** não podem sobrepor; consecutivas permitidas; **Canceladas** fora da checagem. Semântica ADR-003: `[início, fim)`; adjacência se `A_fim == B_início`. |
| RN-11 | Usuário altera **própria** reserva se **ainda não iniciou**, período válido, sem conflito; pode mudar recurso, início, término, finalidade. |
| RN-12 | Usuário cancela **própria** reserva somente se **ainda não iniciou**. |
| RN-14 | Cancelada permanece no histórico funcional, consultável como **Cancelada** — sem exclusão física. |

**Fora desta feature (referência apenas):** RN-11a, RN-13 (D09 — gestor); RN-03–07 (catálogo — FEAT-001).

**Atributos da reserva (PRD §10)**

- Recurso, data/hora inicial, data/hora final, finalidade/motivo, usuário responsável (automático), situação **Ativa** ou **Cancelada**.

**Explícito nas funcionalidades**

- Cancelamento exige confirmação (jornada 9.3).
- Alteração reexecuta verificação de conflito.
- Responsável = autenticado; **não** selecionar outro usuário na criação.

**“Já iniciou” (RN-11, RN-12)**

- Depende de relógio/fuso (**TP-RVW-004 não decidido**). ADR-003 hipótese: relógio do servidor da API no fuso configurado do ambiente. Implementation Plan deve documentar fuso efetivo nos testes; **não inventar** horário comercial nem fuso institucional.

**Não inventar**

- PRD-RVW-003: impacto indireto — escolha de recurso inativo na UI de reserva segue catálogo FEAT-001; não prescrever lista do usuário.
- PRD-RVW-007: categoria inativa × elegibilidade de recurso para **nova** reserva — **não decidido**; API deve isolar ponto sem hardcode silencioso (Plano Técnico §8).
- Comportamento detalhado de reserva **Ativa já iniciada** para usuário comum além de “não altera/não cancela” — PRD 19.1 lacuna; consultável permanece; alteração/cancelamento recusados (RN-11/12).

---

## 9. Fluxos

### 9.1 Reservar um recurso (PRD 9.1)

1. Usuário autenticado acessa fluxo de reserva.
2. Consulta/seleciona recurso (catálogo FEAT-001 — ativo para nova reserva).
3. Informa início, término e finalidade.
4. Sistema valida RN-08, RN-09, RN-06 (recurso ativo).
5. Sistema verifica conflito RN-10 (ADR-003).
6. Sem conflito: persiste reserva **Ativa** com responsável = autenticado.
7. Informa sucesso.

### 9.2 Tentativa de reserva conflitante (PRD 9.2)

1. Usuário seleciona recurso e período sobreposto a reserva **Ativa** existente.
2. Sistema identifica conflito; **não** persiste nova reserva.
3. Usuário recebe mensagem de indisponibilidade no período (distinta de erro de auth).

### 9.3 Cancelar uma reserva (PRD 9.3)

1. Usuário consulta **minhas** reservas (D10).
2. Seleciona reserva futura (não iniciada).
3. Solicita cancelamento.
4. Sistema solicita **confirmação**.
5. Usuário confirma.
6. Situação → **Cancelada**; período disponível para novas reservas; registro permanece (RN-14).

### 9.4 Alterar reserva futura própria (aceite 10; jornada não passo a passo no PRD)

1. Usuário em minhas reservas seleciona reserva **própria não iniciada**.
2. Altera recurso, período e/ou finalidade.
3. Sistema valida RN-08, RN-09, RN-06, RN-11, RN-10.
4. Persiste ou recusa com erro compreensível.

### 9.5 Validação integrada (macro)

Usuário A cria reserva → Usuário B tenta conflito (recusa) → consecutivas ok → A lista minhas (não vê B) → A altera/cancela futura → cancelada permanece no histórico.

---

## 10. Casos de borda

| Caso | Comportamento esperado |
| --- | --- |
| Fim ≤ início (RN-08) | Recusa validação. |
| Início no passado na criação (RN-09) | Recusa. |
| Overlap com reserva Ativa (RN-10) | Recusa; mensagem de indisponibilidade. |
| Consecutivas 09:00–10:00 e 10:00–11:00 (RN-10 / ADR-003) | Ambas Ativas — **sem** conflito. |
| Reserva Cancelada no período | Não entra na checagem de conflito. |
| Criar/alterar para recurso inativo (RN-06) | Recusa. |
| Usuário altera/cancela reserva **já iniciada** (RN-11/12) | Recusa. |
| Usuário tenta alterar/cancelar reserva **alheia** (D10) | Recusa na API (404 ou 403 conforme Implementation Plan — sem vazar existência indevida). |
| Usuário lista reservas | Somente onde `ID_RESPONSAVEL` = autenticado (D10). |
| Gestor na jornada usuário | Mesmas restrições D10 na listagem “minhas”; administração alheia = FEAT-003. |
| Duas criações simultâneas mesmo slot (R-RACE) | Uma persiste; outra recusa conflito — transação/constraint atômica. |
| Cancelamento | Confirmação obrigatória; situação Cancelada; não delete físico. |
| Recurso de categoria inativa (PRD-RVW-007) | **Não decidido** — isolar; não inventar bloqueio extra além de RN-06 no recurso. |
| Fuso / virada de dia | Testes documentam hipótese ADR-003; decisão TP-RVW-004 pendente. |

---

## 11. Critérios testáveis

Alinhados ao PRD §18 (aceites 6–11, 15), Development Plan INC-002 e Quality Plan (QHML / QRSK):

| # | Critério | Evidência |
| --- | --- | --- |
| 1 | Usuário cria reserva em recurso ativo (aceite 6 / QHML-006); responsável = autenticado | UI + API |
| 2 | Sistema impede reserva conflitante (aceite 7 / QHML-007); erro distinguível de 401/403 | UI + API |
| 3 | Reservas consecutivas permitidas (aceite 8 / QHML-008 / QRSK-015) | Domínio + API |
| 4 | Usuário consulta **somente** próprias reservas (aceite 9 / D10 / QHML-009 / QRSK-003) | API obrigatório; UI |
| 5 | Usuário altera reserva futura própria válida (aceite 10 / QHML-010); recusa se já iniciou | UI + API — relógio documentado |
| 6 | Usuário cancela reserva futura própria com confirmação (aceite 11 / QHML-011) | UI + API |
| 7 | Recurso inativo não recebe nova reserva (aceite 14 / QHML-014 / RN-06) | API na criação/alteração |
| 8 | Canceladas permanecem no histórico (aceite 15 / QHML-015 / RN-14) | UI + consulta |
| 9 | Período inválido e início passado recusados (RN-08/09 / QHML-017) | Domínio + API |
| 10 | Conflito atômico sob concorrência (R-RACE / QRSK-005) | Integração persistência — **recomendado** corrida |
| 11 | OpenAPI descreve operações de reserva entregues | Contrato |
| 12 | Anônimo recusado em operações protegidas | API — quando auth disponível |
| 13 | Não há teste que “feche” PRD-RVW-007 (QRSK-013) | Spec de teste |

**Aceites 12 e 13:** aceite 12 parcialmente FEAT-001..003; aceite 13 = FEAT-003 — **fora** dos critérios de fechamento de FEAT-002.

---

## 12. Impactos técnicos

| Área | Impacto |
| --- | --- |
| API | Módulo `reserva` (Plano Técnico §10); serviço de conflito no domínio; transação/constraint R-RACE; `@PreAuthorize` `Reserva.*`; filtro D10 em listagem/alteração/cancelamento |
| Persistência | `RC_RESERVA` V001; possível V002 para constraint de overlap — decisão no Implementation Plan; `ID_RESPONSAVEL` mínimo (D08) |
| OpenAPI | Evoluir contrato com paths de reserva usuário (criar, minhas, alterar, cancelar) |
| Web | Rotas reserva + minhas reservas; formulários; confirmação cancelamento; serviços HTTP tipados |
| Catálogo FEAT-001 | Consumo de recursos ativos; RN-06 já preparado |
| ADR-003 | Intervalo semiaberto obrigatório nos testes de domínio |
| Identidade | PROV-001 para `ID_RESPONSAVEL`; estratégia Plano Técnico §9 / ADR-004 — sem mecanismo novo |

---

## 13. Segurança, privacidade, auditoria e permissões

- Identidade corporativa; responsável derivado do token/`sub` (D08) — não selecionável na UI.
- **D10 na API:** listagem e mutações escopadas ao responsável autenticado para perfil Usuário; gestor em rotas desta feature obedece mesma regra de “minhas” — rotas admin = FEAT-003.
- ActionIds: `Reserva.C`, `Reserva.I`, `Reserva.A`, `Reserva.E` (`CATALOGO_ACOES_USUARIO_GESTOR.md`); policy `perfil_portal-policy`; sistema `RESREC`.
- **PROV-002:** catálogo definido; cadastro operacional Portal/PDP **pendente** — Permit real e homologação autenticada (aceites 6–11 com SSO) **pendentes**; não declarar SSO disponível; não inventar Permit.
- Minimização (D08): persistir só identificador do responsável + dados da reserva.
- Auditoria formal de alteração alheia (D09): **FEAT-003**; log técnico recomendado pelo Plano, não requisito de produto nesta feature.

---

## 14. Contratos entre frontend e backend

Nível de produto (paths exatos no OpenAPI / Implementation Plan):

| Capacidade | Consumidor | Notas |
| --- | --- | --- |
| Criar reserva | Web usuário | Recurso ativo; período; finalidade; responsável implícito |
| Listar minhas reservas | Web usuário | D10 — filtro server-side |
| Obter reserva própria | Web usuário | Recusa se alheia |
| Alterar reserva própria não iniciada | Web usuário | Revalida conflito |
| Cancelar reserva própria não iniciada | Web usuário | Confirmação na UI |
| Consultar recursos (catálogo) | Web | FEAT-001 — `Recurso.C` |

- Erros de **conflito** (RN-10) distinguíveis de **autorização** (RN-02/D10) e validação (RN-08/09).
- Contrato canônico no `-api` (ADR-002); Web consome artefato publicado.

---

## 15. Testes iniciais

Conforme Quality Plan §13–14, §15 INC-002:

| Camada | Obrigatório (FEAT-002) | Observação |
| --- | --- | --- |
| Domínio | RN-08–12, RN-14; intervalo ADR-003; consecutivas; passado; conflito | QRSK-005, QRSK-015; relógio injetável (QRSK-006) |
| Persistência | Overlap / transação | QRSK-005 — integração quando banco de teste |
| API HTTP | D10; conflito vs 403; anônimo; inativo | QRSK-003, QRSK-007 |
| Web | Jornadas 9.1–9.3; confirmação cancelamento; estados UI | QHML-006..011, 015, 017 |
| E2E autenticado | Núcleo 9.1–9.3 | **Bloqueado** sem PROV-001/002 |
| Corrida R-RACE | Duas criações simultâneas | **Recomendado** |
| Fuso TP-RVW-004 | Documentar hipótese nos testes de “já iniciou” | Não fechar produto |

Comandos: `mvn test` (api); `npm run build` / `npm run test` (web) — Quality Plan.

---

## 16. Dependências

| Dependência | Estado | Efeito em FEAT-002 |
| --- | --- | --- |
| FEAT-001 | **Concluída** (2026-08-24) | Catálogo, recursos, RN-06 na elegibilidade — **gate atendido** |
| FND-003 Schema V001 | Concluído | `RC_RESERVA` disponível |
| ADR-003 | Aceita | Semântica intervalo |
| PROV-001 | Quase completo | Identidade do responsável |
| PROV-002 | Catálogo definido; Portal pendente | Permit real pendente |
| FEAT-003 | Posterior | D09, listar todas, aceite 13 |

---

## 17. Restrições

- Não implementar código Web/API nesta execução P-12.
- Não commit, push ou branch.
- Não redefinir produto; não decidir PRD-RVW-003/007 nem TP-RVW-004 (fuso).
- Não criar mecanismo de identidade/autorização novo.
- Não fragmentar spec por camada; recortes backend, frontend e validação integrada na mesma FEAT-002.
- Não definir task única multi-repositório.
- Templates Smart; monólito modular API.
- Rollback funcional: cancelar (RN-14), não delete físico.

---

## 18. Dúvidas

Pendências **não decididas** (isolar no Implementation Plan):

1. **TP-RVW-004** — Fuso institucional e relógio de “já iniciou”; ADR-003 registra hipótese técnica apenas.
2. **PRD-RVW-007** — Categoria inativa impacta nova reserva? Só referência se recurso/categoria bloquear elegibilidade.
3. **PRD-RVW-003** — Indireto na seleção de recurso; catálogo FEAT-001 não fechou lista do usuário.
4. Constraint DB de overlap vs. só lógica transacional — detalhe Implementation Plan (R-RACE).
5. Cadastro Portal/PDP (PROV-002) e redirect RESREC (PROV-001).
6. Comportamento UX de reserva Ativa já iniciada para usuário (consulta sim; mutação não) — lacuna PRD 19.1.

---

## 19. Necessidade de IMPLEMENTATION_PLAN

**Sim — obrigatório** (Development Plan §7 e §17).

Motivos explícitos do plano:

- Conflito **RN-10** e mitigação **R-RACE** (transação/constraint).
- Semântica de intervalo **ADR-003** (`[início, fim)`, adjacência, canceladas).
- Relógio/fuso **TP-RVW-004** — documentar hipótese sem inventar decisão.
- Detalhe OpenAPI, códigos de erro, enforcement D10, integração com `RC_RESERVA` V001.

Próximo artefato canônico: `docs/features/FEAT-002/IMPLEMENTATION_PLAN.md`.  
Task Breakdown (`TASK-*`) somente após prepare / Implementation Plan.

**Não avançar para Task Breakdown ou implementação sem Implementation Plan.**

---

## 20. Notas técnicas (trilha média)

- Schema V001: `CK_RC_RESERVA_PERIODO` cobre RN-08 parcialmente; RN-09 e conflito na camada de domínio/serviço.
- Comentários SQL referenciam ADR-003 em `DH_INICIO`/`DH_FIM`; testes devem incluir fronteira `fim == início` da reserva seguinte.
- `ID_RESPONSAVEL VARCHAR2(128)` — alinhar ao claim usado no encaixe INC-000 (`sub` ou identificador Portal documentado).
- Gestor possui `Reserva.*` no catálogo, mas listagem “todas” e D09 exigem endpoints/regras FEAT-003 — evitar vazamento em “minhas” vs admin no Implementation Plan.
- FEAT-001 TASK-FEAT-001-010 fechou com ressalva PROV (sem E2E SSO); FEAT-002 herda mesma ressalva para aceite autenticado.
- Aceite 14 (inativo): validação na criação FEAT-002 complementa catálogo FEAT-001.

---

## Conclusão P-12

**pronta para IMPLEMENTATION_PLAN**

### Ressalvas (não bloqueiam o Implementation Plan)

- **TP-RVW-004** (fuso / “já iniciou”) permanece **não decidido**; Implementation Plan documenta hipótese ADR-003 e relógio injetável nos testes.
- **PRD-RVW-003/007** não inventados; impacto em elegibilidade de recurso isolado.
- Homologação autenticada / Permit real pendente de cadastro Portal (PROV-002).
- D09, aceite 13 e listagem administrativa explicitamente **FEAT-003**.
- R-RACE: estratégia transação vs constraint SQL a fechar no Implementation Plan.

### Self-review P-12

| Pergunta | Resultado |
| --- | --- |
| Saída em `docs/features/FEAT-002/FEATURE_SPEC.md`? | Sim |
| Feature vinculada ao plano? | Sim — INC-002 / Reserva |
| Escopo pequeno e claro? | Sim — usuário, não gestor admin |
| Evita fragmentação BE/FE? | Sim — feature vertical com recortes |
| Repositórios identificados? | pda, api, web |
| Critérios testáveis? | Sim — §11 (aceites 6–11, 15) |
| Regra inventada? | Não — D09/TP-RVW-004/RVW isolados |
| Impactos sensíveis? | Sim — §13 D10, conflito, identidade |
| Conclusão ↔ pendências? | Sim — IMPLEMENTATION_PLAN obrigatório antes de tasks |

### Próxima etapa indicada

`$pda-implementation-plan` / criar `docs/features/FEAT-002/IMPLEMENTATION_PLAN.md`, depois `$pda-prepare feature_id=FEAT-002` — **sem** implementar Web/API nesta Spec.
