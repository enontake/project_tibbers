# 00 — Contexto e escopo

[◀ README](../README.md)

## Motivação

Operar um SIEM corporativo não se resume a instalá-lo. O valor está em **engenharia de
detecção** (entender qual evento registra cada técnica e escrever regras que de fato
disparem), em **resposta** (conter o incidente a partir do próprio SIEM) e em **redução de
superfície** (vulnerabilidades e hardening). Este estudo documenta esse ciclo completo sobre
uma stack open source, usando o Wazuh como núcleo.

A escolha por open source é deliberada: demonstra que é possível montar capacidade de
detecção e resposta de nível corporativo com **custo de licença próximo de zero**, à custa de
engenharia própria — que é justamente o objeto desta pesquisa.

## Perguntas de pesquisa

1. Que arquitetura mínima reproduz um ambiente corporativo realista e isolado para estudar
   detecção?
2. Quais fontes de telemetria registram cada tática do MITRE ATT&CK, e com quais limitações?
3. Como escrever regras **confiáveis** — que disparem no evento certo, no nível certo, sem
   serem engolidas por regras genéricas?
4. Que respostas automatizadas são viáveis a partir do próprio SIEM, e quando cada uma se
   aplica?
5. Como fechar o ciclo com gestão de vulnerabilidades e hardening, e como medir maturidade?

## Os cinco pilares (escopo)

| Pilar | O que o estudo cobre |
|---|---|
| Telemetria | Sysmon (EIDs de processo/rede/registro/LSASS), canais Security/System, Windows Defender, auditd (Linux), logs web (nginx), FIM |
| Detecção | Regras locais, decoders, correlação, ancoragem no ruleset, mapeamento MITRE |
| Resposta | Active Response (`firewall-drop`), lockout de conta via GPO, contenção |
| Vulnerabilidades | Detecção de CVEs por inventário + patch em massa (Chocolatey via Active Response) |
| Validação | Ataques controlados (purple team) que provam cada detecção com evidência |

## O que está fora do escopo

- **Provisionamento/instalação passo a passo** — o interesse é a operação e a detecção, não
  o build da infraestrutura.
- **Escala de produção** (cluster de Indexers, alta disponibilidade) — citada como evolução,
  não executada.
- **Enriquecimento externo** (threat intel comercial) — tratado no roadmap.

## Fases do estudo

O material acompanha a maturação de um SOC em três fases, da base à detecção avançada:

1. **Fundamentos e saúde do pipeline** — a telemetria precisa estar íntegra antes de detectar
   qualquer coisa (agente online, enrollment, logs chegando, Sysmon emitindo, sem flood).
2. **Detecção de ataques** — técnicas ofensivas comuns (brute force, Kerberoasting, dumping,
   web, malware) e suas detecções.
3. **Detecção avançada e evasão** — persistência, exfiltração, anti-forense e as técnicas que
   tentam cegar o SOC.

[◀ README](../README.md) · [Ambiente de referência ▶](01-ambiente-de-referencia.md)
