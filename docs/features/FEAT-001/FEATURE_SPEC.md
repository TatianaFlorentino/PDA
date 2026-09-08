# FEATURE_SPEC — FEAT-001

| Campo | Conteúdo |
| --- | --- |
| ID | FEAT-001 |
| Nome | Cadastro e consulta de categorias e recursos |
| Produto | Reserva Corporativa (nome provisório) |
| Incremento | INC-001 |
| Status deste artefato | Gerado via P-12 (`pda-create-feature-spec`) |
| Data | 2026-08-24 |
| Fontes | `DEVELOPMENT_PLAN.md`; `PRD.md` v0.2.0; `PRD_REVIEW.md`; `TECHNICAL_PLAN.md`; `QUALITY_PLAN.md`; `PROJECT_STATE.md`; `PROVISIONING.md`; `CATALOGO_ACOES_USUARIO_GESTOR.md` |
| Conclusão P-12 | **pronta para IMPLEMENTATION_PLAN** |

---

## 1. ID e nome

**FEAT-001 — Cadastro e consulta de categorias e recursos**

Capacidade vertical de catálogo: o gestor administra categorias e recursos; o usuário consulta recursos que alimentarão a reserva em FEAT-002.

---

## 2. Trilha

**Catálogo** (Development Plan §7).

Trilha de complexidade **média** no sentido do plano: Feature Spec obrigatória; IMPLEMENTATION_PLAN **recomendado** (RN-05/07 exclusão vs inativação).

---

## 3. Repositórios afetados

| Papel | Repositório | Recorte nesta feature |
| --- | --- | --- |
| PDA | `recursos-corporativos-tati-pda` | Spec, Implementation Plan, Task Breakdown canônicos em `docs/features/FEAT-001/` |
| API | `recursos-corporativos-tati-api` | CRUD/consulta de categoria e recurso; regras RN-03–07; OpenAPI das operações de catálogo; autorização no servidor (RN-02) |
| Web | `recursos-corporativos-tati-web` | Telas gestoras de categorias/recursos; consulta de recursos pelo usuário; estados de UI |

Não misturar alteração Web + API na mesma `TASK-*`. Validação integrada exercita o fluxo ponta a ponta sem unificar patch entre repositórios.

---

## 4. Objetivo

Permitir que:

1. o **gestor** cadastre, altere, ative/inative e exclua (quando RN permitir) **categorias** e **recursos**;
2. o **usuário** (e o gestor) **consulte recursos** para uso posterior na reserva;
3. o sistema garanta vínculo categoria↔recurso (RN-03), unicidade de nome de categoria (RN-04), exclusão bloqueada / inativação (RN-05, RN-07) e que **recurso inativo não possa ser escolhido para nova reserva** (RN-06 / aceite 14) — a criação da reserva em si permanece em FEAT-002, mas o catálogo e a API devem impedir o uso do inativo para nova reserva quando o fluxo existir.

---

## 5. Contexto

- A primeira versão do PRD cobre categorias, recursos e reservas; esta feature isola o **catálogo**, independente da reserva.
- INC-000 (FND-001–005 / TASK-INC0-001–008) está **concluído** com P-16 (`PROJECT_STATE.md`): ADRs, OpenAPI mínimo, schema Oracle inicial (`RC_CATEGORIA`, `RC_RECURSO`, `RC_RESERVA`), catálogo documental de ações, encaixe de identidade Web/API e smokes.
- OpenAPI vigente (`openapi.yaml` v0.1.0) ainda tem `paths: {}`; operações de categoria/recurso entram nesta feature (ADR-002).
- Schema V001 já modela categoria e recurso; FEAT-001 implementa o domínio HTTP/UI sobre essa base (sem reinventar o modelo lógico).
- PROV-001 quase completo; PROV-002 com **catálogo definido** e **cadastro Portal pendente** — homologação autenticada e Permit real ficam pendentes; o domínio pode ser especificado e parcialmente implementado de forma reversível.

---

## 6. Escopo e fora de escopo

### Em escopo

**Backend / contrato (`-api`)**

- Operações de categoria: listar/pesquisar, obter, criar, alterar, ativar/inativar, excluir se RN-05 permitir.
- Operações de recurso: listar/filtrar (nome, categoria, situação), obter, criar, alterar, ativar/inativar, excluir se RN-07 permitir.
- Validações de domínio RN-03 a RN-07 no servidor.
- Impedir uso de recurso inativo para **nova reserva** na API (aceite 14 / RN-06), mesmo que criar reserva seja FEAT-002 — pelo menos: recurso inativo não é selecionável / criação futura recusa; nesta feature, cadastro e consulta refletem situação ativa/inativa e a API de catálogo não promove inativo como elegível a nova reserva.
- OpenAPI canônico no `-api` descrevendo as operações entregues.
- Recusa de CRUD administrativo para ator Usuário (RN-02), quando enforcement autenticado estiver disponível; até lá, desenho e testes unitários/domínio sem declarar SSO homologado.

