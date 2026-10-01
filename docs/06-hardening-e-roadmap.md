# 06 — Hardening e evolução

[◀ Resposta](05-resposta-e-remediacao.md)

Detecção é reativa; **hardening** reduz a superfície antes do ataque. E um SOC não nasce
maduro — este documento fecha o estudo com o que endurecer e para onde evoluir.

## Hardening de endpoint

O caso mais ilustrativo é o **credential dumping** (dump de LSASS). Em vez de só detectar,
o estudo mostra como **impedir** a técnica no host:

- **LSA Protection (RunAsPPL).** Faz o `lsass.exe` subir como *protected process*. Após
  aplicar e reiniciar, o próprio Windows registra o evento (Wininit) confirmando que o LSASS
  iniciou como processo protegido — o acesso de dump é negado.
- **Regra ASR "block credential stealing from lsass".** Camada adicional do Defender que
  bloqueia o acesso malicioso ao LSASS.
- **Credential Guard** (opcional). Isola segredos em ambiente baseado em virtualização;
  cuidado em VMs aninhadas, onde pode não ativar corretamente.

Resultado conceitual: a técnica passa a ser **contida pelo controle**, não apenas detectada —
o que, na prática, demonstra a defesa funcionando. (No estudo, valida-se o **controle**; a
re-execução ofensiva segue bloqueada, o que é o comportamento desejado.)

## Outras medidas de redução de superfície

- Auditoria avançada por GPO (habilita os eventos que as detecções consomem).
- Forçar **AES** em contas de serviço (fecha Kerberoasting por RC4).
- Política de senhas e contas gerenciadas (gMSA) para serviços.
- FIM em caminhos sensíveis para flagrar alteração indevida.

## Roadmap de maturidade do SOC

Como incrementar a plataforma, por prioridade (A alta · M média · E evolução):

| Área | Incremento | Prio |
|---|---|:---:|
| Threat Intel | Feeds de IOC (CDB lists) e integração com plataformas de inteligência | A |
| Detecção-como-código | Conversão de regras Sigma, validação contínua com Atomic Red Team, CI de regras | A |
| Resposta (SOAR) | Playbooks (isolar host, desabilitar conta) e orquestração | A/M |
| Hardening | LSA Protection + Credential Guard + ASR + política de contas | A |
| Compliance/visibilidade | SCA/CIS, relatórios de conformidade, dashboards de KPI (MTTD/MTTR) | M |
| Fontes/escala | Logs de nuvem e identidade; cluster de Indexers + alta disponibilidade | M/E |

## Por que esta stack se defende

- **Custo de licença ~0** com capacidade de SIEM/XDR corporativo, à custa de engenharia
  própria — que este estudo demonstra dominar.
- **Mensurável:** cobertura mapeada ao MITRE ATT&CK, resposta e remediação a partir do próprio
  SIEM, limites documentados com honestidade.
- **Escalável:** a mesma tecnologia vai do host único ao cluster de produção.

[◀ Resposta](05-resposta-e-remediacao.md) · [Cobertura MITRE ▶](07-cobertura-mitre.md)
