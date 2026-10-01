# Casos estudados

[◀ README](../README.md)

As cinco técnicas, uma a uma: o que o atacante faz, o que fica registrado, como a regra casa
o evento e qual a resposta. A lógica completa das regras está em
[`docs/02-deteccao-e-regras.md`](../docs/02-deteccao-e-regras.md).

---

## Caso 01 — Brute force de logon (T1110)

- **Ataque.** Tentativas repetidas de autenticação contra uma conta (SMB/RDP/AD) a partir da
  estação ofensiva.
- **Telemetria.** Série de eventos **4625** (falha de logon) do mesmo IP de origem
  (`win.eventdata.ipAddress`) numa janela curta.
- **Detecção.** Regra `110001` (nível 12), escalando da built-in de frequência `60204`.
- **Resposta.** Lockout da conta via GPO (o canal Windows não traz `srcip`, então
  `firewall-drop` não se aplica).

## Caso 02 — Kerberoasting (T1558.003)

- **Ataque.** Solicitar tickets de serviço (TGS) para contas com SPN e crackear offline a
  cifra fraca.
- **Telemetria.** Evento **4769** com `ticketEncryptionType = 0x17` (RC4) para o SPN de
  `svc_app`.
- **Detecção.** Regra `110002` (nível 12), filha de `60103`, casando o campo de cifra.
- **Resposta.** Forçar AES nas contas de serviço; usar senha longa/gerenciada (gMSA).

## Caso 03 — Persistência por serviço (T1543.003)

- **Ataque.** Instalar um serviço que executa um binário/script do atacante, sobrevivendo a
  reinícios.
- **Telemetria.** Evento **7045** (novo serviço) com caminho/binário incomum. Pode ser
  reproduzido de forma benigna com um serviço de teste inócuo.
- **Detecção.** Regra `110003` (nível 10), filha de `61138`.
- **Resposta.** Remover o serviço e investigar o vetor de entrada.

## Caso 04 — Exfiltração por rede (T1041 / T1048)

- **Ataque.** Enviar dados do endpoint para um host externo (a estação ofensiva).
- **Telemetria.** Sysmon **EID 3** (conexão de rede) com `destinationIp` do host de ataque
  (`10.20.0.50`).
- **Detecção.** Regra `110004` (nível 8), filha de `92101`, casando o IP de destino.
- **Resposta.** Bloquear o IP de destino (`firewall-drop`) — aqui há `srcip`/IP de rede.

## Caso 05 — Sabotagem anti-forense (T1562.001)

- **Ataque.** Desabilitar um serviço de defesa/registro para cegar o SOC.
- **Telemetria.** Evento **7040** com `param3 = disabled`. Estudado de forma segura em um
  serviço de teste, sem tocar em serviços de defesa reais.
- **Detecção.** Regra `110005` (nível 12), filha de `61104`.
- **Resposta.** Investigar quem alterou, restaurar o serviço e revisar privilégios.

[◀ README](../README.md)
