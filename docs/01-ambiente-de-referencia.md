# 01 — Ambiente de referência

[◀ Contexto](00-contexto-e-escopo.md)

O estudo assume um ambiente corporativo pequeno, porém realista e **totalmente isolado** —
suficiente para gerar a telemetria de todas as táticas estudadas. Este documento descreve o
ambiente de referência (o "sob o que" a pesquisa raciocina), não um roteiro de instalação.

## Rede

- **vSwitch interno** `TIBBERS-LAB` (Hyper-V, tipo *Internal*).
- **NAT** `TIBBERS-NAT` em `10.20.0.0/24` (host = `10.20.0.1`), só para updates/instalação.
- **Isolamento:** sem bridge com a placa física; a rede do estudo não toca a rede pessoal.

```
 Internet --(NAT)--> [ TIBBERS-LAB  10.20.0.0/24 ]   host/gateway = 10.20.0.1
                     dominio Windows = corp.tibbers.lab

   wazuh(.10)  Manager+Indexer+Dashboard
   dc01(.5) AD/DNS/GPO   srv01(.20) File/Print   ws01(.30) estacao
   web01(.40) Nginx (Linux)            atacante(.50) ofensiva (so nos ensaios)
```

## Inventário de referência

| Host | SO | Papel | IP | Agente |
|---|---|---|---|:---:|
| wazuh | Ubuntu Server | Wazuh all-in-one (Manager + Indexer + Dashboard) | 10.20.0.10 | 000 |
| dc01 | Windows Server | Domain Controller, DNS, GPOs | 10.20.0.5 | sim |
| srv01 | Windows Server | File/Print server | 10.20.0.20 | sim |
| ws01 | Windows | Estação de usuário (`jdoe`) | 10.20.0.30 | sim |
| web01 | Ubuntu Server | Servidor web Nginx | 10.20.0.40 | sim |
| atacante | Kali Linux | Estação ofensiva (ligada **só** nos ensaios) | 10.20.0.50 | não |

> O ambiente é dimensionado para caber em um host modesto: memória dinâmica por VM e a
> estação ofensiva desligada fora dos ensaios de ataque.

## Domínio e identidades

- Floresta/domínio **`corp.tibbers.lab`**, com OUs para contas e servidores.
- Usuário comum `jdoe`; conta de serviço `svc_app` **com SPN** (necessária para o estudo de
  Kerberoasting).
- Auditoria avançada habilitada por GPO (logon, Kerberos, criação de serviço, mudanças em
  objetos do AD) — é o que alimenta boa parte das detecções.

## Papel do Wazuh (núcleo)

O Wazuh concentra, numa única plataforma open source, capacidades que normalmente exigiriam
vários produtos: SIEM (análise/correlação de logs), XDR (detecção/resposta em endpoint), FIM
(integridade de arquivos), SCA (avaliação de configuração/CIS) e detecção de vulnerabilidades
por inventário de software. A arquitetura **Manager + Indexer + Dashboard** escala do host
único a milhares de agentes sem troca de tecnologia.

## Credenciais fictícias

> ⚠️ Valores **fictícios**, exclusivos deste ambiente isolado. Nunca reutilizar fora dele.

| Conta | Usuário | Senha (fictícia) |
|---|---|---|
| Admin local/domínio | `Administrator` | `T!bbers#Lab2026` |
| Usuário de domínio | `jdoe` | `Usuario#2026` |
| Conta de serviço (SPN) | `svc_app` | `Servico#2026` |

## Convenções

- Dados pesados (imagens de SO, discos de VM) ficariam fora do versionamento.
- Ferramentas ofensivas **só** na estação dedicada, nunca nos demais hosts.
- Parâmetros do ambiente (rede, inventário) tratados como fonte única de verdade.

[◀ Contexto](00-contexto-e-escopo.md) · [Metodologia ▶](02-metodologia.md)
