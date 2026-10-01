# 03 — Telemetria

[◀ Metodologia](02-metodologia.md)

Nenhuma detecção existe sem o evento que a sustenta. Este documento mapeia as fontes de
telemetria do ambiente e o que cada uma habilita — a base de todo o resto.

## Fontes por plataforma

### Windows (grupo `windows`)

- **Canal Security** — autenticação e identidade: 4624/4625 (logon), 4740 (lockout), 4768/4769
  (Kerberos), 4776 (NTLM), 4688 (criação de processo), 4698 (tarefa agendada), 4720 (criação
  de conta), 4662/5136 (mudanças no AD).
- **Canal System** — serviços e drivers: 7045 (novo serviço), 7040 (serviço alterado).
- **Sysmon** (`Microsoft-Windows-Sysmon/Operational`) — telemetria profunda de endpoint:

  | EID | Registra |
  |---|---|
  | 1 | Criação de processo (linha de comando, pai/filho) |
  | 2 | Alteração de timestamp de arquivo (timestomping) |
  | 3 | Conexão de rede (origem/destino) |
  | 7 | Carregamento de módulo/DLL |
  | 10 | Acesso a processo (ex.: a `lsass.exe`) |
  | 11 | Criação de arquivo |
  | 13 | Alteração de valor no Registro |
  | 15 | Criação de *Alternate Data Stream* |

- **Windows Defender** (`.../Windows Defender/Operational`) — detecção de malware/EICAR.
- **FIM** — integridade de arquivos em caminhos sensíveis.

### Linux (grupo `linux`)

- `auth.log` — autenticação SSH/sudo.
- **auditd** — auditoria de kernel (execução, acesso a arquivos) com *keys* pesquisáveis.
- **nginx** `access.log`/`error.log` — ataques web.
- **FIM** em `/var/www` — defacement/webshell.

## Coleta por grupo (conceito)

A coleta é definida por grupo de agentes (`agent.conf`): o grupo `windows` encaminha os canais
Security/System/Sysmon/Defender e faz FIM; o grupo `linux` encaminha auth/auditd/nginx e faz
FIM realtime em `/var/www`. Centralizar por grupo mantém a telemetria consistente entre hosts
do mesmo papel.

## Lições de telemetria (decisivas para a detecção)

1. **O canal de eventos do Windows não popula `srcip`.** O IP de origem vem em
   `win.eventdata.ipAddress`. Isso muda a correlação (usar `same_field` sobre esse campo) e
   limita a resposta automatizada (ver [resposta](05-resposta-e-remediacao.md)).
2. **A cobertura do Sysmon depende da config.** Eventos como EID 2/11/13/15/22 só aparecem se
   a configuração do Sysmon os habilitar; "Sysmon instalado" não significa "Sysmon emitindo o
   que você precisa".
3. **Alguns eventos simplesmente não vêm.** Em certos builds, o Sysmon pode não emitir o EID
   22 (DnsQuery) mesmo configurado — uma **lacuna de telemetria** que nenhuma regra resolve;
   exige fonte alternativa.
4. **Saúde do pipeline é pré-requisito.** Agente offline, enrollment falho, logs não chegando
   ou flood de eventos cegam o SOC antes de qualquer detecção — por isso são o primeiro tema
   estudado (ver [casos](../casos/README.md), fase de fundamentos).

[◀ Metodologia](02-metodologia.md) · [Engenharia de detecção ▶](04-engenharia-de-deteccao.md)
