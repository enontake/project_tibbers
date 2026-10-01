# Tibbers — Pesquisa em operação de um SOC com Wazuh

Estudo aplicado sobre como **operar um Security Operations Center** (blue team) de ponta a
ponta sobre uma stack **open source** centrada no **Wazuh** (SIEM/XDR). Não é um guia de
instalação nem um laboratório para montar: é uma **pesquisa documentada** sobre as decisões,
os mecanismos e as lições de cada parte de um SOC — telemetria, engenharia de detecção,
resposta, gestão de vulnerabilidades, hardening e validação *purple team*.

O material cobre um ambiente corporativo de referência (Active Directory, servidores,
estações, servidor web e uma estação ofensiva) e analisa **detecções mapeadas ao MITRE
ATT&CK**, incluindo por que cada regra funciona — ou falha — e o que isso ensina.

## Pergunta central

> Como montar, operar e **defender tecnicamente** um SOC corporativo sobre ferramentas open
> source (custo de licença ~0), com detecção confiável e resposta efetiva, e quais armadilhas
> de telemetria e de engenharia de detecção precisam ser contornadas?

## Como o estudo está organizado

| # | Documento | O que investiga |
|---|---|---|
| 00 | [Contexto e escopo](docs/00-contexto-e-escopo.md) | Motivação, perguntas de pesquisa e os cinco pilares do SOC estudado |
| 01 | [Ambiente de referência](docs/01-ambiente-de-referencia.md) | Topologia, rede isolada, domínio e papéis dos hosts (conceitual) |
| 02 | [Metodologia](docs/02-metodologia.md) | Como cada detecção/afirmação é construída e validada |
| 03 | [Telemetria](docs/03-telemetria.md) | Fontes de log: Sysmon, auditd, canais do Windows, FIM |
| 04 | [Engenharia de detecção](docs/04-engenharia-de-deteccao.md) | Regras, decoders, ancoragem, correlação e as armadilhas do ruleset |
| 05 | [Resposta e remediação](docs/05-resposta-e-remediacao.md) | Active Response, lockout GPO, gestão de vulnerabilidades e patch |
| 06 | [Hardening e evolução](docs/06-hardening-e-roadmap.md) | Redução de superfície (RunAsPPL, ASR) e roadmap de maturidade |
| 07 | [Cobertura MITRE ATT&CK](docs/07-cobertura-mitre.md) | As 12 táticas e o catálogo de técnicas estudadas |
| 08 | [Resultados e lições](docs/08-resultados-e-licoes.md) | O que generaliza: lições sistêmicas de detecção e operação |
| 09 | [Referências](docs/09-referencias.md) | Documentação e material de apoio |

Complementos:

- [Casos estudados](casos/README.md) — catálogo das técnicas por tática (o "o quê" e o "como se detecta").
- [`configs/wazuh/local_rules.xml`](configs/wazuh/local_rules.xml) — artefatos de detecção (regras representativas).

## Os cinco pilares estudados

1. **Telemetria** — Sysmon, canais de evento do Windows, Windows Defender, auditd, logs web, FIM.
2. **Detecção** — regras customizadas + built-in, correlação, alinhamento MITRE ATT&CK.
3. **Resposta** — Active Response (bloqueio de IP), lockout de conta via GPO, contenção.
4. **Gestão de vulnerabilidades** — detecção de CVEs e patch em massa (Chocolatey via Active Response).
5. **Validação (purple team)** — ataques controlados validam cada detecção.

## Observações

- Ambiente **isolado**; ferramentas ofensivas apenas na estação dedicada.
- **Nenhuma credencial real** — todos os valores são fictícios e exclusivos deste estudo.
- Material **educacional**, com foco em defesa. O interesse é **detectar e corrigir**, não atacar.
