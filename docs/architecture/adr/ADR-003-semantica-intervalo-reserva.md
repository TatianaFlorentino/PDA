# ADR-003 — Semântica de intervalo da reserva (RN-10)

| Campo | Conteúdo |
| --- | --- |
| Status | Aceita (semântica de intervalo); fuso permanece hipótese documentada |
| Data | 2026-08-21 |
| Decisores | Incremento 0 / TASK-INC0-001 |
| Fontes | PRD RN-10, RN-08, RN-09, RN-11/11a/12/13; TECHNICAL_PLAN.md §8; TECHNICAL_PLAN_REVIEW.md TP-RVW-004 |

## Contexto

RN-10 exige que um recurso não tenha duas reservas **ativas** com períodos sobrepostos, e permite reservas **consecutivas** (ex.: 09:00–10:00 e 10:00–11:00). “Já iniciou” (RN-11/12 e D09) depende de relógio/fuso não definidos nas fontes de produto.

## Decisão

1. **Modelo de intervalo:** tratar o período da reserva como intervalo **semiaberto** `[início, fim)` na linha do tempo do servidor da API.
2. **Sobreposição:** duas reservas ativas do mesmo recurso **conflitam** se e somente se os intervalos `[A_início, A_fim)` e `[B_início, B_fim)` têm interseção não vazia.
3. **Adjacência:** se `A_fim == B_início`, **não** há conflito (consecutivas permitidas).
4. **Canceladas:** reservas com situação Cancelada **não** entram na verificação de conflito.
5. **Relógio de “já iniciou” (TP-RVW-004):** usar o **relógio do servidor da API** no fuso configurado do ambiente (hipótese técnica do Plano Técnico, ainda sem fuso institucional nomeado nas fontes). Documentar o fuso efetivo na implementação de `FEAT-002`/`FEAT-003`. Não inventar horário comercial. Mudança de fuso oficial exige decisão humana e emenda a esta ADR ou ADR dedicada.

## Consequências

- Testes de domínio em FEAT-002 devem incluir o caso fronteira fim=início.
- Implementação atômica (transação/constraint) permanece obrigatória no servidor (R-RACE); detalhe fica na feature.
- Aceite de “já iniciou” deve declarar o fuso/relógio usado na evidência de teste.

## Não decide

- Fuso oficial da organização (ainda não determinado).
- Retenção LGPD do histórico.
- PRD-RVW-003 / PRD-RVW-007.
