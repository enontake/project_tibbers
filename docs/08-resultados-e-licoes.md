# 08 — Resultados e lições

[◀ Cobertura MITRE](07-cobertura-mitre.md)

Síntese do que o estudo mostrou — as lições que valem **além** das técnicas individuais.

## Lições sistêmicas de detecção

1. **Ancoragem é tudo.** A maioria das regras que "não disparam" não está errada no campo —
   está sem o `if_sid`/`if_group` do pai, e por isso perde para uma regra genérica de nível
   baixo. Ancorar sempre na built-in que decodifica o evento.
2. **Conheça os campos do seu canal.** O canal de eventos do Windows não popula `srcip`
   (usar `win.eventdata.ipAddress`). Esse detalhe muda a **correlação** e a **resposta**
   automatizada possível.
3. **Correlação tem sintaxe própria.** Regras de frequência exigem `if_matched_sid`/
   `if_matched_group`; trocar por `if_sid` quebra o carregamento do ruleset.
4. **Prefira casamento positivo por localização** a negações/lookaheads para flagrar binários
   em caminhos anômalos — e aproveite built-ins *path-aware* quando já cobrem o caso.
5. **Nível importa.** Uma regra correta abaixo do limiar de alerta é, na prática, invisível.
6. **Valide antes de confiar.** `wazuh-analysisd -t` + `wazuh-logtest` provam o casamento com
   evento real; sem isso, "cobertura" é só teoria.

## Lições de telemetria

- "Sysmon instalado" ≠ "Sysmon emitindo o que você precisa": a cobertura depende da config.
- Há eventos que simplesmente não vêm em certos builds (ex.: EID 22/DnsQuery) — lacuna real
  que exige fonte alternativa, não mais uma regra.
- A **saúde do pipeline** (agente online, enrollment, logs chegando, sem flood) é
  pré-requisito; sem ela, toda detecção é fantasia.

## Lições de resposta

- `firewall-drop` só serve quando há `srcip` (Linux/rede); em alertas Windows sem IP, a
  contenção é no plano de identidade (lockout/GPO).
- Fechar o ciclo **detecção → remediação** a partir do próprio SIEM (patch via Active Response)
  é viável, mas tem nuances operacionais (logs dedicados, credenciais de API, empacotamento).
- Às vezes a melhor "resposta" é **prevenção**: o hardening anti-LSASS converte uma detecção
  em uma contenção.

## Limitações do estudo

- Ambiente de **laboratório** (host único, teto de memória) — correlação em larga escala e
  alta disponibilidade ficam como evolução.
- Algumas técnicas permanecem como **regra pronta** (sem gatilho/ferramenta) ou **lacuna**
  (telemetria ausente) — registradas com honestidade, não mascaradas.
- Enriquecimento externo (threat intel/sandbox) tratado apenas no roadmap.

## O que generaliza

A mensagem central: um SOC eficaz open source depende menos da ferramenta e mais da
**engenharia** — ancorar regras corretamente, conhecer a telemetria, calibrar níveis,
escolher a resposta certa e ser honesto sobre o que está (e o que não está) coberto.

[◀ Cobertura MITRE](07-cobertura-mitre.md) · [Referências ▶](09-referencias.md)
