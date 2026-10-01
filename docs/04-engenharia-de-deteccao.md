# 04 — Engenharia de detecção

[◀ Telemetria](03-telemetria.md)

O núcleo do estudo. Escrever uma regra que "parece certa" é fácil; escrever uma que **dispara
de verdade**, no nível certo e sem ruído, é o trabalho real. Aqui estão os princípios e as
armadilhas, destilados da prática.

## Princípio 1 — Ancoragem no pai

Regras do Windows que casam por `<field>` **precisam** do `<if_sid>` (ou `<if_group>`) da
built-in que decodifica o evento. Sem isso, o alerta cai em uma regra genérica de nível < 3 e
**nunca aparece** no `alerts.json`. Esta é a causa nº 1 de "a regra não dispara".

Pais usados ao longo do estudo:

| Evento | Pai (`if_sid`/`if_group`) |
|---|---|
| 4625 repetido (brute force) | `60204` (frequência built-in) |
| 4769 (TGS Kerberos) | `60103` (cadeia do canal Security) |
| 4688 e demais do canal Security | `60103` |
| 4720 (criação de conta) | `60109` (built-in dedicada) |
| 7045 (novo serviço) | `61138` |
| 7040 (serviço alterado) | `61104` (estado no campo `param3`) |
| Sysmon EID 1..9 | grupo `sysmon_event1`..`sysmon_event9` (sem `_`) |
| Sysmon EID 10..26 | grupo `sysmon_event_10`..`sysmon_event_26` (com `_`) |
| Sysmon EID 3 (rede) | `92101` / grupo `sysmon_event3` |
| PowerShell script block | `91802` |
| Web (access.log) | `31100` |

> Detalhe traiçoeiro: os grupos do Sysmon mudam de convenção — `sysmon_eventN` **sem**
> underscore para EID 1–9, e `sysmon_event_N` **com** underscore para EID 10–26.

## Princípio 2 — O campo é a precisão

Depois de ancorar, a regra discrimina pelo campo que carrega o indicador — por exemplo, um
4769 só interessa quando `ticketEncryptionType = 0x17` (RC4). Dois cuidados reais:

- **`grantedAccess` precisa de âncora exata.** Ao detectar acesso a LSASS (Sysmon EID 10), um
  padrão frouxo casa dentro de valores maiores (ex.: `0x1010` casa dentro de `0x101000`,
  gerando falso positivo no próprio antivírus). Ancorar o valor (`^0x...$`) e excluir caminhos
  legítimos.
- **O campo `url` é estático.** Usar a tag `<url type="pcre2">`; `<field name="url">` dá erro
  "Field 'url' is static" e **derruba o manager**.

## Princípio 3 — Correlação pede `if_matched`

Regras de frequência/correlação (várias ocorrências do mesmo campo numa janela) usam
`if_matched_sid`/`if_matched_group` — **não** `if_sid`/`if_group`. Trocar isso gera "Missing
if_matched" e o ruleset não carrega. Exemplo clássico: password spraying = mesma origem,
usuários diferentes, numa janela.

## Princípio 4 — Casar por localização positiva, não por negação

Para pegar binários em caminhos anômalos (masquerading), negative-lookahead e negação por
campo duplicado tendem a falhar no matcher (produzem falso positivo em binários legítimos de
sistema). O que funciona é o **casamento positivo** por localização suspeita (Temp, AppData,
diretórios de usuário). Muitas vezes a built-in *path-aware* (ex.: 61618/61625) já cobre o
caso melhor que uma regra nova.

## Princípio 5 — Nível condiz com o risco

Uma regra correta num nível abaixo do limiar de alerta é, na prática, invisível. O nível
comunica prioridade ao analista — calibrá-lo ao risco da técnica (e às vezes escalar de uma
built-in de nível baixo para um alerta de verdade).

## A armadilha do `srcip` no Windows

Eventos do canal Windows **não populam `srcip`** (o IP vem em `win.eventdata.ipAddress`).
Consequências diretas:

- correlação por IP usa `same_field` sobre `win.eventdata.ipAddress`, não `same_source_ip`;
- a resposta `firewall-drop` (que extrai `srcip`) **não se aplica** a alertas Windows — ali a
  contenção é no plano de identidade (**lockout via GPO**). O `firewall-drop` fica para
  alertas **com** `srcip` (tráfego Linux/rede).

## Precedência de regras

Quando uma built-in já casa o evento com bom nível, uma regra customizada redundante pode
ser desnecessária (gera alerta duplicado) ou precisa escalar explicitamente da built-in. E
cuidado com filtros que vencem antes: por exemplo, uma blacklist de *User-Agent* pode casar
(e silenciar) um teste de SQLi feito com `sqlmap`/`wget` antes da regra de SQLi — testar com
UA de navegador.

## Ferramentas que provam o comportamento

- `wazuh-analysisd -t` — valida o ruleset (sintaxe/carga).
- `wazuh-logtest` — Fase 2 mostra os campos decodificados; Fase 3 mostra a regra que casou.
- `<logall_json>` temporário — confirma se o evento chega ao manager (antes de ser descartado
  por nível). **Desligar depois**: enche disco.

[◀ Telemetria](03-telemetria.md) · [Resposta e remediação ▶](05-resposta-e-remediacao.md)