**Frontend (`-web`)**

- Área gestora: gerenciamento de categorias e de recursos (jornada 9.4 e capacidades 7.2–7.4 / §7 área administrativa).
- Área do usuário: consulta de recursos (aceite 5).
- Estados de UI: carregamento, vazio, erro, sucesso; confirmação antes de exclusão quando houver exclusão na UI.
- Área gestora ocultável para Usuário (UX); não substitui autorização na API.

**Validação integrada**

- Gestor cadastra categoria e recurso vinculado; usuário consulta recursos; Usuário não conclui CRUD na API (quando PROV permitir). Sem patch multi-repo na mesma task.

### Fora de escopo

- Criar / alterar / cancelar reserva; conflito RN-10; D09/D10 (FEAT-002 / FEAT-003).
- Itens da seção 8 do PRD (recorrência, e-mail, calendário, mobile, fila, aprovação, etc.).
- Administração de permissões na aplicação; login/senha local.
- Decidir PRD-RVW-003 (visibilidade de inativos na lista do usuário) ou PRD-RVW-007 (efeito de categoria inativa).
- Unicidade de nome de recurso (lacuna PRD 19.2 — não inventar).
- Seed obrigatório de categorias/recursos; carga de produção.
- Paths HTTP finais e códigos de status exatos além do nível de produto do Plano Técnico — fechar no OpenAPI / Implementation Plan sem inventar coleção nesta Spec.

---

## 7. Usuários / perfis

| Perfil | Nesta feature |
| --- | --- |
| **Usuário** | Consulta recursos (`Recurso.C`). Não executa CRUD de categoria/recurso. |
| **Gestor** | CRUD categoria (`Categoria.C/I/A/E`) e recurso (`Recurso.C/I/A/E`); consulta recursos. |
| Anônimo | Sem uso de funcionalidades protegidas (RN-01) — aceite via FND-004 / PROV. |

Referência de actionIds: `docs/architecture/CATALOGO_ACOES_USUARIO_GESTOR.md` (PROV-002). `Categoria.C` no perfil Usuário é opcional para filtro por categoria — **não obrigatório pelo PRD**; não decidir aqui.

---

## 8. Regras de negócio

Somente regras já presentes no PRD / plano, aplicáveis ao catálogo:

| ID | Regra aplicável a FEAT-001 |
| --- | --- |
| RN-01 | Utilização autenticada; sem cadastro local de usuários. |
| RN-02 | Usuário comum não executa operações administrativas (CRUD categoria/recurso) mesmo por URL/API direta. |
| RN-03 | Todo recurso vinculado a categoria existente. |
| RN-04 | Nome de categoria único (não há mais de uma com o mesmo nome). |
| RN-05 | Categoria com recursos vinculados não pode ser excluída; pode ser inativada. |
| RN-06 | Recurso inativo permanece registrado e não recebe novas reservas; reservas anteriores consultáveis (consulta de reservas = FEAT-002+). |
| RN-07 | Recurso nunca usado em reserva pode ser excluído; com reservas relacionadas, não exclui — inativa quando deixar de ser utilizado. |

**Atributos (PRD §10)**

- **Categoria:** nome (único), descrição, situação ativa ou inativa.
- **Recurso:** nome, categoria obrigatória, descrição, localização, capacidade opcional, situação ativa ou inativa.

**Explícito nas funcionalidades**

- Recursos inativos podem aparecer em consultas **administrativas**, mas não podem ser usados para novas reservas.
- Persistência alinhada ao schema V001 (`RC_CATEGORIA`, `RC_RECURSO`) sem expandir PII (D08).

**Não inventar**

- PRD-RVW-003: o que o usuário comum vê quanto a recursos inativos na consulta geral — **não decidido**.
- PRD-RVW-007: efeito de categoria inativa sobre recursos / novas reservas — **não decidido**; isolar na implementação; sem hardcode silencioso (Plano Técnico §8).
- Critério de unicidade de nome de categoria quanto a maiúsculas/acentos/espaços — ambiguidade PRD 19.1; detalhar no Implementation Plan sem fechar produto.
- Unicidade de nome de recurso — lacuna; não exigir nesta Spec.

---

## 9. Fluxos

