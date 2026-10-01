# 01 — Metodologia

[◀ Contexto](00-contexto-e-escopo.md)

Cada técnica é estudada com o mesmo rito, para que o resultado seja reproduzível e
auditável — e não apenas uma afirmação de que "a regra funciona".

## Rito de estudo (por técnica)

1. **Mapear a telemetria.** Identificar qual evento registra a técnica e em qual campo mora
   o indicador (ex.: `ticketEncryptionType` em um 4769; `destinationIp` em um Sysmon EID 3).
2. **Escrever a regra.** Ancorar no "pai" correto (`if_sid`/`if_group`) e casar pelo campo.
3. **Validar o ruleset.** `wazuh-analysisd -t` garante que a regra carrega sem erro.
4. **Validar o casamento.** `wazuh-logtest` mostra, para um evento real, os campos
   decodificados (Fase 2) e a regra que casou (Fase 3).
5. **Gerar o evento.** Executar a técnica a partir da estação ofensiva (ou simular de forma
   benigna no endpoint) e confirmar o alerta no `alerts.json`/Dashboard.
6. **Registrar o achado.** Nível, falsos positivos observados e a resposta cabível.

## Ferramentas de diagnóstico

| Ferramenta | Para quê |
|---|---|
| `wazuh-analysisd -t` | Valida o ruleset antes de aplicar (erro de sintaxe derruba o manager) |
| `wazuh-logtest` | Prova, para um evento, qual regra casa e com quais campos |
| `<logall_json>` (temporário) | Confirma se o evento chega ao manager antes de ser descartado — **desligar depois**, enche disco |

## Critério de "detecção confiável"

Uma detecção só é considerada boa quando:

- dispara no evento certo com **nível adequado** (acima do limiar de alerta);
- **não** é engolida por uma regra genérica de nível baixo (problema de ancoragem);
- tem **falsos positivos conhecidos e tratáveis** (allowlist ou refinamento de campo);
- tem uma **resposta associada** viável (lockout, bloqueio de IP, investigação).

## Nota sobre geração benigna de eventos

Nem toda técnica precisa de um ataque real para ser estudada: a criação de um serviço
(cenário 03) ou a mudança de estado de um serviço (cenário 05) podem ser reproduzidas com
artefatos inócuos, evitando ações destrutivas e mantendo o foco na telemetria.

[◀ Contexto](00-contexto-e-escopo.md) · [Detecção ▶](02-deteccao-e-regras.md)
