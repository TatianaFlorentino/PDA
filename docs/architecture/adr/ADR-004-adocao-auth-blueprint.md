# ADR-004 — Adoção do auth-blueprint (OIDC + JWT + PDP) neste sistema

| Campo | Conteúdo |
| --- | --- |
| Status | **Aceita** (confirmação humana em 2026-08-21) |
| Data | 2026-08-21 |
| Decisores | Incremento 0 / TASK-INC0-001; confirmação humana |
| Fontes | PRD D04, RN-01, RN-02; TECHNICAL_PLAN.md §9; TECHNICAL_PLAN_REVIEW.md TP-RVW-001; docs/inputs/auth-blueprint.md; DEVELOPMENT_PLAN FND-004 / PROV-001 / PROV-002 |

## Contexto

O produto exige identidade **corporativa central** (Portal), sem cadastro nem login local. O `auth-blueprint.md` descreve o padrão institucional (OIDC Authorization Code + PKCE no frontend, Bearer JWT na API, PDP ABAC/XACML).

## Decisão

1. **Classificação confirmada (PRD):** corporativa central; fonte de identidade = Portal/serviço corporativo; autorização = mecanismos corporativos; API é a barreira real (RN-02); login local **proibido**.
2. **Mecanismo detalhado — adotado neste sistema:** encaixe do `auth-blueprint.md` — OIDC + PKCE no Web, JWT validado na API, PDP por operação — conforme provisionamento institucional.
3. **Provisionamento (2026-08-21):** ver [PROVISIONING.md](../PROVISIONING.md) e [CATALOGO_ACOES_USUARIO_GESTOR.md](../CATALOGO_ACOES_USUARIO_GESTOR.md). PROV-001: client + `RESREC` + hosts Smart. PROV-002: catálogo Usuário/Gestor → actionIds no padrão Smart (`perfil_portal-policy`, `Dominio.C/I/A/E`). Cadastro operacional no Portal e redirect URIs oficiais ainda pendentes para Permit real.
4. Capacidade dos templates Smart permanece só template até os valores de ambiente do **produto** serem aplicados nas tasks `TASK-INC0-005` / `006`.
5. Encaixe de código em `TASK-INC0-005` / `006` usa o catálogo PROV-002. Permit real em runtime exige cadastro Portal. Login local continua proibido.

## Consequências

- Status desta ADR: **Aceita**.
- Catálogo Usuário×Gestor: definido para cadastro (padrão Smart + PRD).
- Implementação Web/API deve usar a sigla/client do produto e actionIds `Categoria|Recurso|Reserva.*`, não `Cliente.*` / `SMART-PNG` do template.

## ADR-005

Não aberta: identificador do responsável (`sub` vs outro) aguarda decisão humana. Até lá, persistir apenas o mínimo (D08).

## Não decide

- Segredos (`client_secret`, senhas) — não versionar.
- Cadastro operacional no Portal (códigos numéricos de perfil) — fora do repo.
- Retenção LGPD.
- Redirect URIs oficiais do host RESREC no SSO.
