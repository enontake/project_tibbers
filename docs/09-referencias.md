# 09 — Referências

[◀ Resultados](08-resultados-e-licoes.md)

Material de apoio usado no estudo.

## Documentação

- **Wazuh** — ruleset, decoders, Active Response, detecção de vulnerabilidades e SCA:
  https://documentation.wazuh.com
- **MITRE ATT&CK** — táticas e técnicas: https://attack.mitre.org
- **Sysmon (Sysinternals)** — telemetria de endpoint e IDs de evento (documentação da
  Microsoft/Sysinternals).
- **Windows Security Auditing** — catálogo de eventos de segurança do Windows (4624, 4625,
  4662, 4688, 4698, 4720, 4740, 4769, 4776, 5136, 7040, 7045, 1102).
- **auditd** — auditoria de kernel no Linux (regras e *keys*).
- **Chocolatey** — gerenciador de pacotes para patch de software de terceiros no Windows.
- **Atomic Red Team / Sigma** — validação de detecção e detecção-como-código (roadmap).

## Táticas MITRE ATT&CK referenciadas

| Tática | ID |
|---|---|
| Acesso Inicial | TA0001 |
| Execução | TA0002 |
| Persistência | TA0003 |
| Escalonamento de Privilégio | TA0004 |
| Evasão de Defesa | TA0005 |
| Acesso a Credenciais | TA0006 |
| Descoberta | TA0007 |
| Movimento Lateral | TA0008 |
| Coleta | TA0009 |
| Exfiltração | TA0010 |
| Comando e Controle | TA0011 |
| Impacto | TA0040 |

## Eventos de referência (amostra)

| Evento | Canal | Indica |
|---|---|---|
| 4625 / 4740 | Security | Falha de logon / lockout |
| 4769 / 4768 | Security | Ticket Kerberos (TGS/AS) |
| 4662 / 5136 | Security | Acesso/alteração em objetos do AD |
| 4698 / 4720 | Security | Tarefa agendada / criação de conta |
| 7045 / 7040 | System | Novo serviço / serviço alterado |
| Sysmon 1/3/10/11/13/15 | Sysmon/Operational | Processo / rede / acesso a processo / arquivo / registro / ADS |

[◀ Resultados](08-resultados-e-licoes.md) · [README ▶](../README.md)
