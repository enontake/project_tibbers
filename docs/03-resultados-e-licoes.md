# 03 — Resultados e lições

[◀ Detecção](02-deteccao-e-regras.md)

Síntese do que o estudo mostrou — o que generaliza para além das cinco técnicas.

## Achados por técnica

| Caso | Técnica | Sinal que funciona | Resposta |
|---|---|---|---|
| 01 | Brute force (T1110) | Repetição de 4625 do mesmo IP (`win.eventdata.ipAddress`) numa janela curta | Lockout de conta via GPO |
| 02 | Kerberoasting (T1558.003) | 4769 com `ticketEncryptionType 0x17` (RC4) para um SPN | Forçar AES; senha gerenciada (gMSA) |
| 03 | Persistência por serviço (T1543.003) | 7045 (novo serviço) com caminho/binário incomum | Remover o serviço; caçar o vetor |
| 04 | Exfiltração (T1041/T1048) | Sysmon EID 3 com `destinationIp` externo conhecido | Bloquear o IP (firewall-drop) |
| 05 | Anti-forense (T1562.001) | 7040 com `param3 = disabled` em serviço de defesa | Investigar; restaurar o serviço |

## Lições que generalizam

1. **Ancoragem é tudo.** A maioria das regras que "não disparam" não está errada no campo —
   está sem o `if_sid`/`if_group` do pai, e por isso perde para uma regra genérica de nível
   baixo. Sempre ancorar na built-in que decodifica o evento.
2. **Conheça os campos do seu canal.** O canal de eventos do Windows não popula `srcip`; usar
   `win.eventdata.ipAddress`. Esse detalhe muda tanto a **correlação** quanto a **resposta**
   automatizada possível.
3. **Resposta segue a telemetria.** `firewall-drop` só serve quando há `srcip`; em alertas
   Windows sem IP, a contenção é no plano de identidade (lockout/GPO).
4. **Nível importa.** Uma regra correta com nível abaixo do limiar de alerta é, na prática,
   invisível. Calibrar o nível ao risco da técnica.
5. **Valide antes de confiar.** `wazuh-analysisd -t` + `wazuh-logtest` provam o casamento com
   evento real; sem isso, a "cobertura" é só teórica.

## Limitações do estudo

- Recorte de **cinco técnicas** — não é cobertura completa do ATT&CK.
- Ambiente **mínimo** (um endpoint/DC) — correlação entre múltiplos hosts fica fora do
  escopo.
- Enriquecimento externo (threat intel/VirusTotal) não foi avaliado.

## Direções futuras

- Ampliar para mais táticas (descoberta, movimento lateral, impacto).
- Medir falsos positivos em volume realista.
- Automatizar a validação das regras (teste contínuo do ruleset).

[◀ Detecção](02-deteccao-e-regras.md) · [Referências ▶](04-referencias.md)