### 9.1 Gestor — administrar categoria (derivado das capacidades; jornada passo a passo não detalhada na fonte)

1. Gestor autenticado acessa gerenciamento de categorias.
2. Consulta categorias existentes (lista com estados de UI).
3. Cria nova categoria (nome, descrição, situação) **ou** seleciona existente para alterar / ativar / inativar / excluir.
4. Sistema valida (RN-04; RN-05 na exclusão).
5. Persiste e informa resultado (sucesso ou erro compreensível).

### 9.2 Gestor — administrar recurso (jornada PRD 9.4)

1. Gestor acessa o cadastro de recursos.
2. Consulta os recursos existentes (filtros: nome, categoria, situação — Plano Técnico §11).
3. Seleciona um recurso ou solicita novo cadastro.
4. Informa/altera dados (categoria obrigatória, nome, descrição, localização, capacidade opcional, situação).
5. Sistema valida (RN-03, RN-07 na exclusão).
6. Operação registrada; sistema informa o resultado.

### 9.3 Usuário — consultar recursos (aceite 5; trecho da jornada 9.1 passos 3–4)

1. Usuário autenticado acessa a consulta de recursos.
2. Sistema lista recursos conforme regras de produto **decididas** (ativos para uso futuro em reserva; comportamento de inativos na lista do usuário = **PRD-RVW-003 não decidido** — não prescrever).
3. Estados loading / vazio / erro observáveis.
4. Seleção para reserva fica para FEAT-002; nesta feature basta a consulta e a exposição da situação quando já definida pelo produto.

### 9.4 Validação integrada (macro)

Gestor cadastra categoria → cadastra recurso vinculado → usuário consulta; tentativa de CRUD por Usuário na API é recusada (quando Permit real existir).

---

## 10. Casos de borda

| Caso | Comportamento esperado |
| --- | --- |
| Nome de categoria duplicado | Recusa (RN-04). |
| Excluir categoria com recurso vinculado | Recusa; inativação permitida (RN-05). |
| Excluir categoria sem recursos | Exclusão permitida (implícito RN-05). |
| Criar recurso sem categoria / categoria inexistente | Recusa (RN-03). |
| Excluir recurso sem histórico de reserva | Permitido (RN-07). |
| Excluir recurso com reservas relacionadas | Recusa; inativar (RN-07). *Detecção de “já usado em reserva” depende da existência da entidade reserva (schema já existe; criação em FEAT-002).* |
| Inativar recurso | Permanece no cadastro; não elegível a nova reserva (RN-06). |
| Consulta administrativa de inativos | Podem aparecer (PRD §10). |
| Lista do usuário e inativos | **Não decidido** (PRD-RVW-003). |
| Categoria inativa × novo recurso / nova reserva | **Não decidido** (PRD-RVW-007); não implementar regra extra. |
| Usuário chama API de CRUD | Recusa autorização (RN-02) — homologação depende PROV-002. |
| Capacidade vazia / omitida | Aceita (campo opcional; sem regra de uso na v1). |

---

## 11. Critérios testáveis

Alinhados ao PRD §18, Development Plan e Quality Plan (QHML / QRSK):

| # | Critério | Evidência |
| --- | --- | --- |
| 1 | Gestor cadastra categoria (aceite 3 / QHML-003) | UI + API |
| 2 | Gestor cadastra recurso associado a categoria (aceite 4 / QHML-004); rejeita sem categoria | UI + API |
| 3 | Usuário consulta recursos (aceite 5 / QHML-005); loading/vazio/erro observáveis | UI |
| 4 | Nome de categoria duplicado recusado (RN-04 / QHML-016) | Domínio + API |
| 5 | Categoria com recurso não exclui; pode inativar (RN-05) | Domínio + API |
| 6 | Recurso sem reserva pode excluir; com reserva não exclui e inativa (RN-07) | Domínio + API |
| 7 | Recurso inativo não recebe nova reserva (aceite 14 / QHML-014 / RN-06) — na criação (FEAT-002) e impedindo escolha no catálogo | API (+ UI quando reserva existir) |
| 8 | Usuário comum não executa CRUD administrativo via API (aceite 12 / QHML-012 / RN-02) | API — **quando PROV-002 cadastro Portal** |
| 9 | OpenAPI no `-api` descreve operações de catálogo entregues | Contrato |
| 10 | Não há teste prescritivo que “feche” PRD-RVW-003 ou PRD-RVW-007 (QRSK-013) | Spec de teste / Quality |

---

## 12. Impactos técnicos

