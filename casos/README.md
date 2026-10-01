# Casos estudados

[◀ README](../README.md)

Catálogo das técnicas estudadas, agrupadas por tática do MITRE ATT&CK. Para cada uma: o
evento/gatilho que a registra e o **status** honesto da detecção no ambiente de referência.
Os princípios por trás das regras estão em
[`docs/04-engenharia-de-deteccao.md`](../docs/04-engenharia-de-deteccao.md); as regras
representativas, em [`../configs/wazuh/local_rules.xml`](../configs/wazuh/local_rules.xml).

**Legenda de status:** *Validado* (evidência real) · *Parcial* (ataque contido por controle)
· *Regra pronta* (sem gatilho/telemetria ainda) · *Lacuna* (detecção a fechar).

**Panorama:** Validado **28** · Parcial **2** · Regra pronta **17** · Lacuna **3**.

---

## Fundamentos / Observabilidade

Antes de detectar ataques, a telemetria precisa estar íntegra. "Falhas que cegam o SOC."

| Técnica | Evento / sintoma | Status |
|---|---|---|
| Agente Wazuh offline | agente `Disconnected` (porta 1514 bloqueada) | Validado |
| Falha de enrollment | registro falha (1515 / authd) | Validado |
| Logs não chegam | agente ACTIVE sem fluxo de eventos (config errada) | Validado |
| Sysmon sem eventos | canal Sysmon vazio (config inválida) | Validado |
| Flood de logs | saturação de EPS (`agent buffer is full`) | Validado |

## TA0001 Acesso Inicial · TA0002 Execução

| Técnica | MITRE | Evento / gatilho | Status |
|---|---|---|---|
| Ataque web (SQLi, log poisoning, defacement) | T1190 · T1505.003 | nginx 31104/31106 + FIM 550/554 | Validado |
| Malware (EICAR) e discovery pós-execução | T1204 · T1082 | Defender → 62123; discovery 92031/92039 | Validado |
| Phishing: Office gera shell (macro) | T1204.002 | Sysmon EID 1 (winword/excel → cmd/powershell) | Regra pronta |
| PowerShell ofuscado / EncodedCommand | T1059.001 | script block (pai 91802) | Regra pronta |
| Execução via WMI | T1047 | `wmic`/`WmiPrvSE` gera processo filho | Regra pronta |

## TA0003 Persistência

| Técnica | MITRE | Evento / gatilho | Status |
|---|---|---|---|
| Novo serviço Windows | T1543.003 | System 7045 | Validado |
| Chaves Run no Registro | T1547.001 | Sysmon EID 13 | Lacuna |
| Tarefa agendada | T1053.005 | Security 4698 | Validado |
| Assinatura de evento WMI | T1546.003 | Sysmon/WMI | Regra pronta |
| Criação de conta + elevação a Admins | T1136.001 · T1098 | 4720 (pai 60109) + 4732 | Validado |
| Pasta Startup (atalho LNK) | T1547.009 | Sysmon EID 11 | Lacuna |

## TA0004 Escalonamento de Privilégio

| Técnica | MITRE | Evento / gatilho | Status |
|---|---|---|---|
| Bypass de UAC (fodhelper/eventvwr) | T1548.002 | Sysmon EID 1 + Registro | Regra pronta |
| Manipulação de token / SeDebugPrivilege | T1134 | Security/Sysmon | Regra pronta |
| Modificação não autorizada de GPO | T1484.001 | Security 5136 (DS Changes) | Validado |

## TA0005 Evasão de Defesa

