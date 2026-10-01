# 04 — Referências

[◀ Resultados](03-resultados-e-licoes.md)

Material de apoio usado no estudo.

## Documentação

- **Wazuh** — ruleset, decoders e sintaxe de regras: https://documentation.wazuh.com
- **MITRE ATT&CK** — táticas e técnicas: https://attack.mitre.org
- **Sysmon (Sysinternals)** — telemetria de endpoint e IDs de evento: documentação oficial da
  Microsoft/Sysinternals.
- **Windows Security Auditing** — significado dos eventos 4625, 4769, 7040, 7045 no catálogo
  de eventos de segurança do Windows.

## Técnicas citadas (MITRE ATT&CK)

| ID | Técnica |
|---|---|
| T1110 | Brute Force |
| T1558.003 | Steal or Forge Kerberos Tickets: Kerberoasting |
| T1543.003 | Create or Modify System Process: Windows Service |
| T1041 | Exfiltration Over C2 Channel |
| T1048 | Exfiltration Over Alternative Protocol |
| T1562.001 | Impair Defenses: Disable or Modify Tools |

## Eventos de referência

| Evento | Canal | O que indica |
|---|---|---|
| 4625 | Security | Falha de logon |
| 4769 | Security | Solicitação de ticket de serviço Kerberos (TGS) |
| 7045 | System | Novo serviço instalado |
| 7040 | System | Alteração do tipo de inicialização de um serviço |
| Sysmon 3 | Sysmon/Operational | Conexão de rede |
| Sysmon 10 | Sysmon/Operational | Acesso a processo (ex.: LSASS) |

[◀ Resultados](03-resultados-e-licoes.md) · [README ▶](../README.md)
