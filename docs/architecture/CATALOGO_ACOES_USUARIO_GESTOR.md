# Catálogo documental — operações Usuário × Gestor

| Campo | Conteúdo |
| --- | --- |
| Produto | Reserva Corporativa / sistema Portal `RESREC` |
| Status | **Catálogo para cadastro** — actionIds de grupo Portal; cadastro operacional ainda pendente |
| Data | 2026-08-27 |
| Task | TASK-INC0-004 / FND-004 / PROV-002 |
| Fontes | Confirmação operacional (`GrupoCategorias`, `GrupoRecursoAdm`, `GrupoReservas`); PRD D09, D10, RN-02 |

## Disclaimer

1. ActionIds do produto são **grupos** Portal (não mais `Dominio.C/I/A/E`).
2. O template Smart ainda usa `Cliente.C|I|A|E` no domínio de exemplo — **não** reutilizar em RESREC.
3. Identidade: corporativa central (ADR-004). Login local **proibido**.
4. A **API** é a barreira de autorização (RN-02). Ocultar menu no Web **não** basta.
5. Escopo D09/D10 (próprias vs todas): regra de negócio na API **após** `Permit` do grupo.

## Evidência no Smart (fonte de policy)

| Item | Valor | Onde |
| --- | --- | --- |
| Policy PDP | `perfil_portal-policy` | `@PreAuthorize` |
| Origem em runtime | Portal `…/actions?login=&sistema=` → `um_attr_value` | Web `AcessosUsuarioService` |
| Nomes de perfil no repo | **Não há** — vêm do Portal | Web `selecao-perfil` |

## Matriz de operações (produto)

| Operação | Usuário | Gestor | actionId |
| --- | --- | --- | --- |
| Consultar recursos | sim | sim | `GrupoRecursoAdm` |
| CRUD categoria | não | sim | `GrupoCategorias` |
| CRUD recurso | não | sim | `GrupoRecursoAdm` |
| Criar / consultar / alterar / cancelar reservas | sim (escopo próprio) | sim (escopo ampliado D09) | `GrupoReservas` |

## Catálogo de actionIds (RESREC)

| actionid | Domínio / controllers | Usuário | Gestor |
| --- | --- | --- | --- |
| `GrupoCategorias` | `CategoriaController` | não | sim |
| `GrupoRecursoAdm` | `RecursoController` | sim (consulta) | sim |
| `GrupoReservas` | `ReservaController` | sim | sim |

## Pacotes de perfil (PROV-002 — cadastro Portal)

| Perfil | actionIds a vincular no Portal / PDP |
| --- | --- |
| **Usuário** | `GrupoRecursoAdm`, `GrupoReservas` |
| **Gestor** | `GrupoCategorias`, `GrupoRecursoAdm`, `GrupoReservas` |

Policy: `perfil_portal-policy`. Sistema: `RESREC`.

## Encaixe código

```java
@PreAuthorize("hasPermission('perfil_portal-policy','{actionid:GrupoReservas}')")
```

Web: `*ngxPermissionsOnly="['GrupoReservas']"` — mesma string que `um_attr_value` do Portal.

## Dependências

| ID | Status |
| --- | --- |
| PROV-001 | Quase completo — [PROVISIONING.md](PROVISIONING.md) |
| PROV-002 | **Catálogo atualizado** (grupos); falta cadastro operacional no Portal/PDP |

## Referências

- [PROVISIONING.md](PROVISIONING.md)
- [ADR-004](adr/ADR-004-adocao-auth-blueprint.md)