| Área | Impacto |
| --- | --- |
| API | Módulos `categoria` e `recurso` (Plano Técnico §10); serviços de domínio RN-03–07; controllers com `@PreAuthorize` / actionIds PROV-002 quando enforcement ativo |
| Persistência | Usa `RC_CATEGORIA` / `RC_RECURSO` (V001); índice/unicidade de nome de categoria já no schema; sem migração inventada nesta Spec |
| OpenAPI | Preencher paths de catálogo no contrato canônico do `-api` (hoje vazio) |
| Web | Rotas área gestora (categorias, recursos) e consulta de recursos; componentes kebab-case com `.spec.ts` (standards §5); Design System Smart/PrimeNG |
| Identidade | Referencia estratégia do Plano Técnico §9 e ADR-004; **não** cria mecanismo novo |
| Reserva | FEAT-002 consome recursos ativos; FEAT-001 prepara elegibilidade (inativo) |

---

## 13. Segurança, privacidade, auditoria e permissões

- Identidade corporativa central; sem login local (RN-01, D04, ADR-004).
- Autorização: API decide (RN-02); UX só oculta menus.
- ActionIds de catálogo: `Categoria.*`, `Recurso.*` conforme `CATALOGO_ACOES_USUARIO_GESTOR.md`; policy `perfil_portal-policy`; sistema `RESREC`.
- **PROV-001:** quase completo (client_id, RESREC, hosts Smart); lacuna redirect oficial de host.
- **PROV-002:** catálogo/mapeamento **definido**; cadastro operacional Portal/PDP **pendente** — Permit real e homologação autenticada (aceites 3–5, 12, 14 com SSO) **pendentes** nesta Spec; não declarar SSO disponível.
- Minimização (D08): não persistir CPF/e-mail/perfil completo no catálogo.
- Auditoria formal de quem alterou cadastro: **não determinada** pelas fontes; log técnico recomendado pelo Plano, não requisito de produto.
- Sem inventar papéis locais nem `api_key` de produto.

---

## 14. Contratos entre frontend e backend

Nível de produto (paths exatos no OpenAPI da feature / Implementation Plan):

| Capacidade | Consumidor | Notas |
| --- | --- | --- |
| Categorias: listar, obter, criar, alterar, ativar/inativar, excluir | Web gestora | Erros de unicidade e exclusão bloqueada distinguíveis |
| Recursos: listar/filtrar, obter, criar, alterar, ativar/inativar, excluir | Web gestora + consulta usuário (leitura) | Filtros nome, categoria, situação |
| Auth Bearer JWT | Web → API | Template/blueprint; provisionamento deste sistema não afirmado como completo |

- Contrato canônico no repositório `-api`; Web consome artefato publicado — sem path relativo entre repos (ADR-002).
- Erros de autorização (RN-02) distinguíveis na UI dos erros de validação de negócio.

---

## 15. Testes iniciais

Conforme Quality Plan §13–14 e Development Plan §7/13:

| Camada | Obrigatório (FEAT-001) | Observação |
| --- | --- | --- |
| Domínio/API | RN-03–07; unicidade; exclusão bloqueada; inativação; Usuário sem CRUD | QRSK-002, 007, 008 |
| Persistência | Unicidade nome categoria; exclusão com vínculo | Quando banco de teste comprovado |
| Web | Telas cadastro gestor + consulta; loading/vazio/erro; `.spec.ts` de componente novo | QRSK-011: *Cliente* não é evidência de produto |
| Contrato | OpenAPI descreve operações entregues | QCMD-016 futuro |
| E2E autenticado | Bloqueado sem PROV cadastro Portal | Não gate pré-merge |
| PRD-RVW-003/007 | Não aplicável / não prescritivo | QRSK-013 |

Comandos reais (Quality Plan): `mvn test` (api); `npm run build` / `npm run test` (web). Não inventar comandos aqui.

---

## 16. Dependências

| Dependência | Estado (PROJECT_STATE / planos) | Efeito em FEAT-001 |
| --- | --- | --- |
| FND-001 Bootstrap documental | Concluído (INC-000) | Atendida |
| FND-002 OpenAPI mínimo | Concluído | Atendida; evoluir paths nesta feature |
| FND-003 Schema Oracle | Concluído (SQL V001); aplicação em Oracle institucional = PROV-003 | Estrutura ok para desenho/local |
| FND-004 / encaixe identidade | Tasks 005/006 concluídas; Permit real pendente Portal | Domínio pode avançar; aceite autenticado pendente |
| FND-005 ADRs | Concluído | Atendida |
| PROV-001 | Quase completo | Homologação autenticada parcial |
| PROV-002 | Catálogo definido; cadastro Portal pendente | Enforcement autenticado **pendente** na Spec |
| FEAT-002 | Posterior | Consome catálogo |

