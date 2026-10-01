# 05 — Resposta e remediação

[◀ Engenharia de detecção](04-engenharia-de-deteccao.md)

Detectar é metade do trabalho. A outra metade é **conter** e **remediar** — de preferência a
partir do próprio SIEM, sem ferramentas extras.

## Active Response

O Wazuh executa ações automáticas a partir de um alerta. As duas estudadas:

| Comando | Quando dispara | Observação |
|---|---|---|
| `firewall-drop` | Brute force (regra de nível alto) e alertas nível ≥ 12 | Bloqueia o **IP de origem** por um tempo. **Só funciona com `srcip`** — útil para Linux/rede, inútil para alertas Windows (que não trazem `srcip`). |
| `choco-upgrade` | Sob demanda / resposta a vulnerabilidade | Atualiza software via Chocolatey no endpoint (ver patch, abaixo). |

> Lição operacional: o `firewall-drop` extrai `srcip` do alerta; como o canal Windows não
> popula esse campo, a resposta para ataques de identidade no Windows é **lockout de conta via
> GPO**, não bloqueio de IP.

## Resposta por tática (resumo)

| Técnica | Resposta estudada |
|---|---|
| Brute force / spraying (Windows) | Lockout de conta via GPO; investigar origem |
| Kerberoasting | Forçar AES nas contas de serviço; senha gerenciada (gMSA) |
| Exfiltração / C2 (com IP) | `firewall-drop` do destino; isolar host |
| Persistência (serviço/tarefa/registro) | Remover o artefato; caçar o vetor |
| Credential dumping | Bloqueado na origem por hardening (ver [06](06-hardening-e-roadmap.md)) |
| Impacto (ransomware/serviços) | Isolar, restaurar backup, acionar IR |

## Gestão de vulnerabilidades

O Wazuh inventaria o software dos agentes e cruza com bases de CVE, produzindo uma lista
priorizada por severidade (críticas/altas/médias) por host e por pacote. É o insumo para a
remediação.

### Patch em massa (Chocolatey via Active Response)

A maior parte das vulnerabilidades exploradas está em **software de terceiros** (navegadores,
leitores, runtimes) — exatamente o que o WSUS nativo não atualiza. O Chocolatey preenche essa
lacuna: um gerenciador de pacotes scriptável que atualiza dezenas de aplicativos com um
comando. Integrado ao Active Response, o patch é disparado de forma centralizada a partir do
SIEM que detectou a vulnerabilidade — fechando o ciclo **detecção → remediação** sem
ferramenta adicional.

Pontos de atenção observados no estudo:

- o fluxo de patch registra em log dedicado (o log padrão de Active Response pode travar sob o
  `execd`);
- o disparo fim-a-fim depende de credenciais da API do Wazuh e, por vezes, de empacotar o
  script como executável para o `execd` lançar de forma confiável.

## O ciclo de plantão

Tudo isso serve a um único ciclo, repetido a cada alerta:

**detectar → triar (priorizar por nível/severidade) → responder (conter) → documentar.**

Cada técnica estudada carrega a resposta cabível, de modo que o catálogo não é só "o que
detecta", mas "o que fazer quando detectar".

[◀ Engenharia de detecção](04-engenharia-de-deteccao.md) · [Hardening e evolução ▶](06-hardening-e-roadmap.md)
