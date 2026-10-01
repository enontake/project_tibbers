# 10 — Regras corporativas (conjunto adicional)

[◀ Referências](09-referencias.md)

Conjunto de regras **úteis no dia a dia de um SOC corporativo** — detecções de alto valor que
complementam o catálogo representativo do estudo. Os artefatos estão em
[`../configs/wazuh/local_rules-corporativas.xml`](../configs/wazuh/local_rules-corporativas.xml)
(IDs `110100`+). Os princípios de ancoragem são os mesmos de
[04 — Engenharia de detecção](04-engenharia-de-deteccao.md).

> Regras de linha de comando dependem do **Sysmon EID 1** (grupo `sysmon_event1`); regras de
> identidade/serviço, do **canal Security/System**. Validar sempre com
> `wazuh-analysisd -t` e testar com `wazuh-logtest`. Ajustar *allowlists* à realidade do
> ambiente (contas de administração, ferramentas de gestão legítimas).

## Catálogo

| Regra | Nível | Detecção | Tática / MITRE | Por que importa no corporativo |
|---|:---:|---|---|---|
| 110100 | 5 | Conta de domínio bloqueada (4740) | Credenciais · T1110 | Base para correlação; lockouts são sinal precoce |
| 110101 | 12 | Tempestade de bloqueios (5 em 5 min) | Credenciais · T1110 | Indica password spraying / brute force em escala |
| 110102 | 14 | Linha de comando tipo Mimikatz | Credenciais · T1003 | `sekurlsa`/`lsadump` = dump de credenciais |
| 110103 | 14 | Coleta do NTDS.dit (ntdsutil/IFM) | Credenciais · T1003.003 | Comprometimento total do AD |
| 110104 | 12 | Conta adicionada a grupo privilegiado | Persistência · T1098 | Escalada silenciosa (Domain/Enterprise Admins) |
| 110105 | 12 | Novo serviço em caminho suspeito | Persistência · T1543.003 | Serviço rodando de Temp/AppData = persistência |
| 110106 | 10 | Conta local criada via `net user /add` | Persistência · T1136.001 | Backdoor local fora do processo de mudança |
| 110107 | 12 | Log de segurança limpo (1102) | Evasão · T1070.001 | Tentativa clássica de apagar rastros |
| 110108 | 12 | Limpeza de log via `wevtutil cl` | Evasão · T1070.001 | Mesmo objetivo, por linha de comando |
| 110109 | 12 | Firewall do Windows desabilitado | Evasão · T1562.004 | Abre caminho para C2/lateral |
| 110110 | 13 | Proteção em tempo real do Defender off | Evasão · T1562.001 | Cega o antivírus antes do payload |
| 110111 | 12 | Exclusão adicionada ao Defender | Evasão · T1562.001 | Esconde o payload do antivírus |
| 110112 | 12 | Office gera shell/script | Execução · T1204.002 | Vetor nº 1 de phishing com macro |
| 110113 | 12 | PowerShell ofuscado / EncodedCommand | Execução · T1059.001 | Ofuscação típica de loader/stager |
| 110114 | 10 | Transferência via BITS | Evasão · T1197 | Download de payload "por baixo do radar" |
| 110115 | 12 | Criação remota de serviço (`sc \\host`) | Lateral · T1021.002 | Movimento lateral e execução remota |
| 110116 | 12 | Execução remota via `wmic /node` | Execução · T1047 | Movimento lateral por WMI |
| 110117 | 8 | Reconhecimento de contas/grupos do AD | Descoberta · T1087 | Enumeração que precede a escalada |
| 110118 | 14 | Inibir recuperação (bcdedit/wbadmin) | Impacto · T1490 | Pré-ransomware: impede restauração |
| 110119 | 6 | Parada de serviço (base) | Impacto · T1489 | Base para correlação |
| 110120 | 12 | Parada de múltiplos serviços (janela) | Impacto · T1489 | Padrão de ransomware/sabotagem |

## Notas de tuning

- **Falsos positivos previsíveis:** ferramentas de administração (SCCM/Intune/GPO), scripts de
  TI legítimos e instaladores podem casar algumas regras de linha de comando — manter
  *allowlist* por conta/host de gerência.
- **Regras de linha de comando** são tão boas quanto a visibilidade do Sysmon: confirmar que o
  EID 1 captura `CommandLine` (config do Sysmon).
- **Correlação** (110101, 110120) usa `if_matched_sid` + janela — ajustar `frequency`/
  `timeframe` ao volume do ambiente para equilibrar ruído e detecção.
- **Níveis** foram calibrados ao risco: dump de credenciais e inibição de recuperação em 14;
  evasão/persistência em 12–13; reconhecimento em 8.

[◀ Referências](09-referencias.md) · [README ▶](../README.md)
