<<<<<<< HEAD >>>>>>>>
# project_tibbers
Pesquisas do ciclo completo de operação de um SIEM
=======
# Tibbers — Estudo de engenharia de detecção com Wazuh

Pesquisa aplicada sobre **detecção e resposta (blue team)** usando o **Wazuh** (SIEM/XDR
open source). O objetivo não é entregar um laboratório para montar, e sim **documentar e
analisar** como técnicas de ataque comuns são detectadas, por que as regras funcionam (ou
falham) e o que isso ensina sobre engenharia de detecção.

É um recorte enxuto, centrado em cinco técnicas representativas do MITRE ATT&CK.

## Pergunta de pesquisa

> Dado um ambiente corporativo típico (Active Directory + endpoints Windows) monitorado por
> Wazuh, **como escrever regras de detecção confiáveis** para técnicas comuns de ataque, e
> quais armadilhas de telemetria e de ruleset precisam ser contornadas?

## Escopo

- **Cinco técnicas estudadas:** brute force, Kerberoasting, persistência por serviço,
  exfiltração por rede e sabotagem anti-forense.
- **Fontes de telemetria:** canais de evento do Windows (Security/System) e Sysmon.
- **Foco:** a lógica de detecção (regra + ancoragem + MITRE) e as lições de tuning — não a
  automação de infraestrutura.

## O que você encontra aqui

| Documento | Conteúdo |
|---|---|
| [00 — Contexto e escopo](docs/00-contexto-e-escopo.md) | Motivação, perguntas e o ambiente de referência estudado |
| [01 — Metodologia](docs/01-metodologia.md) | Como cada detecção é construída e validada |
| [02 — Detecção e regras](docs/02-deteccao-e-regras.md) | As cinco regras, ancoragem no ruleset e MITRE |
| [03 — Resultados e lições](docs/03-resultados-e-licoes.md) | Achados, armadilhas e o que generaliza |
| [04 — Referências](docs/04-referencias.md) | Documentação e material de apoio |
| [Casos estudados](casos/README.md) | As cinco técnicas, uma a uma |

Os artefatos de detecção (regras) estão em
[`configs/wazuh/local_rules.xml`](configs/wazuh/local_rules.xml).

## Ambiente de referência

O estudo assume um ambiente isolado e pequeno, descrito em
[00 — Contexto e escopo](docs/00-contexto-e-escopo.md): um domínio `corp.tibbers.lab`
(`10.20.0.0/24`) com um controlador de domínio que também serve de endpoint monitorado, o
Wazuh all-in-one e uma estação ofensiva usada apenas para gerar a telemetria de ataque.

## Observações

- Ambiente **isolado**; ferramentas ofensivas apenas na estação dedicada.
- **Nenhuma credencial real** — valores fictícios documentados.
- Material educacional, para estudo de detecção defensiva.
>>>>>>> ffafd61 (docs: estudo de engenharia de deteccao com Wazuh)
