# 07 — Cobertura MITRE ATT&CK

[◀ Hardening](06-hardening-e-roadmap.md)

O estudo organiza as técnicas pelas **táticas** do MITRE ATT&CK, o que torna a cobertura
mensurável e comunicável. O catálogo detalhado, caso a caso, está em
[`casos/README.md`](../casos/README.md); aqui fica a visão por tática e a cadeia de ataque.

## Cadeia de ataque ponta a ponta

Encadeamento típico de um comprometimento no ambiente de referência, do acesso inicial ao
impacto — cada etapa é uma tática, e toda a telemetria converge para o Wazuh:

```
 origem (estacao ofensiva / host comprometido)
   |
   v
 Acesso Inicial -> Execucao -> Escalonamento -> Persistencia
                      |            |
                      v            v
                 Credenciais   Evasao de Defesa
                      |
                      v
                 Descoberta -> Movimento Lateral -> Coleta -> C2 -> Exfiltracao -> Impacto
   |
   '--> telemetria (Sysmon / auditd / canais de evento) --> Wazuh (deteccao + resposta)
```

## Táticas cobertas

| Tática MITRE | Foco das técnicas estudadas |
|---|---|
| Fundamentos / Observabilidade | Saúde do pipeline: agente, enrollment, coleta, Sysmon, anti-flood |
| TA0001 Acesso Inicial | Ataque web (SQLi, log poisoning, defacement), phishing |
| TA0002 Execução | Malware/discovery, PowerShell ofuscado, WMI |
| TA0003 Persistência | Serviço, tarefa agendada, chaves Run, pasta Startup, assinatura WMI, conta nova |
| TA0004 Escalonamento de Privilégio | Bypass de UAC, manipulação de token, abuso de GPO |
| TA0005 Evasão de Defesa | LOLBins (mshta/rundll32/regsvr32), anti-forense, timestomp, ADS, tamper no Defender, limpeza de log |
| TA0006 Acesso a Credenciais | Brute force, Kerberoasting, dump de LSASS, DCSync, NTDS, spraying, AS-REP |
| TA0007 Descoberta | Contas/domínio, recon LDAP, compartilhamentos |
| TA0008 Movimento Lateral | Pass-the-Hash, PsExec, RDP, WinRM |
| TA0009 Coleta | Staging/compactação de dados |
| TA0010 Exfiltração | Canal de rede, túnel DNS |
| TA0011 Comando e Controle | Beaconing periódico |
| TA0040 Impacto | Ransomware, deleção de shadow copies, parada de serviços, flood |

## Como ler a cobertura

Cobertura **não** é "tem regra para tudo". Para cada técnica, o catálogo registra honestamente
o status (validado / parcial / regra pronta / lacuna), porque:

- algumas técnicas são **contidas por controle** (dump de LSASS), e o que se prova é o
  controle;
- outras dependem de **telemetria que o ambiente pode não emitir** (ex.: EID 22 de DNS),
  configurando uma lacuna;
- outras têm a **regra pronta** mas aguardam um gatilho/ferramenta para evidência real.

Essa honestidade é parte do método (ver [metodologia](02-metodologia.md)).

[◀ Hardening](06-hardening-e-roadmap.md) · [Resultados e lições ▶](08-resultados-e-licoes.md)
