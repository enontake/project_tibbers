# 02 — Metodologia

[◀ Ambiente](01-ambiente-de-referencia.md)

Cada afirmação deste estudo — "esta técnica é detectada por esta regra" — segue o mesmo rito,
para ser **reproduzível e auditável**, e não apenas uma alegação de cobertura.

## Rito por técnica

1. **Mapear a telemetria.** Identificar o evento que registra a técnica e o campo onde mora o
   indicador (ex.: `ticketEncryptionType` em um 4769; `destinationIp` em um Sysmon EID 3).
2. **Escrever a regra.** Ancorar no "pai" correto (`if_sid`/`if_group`) e casar pelo campo.
3. **Validar o ruleset.** `wazuh-analysisd -t` garante que a regra carrega sem erro de
   sintaxe (um erro derruba o manager).
4. **Validar o casamento.** `wazuh-logtest` mostra, para um evento real, os campos
   decodificados (Fase 2) e a regra que casou (Fase 3).
5. **Gerar o evento.** Executar a técnica a partir da estação ofensiva — ou simular de forma
   benigna no endpoint — e confirmar o alerta em `alerts.json`/Dashboard.
6. **Registrar o achado.** Nível, falsos positivos observados e a resposta cabível.

## Ferramentas de diagnóstico

| Ferramenta | Para quê |
|---|---|
| `wazuh-analysisd -t` | Valida o ruleset antes de aplicar |
| `wazuh-logtest` | Prova, para um evento, qual regra casa e com quais campos |
| `<logall_json>` (temporário) | Confirma se o evento chega ao manager antes de ser descartado — **desligar depois**, enche disco |

## Critério de "detecção confiável"

Uma detecção só é considerada boa quando:

- dispara no **evento certo** com **nível adequado** (acima do limiar de alerta);
- **não** é engolida por uma regra genérica de nível baixo (problema de ancoragem);
- tem **falsos positivos conhecidos e tratáveis** (allowlist ou refinamento de campo);
- tem uma **resposta associada** viável (lockout, bloqueio de IP, investigação, patch).

## Classificação de maturidade de cada caso

Para ser honesto sobre o que está provado, cada técnica estudada recebe um status:

| Status | Significado |
|---|---|
| **Validado** | Regra implantada **e** alerta real observado (evidência) |
| **Parcial** | Detecção pronta; a execução do ataque foi contida por um controle (ex.: o próprio hardening) |
| **Regra pronta** | Regra validada no ruleset, aguardando gatilho/telemetria para evidência |
| **Lacuna** | Detecção ainda não fecha (campo ausente na telemetria ou ajuste de regra pendente) |

## Geração benigna de eventos

Nem toda técnica exige um ataque real: criar um serviço (persistência) ou mudar o estado de
um serviço (anti-forense) pode ser reproduzido com artefatos inócuos, evitando ações
destrutivas e mantendo o foco na telemetria. Ações sensíveis (ex.: dump de credenciais) são
estudadas pelo lado do **controle** que as bloqueia, não pela execução ofensiva.

[◀ Ambiente](01-ambiente-de-referencia.md) · [Telemetria ▶](03-telemetria.md)
