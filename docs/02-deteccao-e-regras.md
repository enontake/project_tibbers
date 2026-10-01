# 02 — Detecção e regras

[◀ Metodologia](01-metodologia.md)

O núcleo do estudo: cinco regras customizadas (`110001`–`110005`), cada uma ancorada no
"pai" correto do ruleset e mapeada ao MITRE ATT&CK. Os artefatos estão em
[`configs/wazuh/local_rules.xml`](../configs/wazuh/local_rules.xml).

## Princípio de ancoragem

Regras do Windows que casam por `<field>` **precisam** do `<if_sid>` (ou `<if_group>`) da
built-in que decodifica o evento. Sem isso, o alerta cai em uma regra genérica de nível < 3
e **nunca aparece**. Ancorar no pai certo é o ponto que mais separa uma regra que "parece
certa" de uma que de fato dispara.

Para os eventos deste estudo, os pais são:

| Evento | Pai | Observação |
|---|---|---|
| 4625 (falha de logon) repetido | `60204` | regra de frequência built-in (brute force) |
| 4769 (TGS Kerberos) | `60103` | cadeia do canal Security |
| 7045 (novo serviço) | `61138` | canal System |
| Sysmon EID 3 (conexão de rede) | `92101` | grupo `sysmon_event3` |
| 7040 (serviço alterado) | `61104` | estado no campo `param3` |

## As cinco regras

| Regra | Nível | Gatilho | Ancoragem | MITRE | Caso |
|---|:---:|---|---|---|:---:|
| 110001 | 12 | Brute force de logon (4625 repetido) | `if_sid 60204` | T1110 | [01](../casos/README.md#caso-01) |
| 110002 | 12 | 4769 com RC4 (`ticketEncryptionType 0x17`) | `if_sid 60103` + campo | T1558.003 | [02](../casos/README.md#caso-02) |
| 110003 | 10 | Novo serviço instalado (7045) | `if_sid 61138` | T1543.003 | [03](../casos/README.md#caso-03) |
| 110004 | 8 | Conexão de saída para o host de ataque (EID 3 → 10.20.0.50) | `if_sid 92101` + `destinationIp` | T1041/T1048 | [04](../casos/README.md#caso-04) |
| 110005 | 12 | Serviço alterado para *disabled* (7040) | `if_sid 61104` + `param3` | T1562.001 | [05](../casos/README.md#caso-05) |

## A armadilha do `srcip` no Windows

Eventos do **canal de eventos do Windows não populam o campo `srcip`** — o IP de origem vem
em `win.eventdata.ipAddress`. Duas consequências diretas:

- a correlação de brute force deve usar `same_field` sobre `win.eventdata.ipAddress`, não
  `same_source_ip`;
- a resposta `firewall-drop` (que extrai `srcip`) **não se aplica** a alertas do Windows — a
  resposta correta ali é **lockout de conta via GPO**. O `firewall-drop` fica reservado a
  alertas **com** `srcip` (tráfego Linux/rede), como no cenário de exfiltração.

## Coleta mínima

O endpoint precisa encaminhar os canais `Security`, `System` e
`Microsoft-Windows-Sysmon/Operational`, com o Sysmon capturando ao menos os EIDs 1
(processo), 3 (rede) e 10 (acesso a processo). É o suficiente para as cinco técnicas.

[◀ Metodologia](01-metodologia.md) · [Resultados ▶](03-resultados-e-licoes.md)