Não se presume dependência não concluída: INC-000 mínimo para prepare está validado (001–008 aprovadas P-16).

---

## 17. Restrições

- Não implementar código nesta execução P-12.
- Não redefinir produto nem decidir PRD-RVW-003 / PRD-RVW-007.
- Não criar mecanismo de identidade/autorização novo — só referenciar Plano Técnico / blueprint / catálogo.
- Não fragmentar a feature em specs por camada; backend, frontend e validação integrada são recortes da mesma FEAT-001.
- Não definir uma única task que altere múltiplos repositórios.
- Templates Smart Angular / Spring Boot obrigatórios (D07).
- Sem microserviços por entidade; monólito modular API (Plano Técnico).

---

## 18. Dúvidas

Pendências **não decididas** (não fechar nesta Spec):

1. **PRD-RVW-003** — Usuário comum vê recursos inativos na consulta geral? (só identificação visual vs. só administrativos)
2. **PRD-RVW-007** — Categoria inativa impede novas reservas / novo cadastro de recurso vinculado?
3. Unicidade de nome de categoria: case/acentos/espaços?
4. Unicidade de nome de recurso (lacuna PRD)?
5. Incluir `Categoria.C` no pacote Usuário no Portal para filtro? (catálogo marca como opcional)
6. Cadastro operacional Portal/PDP (PROV-002) e redirect oficial RESREC (PROV-001) — fora do controle desta Spec

---

## 19. Necessidade de IMPLEMENTATION_PLAN

**Sim — recomendado** pelo Development Plan §7 e §17 (RN-05/07 exclusão vs inativação; detalhe de API/UI/OpenAPI; isolamento PRD-RVW-003/007).

Próximo artefato canônico: `docs/features/FEAT-001/IMPLEMENTATION_PLAN.md`.  
Task Breakdown (`TASK-*`) somente após prepare / Implementation Plan, no diretório `docs/features/FEAT-001/`.

---

## 20. Notas técnicas (trilha média)

- Schema V001 já cobre entidades; Implementation Plan deve mapear DTOs/serviços sem alterar regras de produto.
- RN-07 “nunca usado em reserva”: com tabela `RC_RESERVA` existente, a checagem pode ser “existe alguma reserva para o recurso”; criação de reservas continua FEAT-002.
- Até PROV-002 no Portal: testes de domínio e, se necessário, stubs — **não** usar `TestSecurityConfig` `permitAll` como prova de RN-02 (QRSK-012).
- Context-path do template (`/smart/clientes/v1.0`) permanece do exemplo até bootstrap de aplicação renomear; não inventar path de produto nesta Spec.
- Aceite macro do Development Plan: gestor cadastra categoria e recurso; usuário consulta; inativo não recebe nova reserva (reserva em FEAT-002; até lá API/cadastro impede uso para nova reserva).

---

## Conclusão P-12

**pronta para IMPLEMENTATION_PLAN**

### Ressalvas (não bloqueiam o Implementation Plan)

- PRD-RVW-003 e PRD-RVW-007 permanecem **não decididos**; Implementation Plan deve isolá-los sem escolher comportamento.
- Homologação autenticada / Permit real (aceites 12 e parte dos 3–5, 14 com SSO) pendente de cadastro Portal (PROV-002) e redirect oficial (PROV-001).
- Unicidade ortográfica de nome de categoria e unicidade de nome de recurso: lacunas de produto — não inventar na implementação silenciosa.

### Self-review P-12

| Pergunta | Resultado |
| --- | --- |
| Saída em `docs/features/FEAT-001/FEATURE_SPEC.md`? | Sim |
| Feature vinculada ao plano? | Sim — INC-001 / Catálogo |
| Escopo pequeno e claro? | Sim — só catálogo |
| Evita fragmentação BE/FE? | Sim — uma feature vertical com recortes |
| Repositórios identificados? | pda, api, web |
| Critérios testáveis? | Sim — §11 |
| Regra inventada? | Não — PRD-RVW isolados |
| Impactos sensíveis? | Sim — §13 |
| Conclusão ↔ pendências? | Sim — avança para IMPLEMENTATION_PLAN com ressalvas explícitas |

### Próxima etapa indicada

`$pda-implementation-plan` / criar `docs/features/FEAT-001/IMPLEMENTATION_PLAN.md` (P correspondente), depois `$pda-prepare feature_id=FEAT-001` / Task Breakdown canônico — **sem** implementar Web/API nesta Spec.