| Técnica | MITRE | Evento / gatilho | Status |
|---|---|---|---|
| Anti-forense: serviço → disabled | T1562.001 | System 7040 (`param3=disabled`) | Validado |
| Execução por mshta | T1218.005 | Sysmon EID 1 | Regra pronta |
| Abuso de rundll32 | T1218.011 | Sysmon EID 1 | Regra pronta |
| Regsvr32 / Squiblydoo | T1218.010 | Sysmon EID 1 (`/i:scrobj`) | Regra pronta |
| Limpeza do log de eventos | T1070.001 | Security 1102 | Regra pronta |
| Tamper no Defender / exclusões | T1562.001 | Sysmon EID 13 (Registro) | Regra pronta |
| Timestomping | T1070.006 | Sysmon EID 2 | Validado |
| Masquerading (binário em caminho anômalo) | T1036.005 | built-ins path-aware 61618/61625 | Validado |
| Alternate Data Streams | T1564.004 | Sysmon EID 15 | Validado |

## TA0006 Acesso a Credenciais

| Técnica | MITRE | Evento / gatilho | Status |
|---|---|---|---|
| Brute force de logon | T1110 | 4625 repetido (pai 60204) | Validado |
| Kerberoasting | T1558.003 | 4769 com RC4 (`0x17`) | Validado |
| Credential dumping (LSASS) | T1003.001 | Sysmon EID 10 → lsass | Parcial |
| DCSync | T1003.006 | 4662 + GUID de replicação | Validado |
| Dump do NTDS.dit | T1003.003 | `vssadmin`/shadow + System | Validado |
| LSASS via comsvcs MiniDump | T1003.001 | Sysmon EID 10 / EID 1 | Parcial |
| Password spraying | T1110.003 | 4625 mesmo IP, N contas (correlação) | Validado |
| AS-REP Roasting | T1558.004 | 4768 sem pré-auth | Validado |

## TA0007 Descoberta

| Técnica | MITRE | Evento / gatilho | Status |
|---|---|---|---|
| Descoberta de contas e domínio | T1087 · T1033 | rajada de `net`/`whoami`/`nltest` | Validado |
| Reconhecimento LDAP (BloodHound) | T1069.002 · T1482 | 4662 (LDAP) / Sysmon na origem | Regra pronta |
| Descoberta de compartilhamentos | T1135 | enum SMB | Regra pronta |

## TA0008 Movimento Lateral

| Técnica | MITRE | Evento / gatilho | Status |
|---|---|---|---|
| Pass-the-Hash | T1550.002 | 4624 tipo 3 NTLM | Validado |
| PsExec / serviço remoto | T1021.002 · T1569.002 | built-in PsExec (serviço remoto) | Validado |
| RDP | T1021.001 | 4624 tipo 10 | Regra pronta |
| WinRM / PowerShell Remoting | T1021.006 | `wsmprovhost`/`winrshost` | Validado |

## TA0009 Coleta · TA0010 Exfiltração · TA0011 C2

| Técnica | MITRE | Evento / gatilho | Status |
|---|---|---|---|
| Coleta e staging (compactação) | T1560.001 | arquivo grande compactado | Regra pronta |
| Exfiltração por canal de rede | T1041 · T1048 | Sysmon EID 3 → host externo | Validado |
| Exfiltração/C2 por túnel DNS | T1048.003 · T1071.004 | Sysmon EID 22 (DnsQuery) | Lacuna |
| Beaconing de C2 (periódico) | T1071.001 | Sysmon EID 3 (conexões periódicas) | Validado |

## TA0040 Impacto

| Técnica | MITRE | Evento / gatilho | Status |
|---|---|---|---|
| Ransomware: alteração em massa | T1486 | FIM (muitas modificações) | Regra pronta |
| Deleção de shadow copies | T1490 | `vssadmin delete shadows` | Validado |
| Parada de serviços críticos | T1489 | System / Sysmon | Regra pronta |

---

> As técnicas marcadas como **Parcial** (dump de LSASS) são **contidas por controle** — o
> estudo valida o hardening, não a execução ofensiva. As **Lacunas** dependem de telemetria
> que o ambiente pode não emitir (ex.: EID 22/DnsQuery) ou de ajuste de override, e estão
> registradas com honestidade (ver [metodologia](../docs/02-metodologia.md)).

[◀ README](../README.md)
