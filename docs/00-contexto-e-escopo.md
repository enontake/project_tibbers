# 00 — Contexto e escopo

[◀ README](../README.md)

## Motivação

Detectar ataques com um SIEM não é "ligar e esperar o alerta". A maior parte do esforço está
em **engenharia de detecção**: entender qual evento registra a técnica, escrever uma regra
que case exatamente esse evento e separar o sinal do ruído. Este estudo documenta esse
trabalho para cinco técnicas comuns, usando o Wazuh.

## Perguntas de pesquisa

1. Qual evento (e qual campo) registra cada técnica no Windows/Sysmon?
2. Como ancorar a regra para que ela realmente dispare, em vez de ser engolida por uma regra
   genérica de nível baixo?
3. Quais limitações de telemetria (campos ausentes, canais que não populam certos dados)
   mudam a estratégia de detecção e de resposta?

## Técnicas estudadas (MITRE ATT&CK)

| # | Técnica | ID | Por que importa |
|---|---|---|---|
| 01 | Brute force de logon | T1110 | Porta de entrada clássica; alto volume, fácil de confundir com erro legítimo |
| 02 | Kerberoasting | T1558.003 | Abuso de SPN com cifra fraca; indicador sutil (RC4 em 4769) |
| 03 | Persistência por serviço | T1543.003 | Mecanismo durável e comum de persistência |
| 04 | Exfiltração por rede | T1041 / T1048 | Saída de dados para host externo; exige telemetria de rede |
| 05 | Sabotagem anti-forense | T1562.001 | Desabilitar serviços de defesa para cegar o SOC |

## Ambiente de referência

O estudo assume um ambiente mínimo e isolado — suficiente para gerar a telemetria das cinco
técnicas, sem a complexidade de uma rede corporativa inteira.

| Host | IP | Papel |
|---|---|---|
| wazuh | 10.20.0.10 | Wazuh all-in-one (Manager + Indexer + Dashboard) |
| dc01 | 10.20.0.5 | Domínio `corp.tibbers.lab` (AD/DNS) e endpoint monitorado (Sysmon) |
| atacante | 10.20.0.50 | Estação ofensiva, usada só para gerar a telemetria de ataque |

Rede isolada `10.20.0.0/24`, sem bridge com a rede física. Contas são fictícias: usuário de
domínio `jdoe` e conta de serviço `svc_app` (com SPN, necessária para o cenário de
Kerberoasting).

> Este é o **cenário de referência** do estudo, não um guia de instalação. O interesse está
> na detecção, não no provisionamento.

[◀ README](../README.md) · [Metodologia ▶](01-metodologia.md)
