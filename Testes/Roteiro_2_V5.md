# Testes V2 — Atendimento IA Recall (Luiza), agentes V5

Segunda bateria de testes. Contém (1) os cenários do `Testes/Roteiro_1_V2aV5.md` que ainda **não passaram** ou **passaram com ressalva** e (2) mais de 200 cenários **novos**, escritos para a V5 (Triagem, Credor, Herdeiro, Parceria Adv., Parceria Geral).

Fonte dos pendentes: resultados registrados em `Testes/Roteiro_1_V2aV5.md` até 26/11/2026 (V2, V3 e V5). Os resultados esperados dos cenários novos saem dos prompts V5 em `Agentes/V5/` (arquivos `<Agente> - prompt.md`).

## Como rodar

0. Rode sempre no contato **Alexandre** (conexão 5 ou 10: anote qual). Conversas de leads reais servem só de exemplo.
1. Zere o contexto (botão de limpeza) e confirme que a **Triagem V5** está atribuída antes de cada cenário.
2. Mande as mensagens na ordem. `→` separa uma mensagem do cliente da seguinte. Para mensagens picotadas, a barra `/` separa cada mensagem enviada em sequência.
3. Preencha a coluna **Res.**: ✅ passou · ⚠️ passou com ressalva · ❌ falhou · — não rodado.
4. A IA não consegue enviar mensagens como cliente. Quem envia é o usuário, e a avaliação (conversa + eventos de transferência) é lida pela API em modo leitura.

**Legenda**
- **Texto:** código do catálogo que deve sair (`[BV]`, `[C-DOC]` etc.). O código nunca aparece para o cliente.
- **→ Credor / Herdeiro / Parceria Adv. / Parceria Geral:** transferência entre agentes **sem mensagem**; quem recebe responde na hora.
- **Humano:** só quando a pessoa **pede** atendente, pessoa, humano, Henzo ou ligação. A resposta tem `[FIM-ATEND]` escrito + a ferramenta.
- **Encerra:** `[FIM-HENZO]` (Credor, Parceria Adv./Geral parceiro e outros), `[FIM-OK-HERD]` (Herdeiro) ou `[FIM-INV]` (investidor). Nunca transfere para humano por isso.
- **Sem resposta:** a IA não manda nada (figurinha; "ok" depois do encerramento).

## Critérios que valem em todos os cenários

- [ ] Nenhum código `[XX]`, JSON, aspas, nome de ferramenta ou símbolo estranho para o cliente.
- [ ] "Eu sou a Luiza" aparece **uma vez** na conversa inteira, e já na primeira resposta de quem atende.
- [ ] Todo texto do catálogo sai palavra por palavra; o mesmo texto não sai duas vezes no atendimento (use a versão `-B`).
- [ ] Nenhum valor, percentual, comissão, prazo ou orientação jurídica. Única exceção: o `[X-PAGTO]` (em até 7 dias úteis após a escritura).
- [ ] Quem recebe uma transferência faz a próxima pergunta; nunca encerra nem transfere para humano na primeira resposta.
- [ ] Nada de documento, processo, CPF ou inventário antes de a pessoa dizer que **tem** um precatório.
- [ ] Sem frases de empatia fora do que o prompt permite (V5: sem "Que bom", "Perfeito!", "Poxa", "Boa pergunta" etc.).
- [ ] Sem "se não souber", "se preferir", "pode mandar depois", "quando puder".
- [ ] Sem pergunta repetida do que já foi respondido (processo, CPF, nome, inventário, OAB).
- [ ] Pedido de atendente: o cliente recebe o `[FIM-ATEND]` e **nunca** a frase padrão "Nossa equipe responderá assim que possível.".
- [ ] Fala sempre no feminino.

---

# PARTE 1 — Pendentes do `Testes/Roteiro_1_V2aV5.md` (não passaram ou passaram com ressalva)

Reteste obrigatório. A coluna **Origem** traz o último resultado registrado.

## 1.1 Falhas abertas

| ID | Origem | Cenário | Esperado | O que falhou | Dependência | Res. |
|---|---|---|---|---|---|---|
| R1 | ❌ J6, J1 pós-desistência, S9, T3, T4, U1, U4 | Em qualquer agente (Credor, Parceria Adv.), depois do 1º documento ou da desistência: "Quero falar com um atendente" | `[FIM-ATEND]` + transferência para humano, na mesma resposta | A IA chama a ferramenta sem escrever o texto. O cliente recebe só "Nossa equipe responderá assim que possível." ou nada (T4). Acontece no Sol e no Terra | **Broker/suporte** (descarta o texto da IA na transferência). Prompt já ajustado | |
| R2 | ❌ J6 | "Pode me ligar?" depois do encerramento | Igual ao R1: `[FIM-ATEND]` + humano | Mesma causa do R1 | Broker | |
| R3 | ❌ N1 (V3) | Dois precatórios: um próprio e um do pai falecido, no Credor | O Credor termina o caso próprio e transfere **uma vez** para o Herdeiro, que encerra. Sem devolver | Pingue-pongue Credor ↔ Herdeiro; "Certo." solto; teve que perguntar "E agora?" | Ajuste de V3 aplicado (DOIS PERFIS); **retestar na V5** | |
| R4 | ❌ N2 (V3) | Advogada com precatório próprio **e** clientes | Credor faz o caso próprio, transfere para Parceria Adv., que encerra. Sem devolver | Voltou para o Credor, que mandou "Que bom ter você de volta!" e parou | Idem R3 | |
| R5 | ❌ D4 (V3) / U3 ❌ (V5) | "Tenho uma empresa de contabilidade e clientes com precatório" → "Queremos conversar" → "Ana, da Contábil Sul" | Triagem → **Parceria Geral** (mesmo sem a palavra "indicar" e depois de `[BV]`/`[BV-B]`). Nunca Credor | Foi para o Credor; só com "indicaríamos clientes" chegou à Parceria Geral | Ajuste na Triagem aplicado em 26/11; **retestar** | |
| R6 | ⚠️ S1 (V5) | Comprador: "tem precatório alimentar Paraná" → "Tem" → "Precatório alimentar Paraná" → (Parceria Geral) → "Que número processo" | Fica na **Parceria Geral**; responde sem repetir `[X-REFAZ]`; encerra com `[FIM-INV]` | Parceria Geral passou para o Credor, que repetiu o `[X-REFAZ]`; depois voltou (pingue-pongue) | Prompt: comprador que fala de "processo" não é vendedor | |
| R7 | ⚠️ U2 (V5) | Conversa **nova**: "Quero vender o precatório do meu pai" | A 1ª resposta do Herdeiro já vem com "Olá! Eu sou a Luiza, responsável pelo primeiro contato aqui na Recall." | Saiu sem apresentação, mas a conversa tinha "Eu sou a Luiza" de testes anteriores | Confirmar em conversa limpa | |
| R8 | ⚠️ A6 (V3) | "Sou servidor público aposentado e tenho valores atrasados" | Só a próxima pergunta. Nada de "É comum ter essa dúvida!" (`[X-NAOSEI]` só se a pessoa disser que não sabe se tem) | Texto sem dúvida do cliente | Ajuste de 25/11; **retestar** | |
| R9 | ⚠️ A8 (V2) | "Não sei se tenho precatório" → "prefeitura" → "vocês me avisam se eu tiver?" | Não pergunta a esfera de novo; responde direto à pergunta final; não supõe advogado | Perguntas repetidas e dúvida sem resposta direta | Retestar na V5 | |
| R10 | ⚠️ N11 (V3) | Mensagens picotadas: "é" / "estadual" / "do paraná" | **Uma** resposta coerente, sem pergunta a mais | Duas respostas para três mensagens, uma com pergunta extra | Broker (N4, suporte) | |
| R11 | ⚠️ G1 (V3) | "Tenho um precatório federal, mas ainda vou pensar" → (contorno) → "Depois eu vejo" | Decidir se "depois eu vejo" logo após o contorno é "envia depois" (encerra com o Henzo) ou desistência | Virou `[DESIST]`; o lead sai do follow-up | **Decisão do usuário** | |
| R12 | ⚠️ H10/N5 | "Meu pedido de pizza já saiu?" no meio do Credor → "Ops, mensagem errada, é estadual" | `[ENGANO-Q]` do especialista; ao voltar ao tema, **retoma o roteiro** de onde parou | Só a 1ª metade foi validada; a 2ª mensagem não chegou ao Broker | Retestar | |

## 1.2 Ressalvas (⚠️) que viraram decisão ou observação

| ID | Origem | Cenário | Esperado | Ressalva | Res. |
|---|---|---|---|---|---|
| R13 | ⚠️ Q1a | "Vc ligou para minha mãe, dizendo que ela tem um precatório" | Triagem → Herdeiro **ou** pergunta se a mãe está viva (`[H-OBITO]`). Não pede documento antes | Foi para o Credor, que pediu o documento sem saber se a mãe vive. (No reteste, `[H-OBITO]` saiu ✅.) | |
| R14 | ⚠️ Q1b | Herdeiro: "O inventário foi concluído há anos" | Aceita como resposta; segue o roteiro | Pede o documento do processo em vez de ir direto ao número. **Decisão do usuário**: manter "documento primeiro" da V5? | |
| R15 | ⚠️ Q1f / Q4e | Pós-encerramento: "Sou advogado também" | `[X-ADV-Q]` ("Você gostaria de fazer uma parceria com a Recall?") ou `[X-INFO-NOVA]`; nunca `[X-OI]` ou `[X-OBRIG]` | Já ✅ no T1/U1; **registrar a informação** para o Henzo continua sem lugar definido (CRM desligado) | |
| R16 | ⚠️ B3 (V3) | `[BV]` → "do meu marido" → "ele está vivo" → "o CPF é o meu ou o do meu marido?" | Credor pede o CPF do **titular** e diz que é "do seu marido" (não "do titular") e não fala "o **seu** precatório" | Texto genérico demais | |
| R17 | ⚠️ T1 (resolvido em U1) | Credor completo → "Ok" → "Obrigado" | "Ok" sem resposta; 1º "Obrigado" → `[X-OBRIG]`; 2º sem resposta | Reconferir em outro agente (Herdeiro e Parceria Geral) | |
| R18 | ⚠️ Q3c | "Ainda n consegui ser atendida por vcs" | "Peço desculpas pela demora." + próxima pergunta (versão `-B`, não repete o texto anterior) | Abertura ficou seca; texto repetiu no 1º reteste | |

## 1.3 Nunca rodados

| ID | Origem | Cenário | Esperado | Res. |
|---|---|---|---|---|
| R19 | — E10 | A cadência do Broker manda a boas-vindas antiga ("Olá! Eu sou a Luiza, responsável pelo primeiro contato...") e o cliente responde "Olá, posso ter mais informações?" | Triagem não se apresenta de novo; vai ao `[BV-B]`/ao especialista direto | |
| R20 | — O5 | Histórico com mensagens do Henzo ou do assistente comercial + cliente manda "oi" | As mensagens da equipe **não** contam como encerramento; segue as regras normais | |
| R21 | — O8 | Repetir 5 cenários básicos na conexão 10 (uazapi) | Mesmo comportamento da conexão 5; anotar diferenças | |
| R22 | — A10 | Foto ou PDF de processo como 1ª mensagem | → Credor; `[X-FOTO]` ou próxima pergunta; sem pedir o documento de novo | |
| R23 | — M1 | Áudio claro: "tenho um precatório no meu nome" | Transcrição tratada como texto; → Credor | |
| R24 | — M2 | Áudio claro sobre herança | → Herdeiro | |
| R25 | — M3 | Áudio ininteligível | `[X-AUDIO]` | |
| R26 | — M4 | Segundo áudio ininteligível | `[FIM-HENZO]` **sem** transferir | |
| R27 | — M5 | Figurinha sozinha | Sem resposta, sem transferência | |
| R28 | — M6 | Figurinha + texto | Responde só ao texto | |
| R29 | — M7 | Foto de documento (RG/processo) | → Credor; `[X-FOTO]` | |
| R30 | — M8 | Vídeo ou localização | Resposta de catálogo, sem transferir | |
| R31 | — M9 | Áudio longo com várias informações (nome, esfera, processo) | Especialista não repete as perguntas já respondidas | |

---

# PARTE 2 — Cenários novos

IDs com prefixo de duas letras por grupo. Todos partem de **conversa limpa**, salvo quando indicado.

## CR. Credor — roteiro e variações (V5: documento → número/CPF → encerra)

| ID | Cliente diz | Esperado | Res. |
|---|---|---|---|
| CR01 | "Tenho um precatório do Estado de São Paulo, quero vender" | → Credor; apresentação + `[C-DOC]`. Não pergunta esfera. | |
| CR02 | "Tenho RPV da União" | → Credor; documento primeiro. | |
| CR03 | Manda só o número do processo (CNJ, 20 dígitos) | → Credor; não pede o número de novo; encerra ou pede só o que falta (CPF não é obrigatório). | |
| CR04 | Manda só o CPF | → Credor; não pede o CPF de novo; encerra. | |
| CR05 | CPF com 10 dígitos | `[C-CONF]` ("Acho que faltou algum número..."). Com o CPF certo, encerra. | |
| CR06 | CPF com 12 dígitos | `[C-CONF]`. | |
| CR07 | CPF escrito com pontos e traço | Aceita como CPF; encerra. | |
| CR08 | Número de processo com pontos e traços ("0001234-56.2015.8.26.0053") | Aceita; encerra. | |
| CR09 | "Não tenho nenhum documento" ao `[C-DOC]` | Segue para `[C-NUM]`, sem insistir no arquivo. | |
| CR10 | "Não tenho" ao `[C-NUM]` | Encerra com `[FIM-HENZO]` (sem insistir). | |
| CR11 | "Amanhã eu mando" ao `[C-NUM]` | Registra "envia depois" e encerra. | |
| CR12 | "Ok" ao pedido de CPF | Entende como "envia depois" e encerra. | |
| CR13 | "Depois eu vejo" ao pedido de documento | Registra "envia depois", vai ao `[C-NUM]`. Não é desistência. | |
| CR14 | "Por que precisam do meu CPF?" | `[X-CPF]` + repete o pedido (versão `-B`). Não diz "o Henzo responde". | |
| CR15 | "Isso é seguro?" ao pedir o CPF | `[X-CPF]` ou `[X-SEG]` + volta ao pedido. | |
| CR16 | "Onde acho o número do processo?" | `[X-ONDE]` + repete o pedido. | |
| CR17 | "Não sei qual é o número, o advogado que tem" | `[X-ONDE]`; segue para o CPF; encerra se "não tenho". | |
| CR18 | Foto do processo na resposta ao `[C-DOC]` | `[X-FOTO]` ou segue; vai ao número só se faltar; encerra sem pedir o que já está na foto. | |
| CR19 | PDF + texto "esse é o processo" | Idem CR18. | |
| CR20 | Dois precatórios no próprio nome ("tenho dois") | Usa `[C-DOC-2]` / `[C-NUM-2]`; encerra no fim. | |
| CR21 | Precatório em nome da empresa ("é da minha empresa") | `[C-NUM-CNPJ]` (CNPJ da empresa titular). | |
| CR22 | "Comprei esse precatório de outra pessoa" (cessão) | Permanece no Credor; `[C-NUM-CNPJ]`/`[C-NUM]` conforme o titular; não vai para a Parceria Geral. | |
| CR23 | Precatório da mãe **viva** ("estou ajudando ela a vender") | Credor; `[C-NUM-TIT]` (CPF do **titular**); diz "da sua mãe". | |
| CR24 | "Meu marido tem um precatório e ele quer vender" | Credor; pede o CPF do marido; não chama de "o seu precatório". | |
| CR25 | Responde ao `[C-DOC]` com "sim" | Entende como "tenho"? Não: pede o arquivo de novo na versão `-B` uma vez; depois segue para o número. | |
| CR26 | "Quero saber se meu precatório já foi pago" | `[X-ANDAMENTO]` + próxima pergunta. Não é desistência. | |
| CR27 | "Já recebi tudo, só quero conferir" | Pode ser `[JA-VENDEU]` ou `[X-ANDAMENTO]`; não pode transferir para humano nem insistir. | |
| CR28 | "Meu precatório é alimentar, de 2009" | → Credor; aceita a informação; documento primeiro. | |
| CR29 | "É do TJSP, ordem cronológica, número 1234" | Aceita como dado do processo; encerra ou pede o que falta. | |
| CR30 | "Quanto eu recebo se vender hoje?" | `[X-VALOR]` (depende da análise) + próxima pergunta. Nenhum número. | |
| CR31 | "Vocês pagam à vista?" | `[X-COMO]` + próxima pergunta. | |
| CR32 | "Quando eu recebo o dinheiro?" | `[X-PAGTO]` (7 dias úteis após a escritura) + próxima pergunta. | |
| CR33 | "Dá para eu calcular quanto vale?" | `[X-CALC]` + próxima pergunta. | |

## HE. Herdeiro

| ID | Cliente diz | Esperado | Res. |
|---|---|---|---|
| HE01 | "O precatório era do meu pai, ele faleceu" | → Herdeiro; pêsames permitidos? (conferir o prompt); `[H-INV]`. | |
| HE02 | `[BV]` → "da minha mãe" | → Herdeiro; `[H-OBITO]` (confirma se faleceu). | |
| HE03 | `[H-OBITO]` → "sim, faleceu em 2019" | `[H-INV]`. Não repete o falecimento. | |
| HE04 | "Inventário aberto" → `[H-DOC]` → "não tenho" | `[H-NUM]`. | |
| HE05 | "Inventário em andamento, tem advogado" | `[H-DOC]`; `[X-ONDE]` se pedir o número. | |
| HE06 | "Ainda não abrimos o inventário" | Segue para `[H-DOC]`, sem julgamento nem orientação jurídica. | |
| HE07 | "Não sei o que é inventário" | `[H-INV-B]` (explica e repete). | |
| HE08 | Manda o CPF do titular falecido | `[FIM-OK-HERD]` sem pedir o CPF de novo. | |
| HE09 | Manda só o CPF do herdeiro | Pede o do titular (`[H-NUM-B]`). | |
| HE10 | Manda o número do processo | Encerra sem pedir CPF. | |
| HE11 | "Somos 4 herdeiros" | Registra; `[X-PENSAR]` mostra "demais herdeiros" se hesitar; encerra normal. | |
| HE12 | "Só eu aceito vender, os outros não sabem" | Sem orientação jurídica; registra e segue. | |
| HE13 | "Tem dois precatórios do meu avô" | `[H-DOC-2]` / `[H-NUM-2]`. | |
| HE14 | "O titular morreu, mas não sei de quê era o precatório" | Segue; pede o CPF do titular (`[X-NAOSEI]`). | |
| HE15 | "Minha esposa faleceu e ela tinha precatório" | → Herdeiro (viúvo(a) é herdeiro). | |
| HE16 | "Meu filho faleceu" | → Herdeiro. | |
| HE17 | "Era do meu tio, ele não tinha filhos" | → Herdeiro. | |
| HE18 | "Estou cuidando do inventário como advogada" | Herdeiro continua o roteiro; `[X-ADV-Q]` só no fim ou ao dizer "advogada também". | |
| HE19 | "Vocês compram precatório de herança?" | `[X-COMPRA]` ("inclusive de herança") + próxima pergunta. | |
| HE20 | "O inventário vai demorar, dá para vender antes?" | Sem orientação jurídica; `[X-FORA]` ou `[X-ENTE]` + próxima pergunta. | |
| HE21 | Herdeiro com desistência: "não quero mais" | `[DESIST]`; não transfere. | |
| HE22 | Pós-encerramento: "o outro herdeiro também quer falar" | Orienta a falar por aqui (cada herdeiro pode falar com a equipe); sem transferir. | |

## AD. Parceria Adv. (V5: nome → honorários → encerra; sem OAB)

| ID | Cliente diz | Esperado | Res. |
|---|---|---|---|
| AD01 | "Sou advogado, tenho clientes com precatório" | → Parceria Adv.; apresentação + `[A1]`. | |
| AD02 | "Sou do escritório Silva & Silva" | → Parceria Adv.; `[A1]`. | |
| AD03 | Nome → `[A2]`/`[A2-HON]` → "sim, trabalho com honorários, uns 20 clientes" | `[FIM-HENZO]`. | |
| AD04 | "Trabalho com honorários" já na 1ª mensagem | Não pergunta honorários de novo; pede só o nome. | |
| AD05 | "Não trabalho com honorários" | Registra; encerra. | |
| AD06 | Resposta que não fala de honorários | Versão `-B` uma vez; depois registra "não informou" e encerra. | |
| AD07 | Cliente dá nome só com o primeiro nome | `[A1-B]` ("nome e sobrenome") uma vez; depois segue. | |
| AD08 | Cliente informa a OAB por conta própria | Registra; **não** pergunta OAB nem UF (V5 não pede). | |
| AD09 | "Vocês fazem parceria?" → "Sou advogada" | `[PARC-Q]` → Parceria Adv. | |
| AD10 | "Qual a porcentagem de honorários que vocês pagam?" | `[X-COND]` + próxima pergunta. Nenhum número. | |
| AD11 | "Posso mandar o processo de um cliente?" | `[X-MANDAR]` + próxima pergunta. | |
| AD12 | "Meu cliente quer vender, como funciona?" | → Parceria Adv.; `[X-ENTE]` ou `[X-MANDAR]` + próxima pergunta. | |
| AD13 | "Sou advogada e o precatório é meu" | → **Credor** (nunca parceria no 1º momento). | |
| AD14 | "Sou advogado, meu pai faleceu e deixou precatório" | → Herdeiro. | |
| AD15 | "Sou advogado de um fundo" | Conforme a frase: investidor → Parceria Geral; advogado de cliente → Parceria Adv. Sem loop. | |
| AD16 | "Sou advogada, vocês também compram precatório de cliente meu?" | Parceria Adv.; `[X-COMPRA]`/`[X-MANDAR]` + pergunta. | |
| AD17 | Advogado hesita: "preciso ver com o sócio" | `[X-PENSAR]` + pergunta; depois encerra com o que há. | |
| AD18 | Advogado: "não tenho interesse" | `[DESIST]`; sem transferência. | |
| AD19 | Pós-encerramento: "quando posso mandar os processos?" | `[X-MANDAR]`; sem pergunta de roteiro. | |
| AD20 | Pós-encerramento: "alguma novidade da parceria?" | `[X-COBRA-1]`; depois alterna (`-2`, `-3`...). | |
| AD21 | Pós-encerramento: "ok, aguardo" | Sem resposta (confirmação). | |
| AD22 | "Sou estagiário de um escritório" | → Parceria Adv.; segue; sem exigir OAB. | |
| AD23 | "Sou contador e advogado" | Roteia pela atividade citada; na dúvida, Parceria Adv.; sem loop entre agentes. | |
| AD24 | Advogado agressivo: "vocês são lentos" | Abertura "Peço desculpas pela demora." + pergunta que falta. | |

## PG. Parceria Geral (investidor, parceiro, outros)

| ID | Cliente diz | Esperado | Res. |
|---|---|---|---|
| PG01 | "Sou investidor e quero comprar precatórios" | → Parceria Geral; apresentação + `[I-REUNIAO]` + `[I1]`. | |
| PG02 | "Pessoa física, quero investir em precatório" | Roteiro investidor (não vai para o Credor); basta o nome. | |
| PG03 | "Represento um fundo" | `[I-REUNIAO]` + `[I1]`. | |
| PG04 | Investidor: "Carlos, Fundo Alfa" | `[I2]`. | |
| PG05 | `[I2]` → "Já investimos, federais, uns 2 milhões por mês" | `[FIM-INV]`. | |
| PG06 | `[I2]` → "Já investimos" (sem descrever) | `[I2-B]` uma vez; depois registra e `[FIM-INV]`. | |
| PG07 | `[I2]` → "ainda não sei, quero conversar" | `[I2-B]` uma vez; depois `[FIM-INV]`. | |
| PG08 | Investidor pergunta rentabilidade | `[X-COND]` + `[I2-B]`/próxima pergunta. Nenhum número. | |
| PG09 | Investidor pergunta o deságio | `[X-COND]`. | |
| PG10 | Investidor: "vocês têm precatório para vender?" | `[VC-INFO]`/explica que a Recall compra; segue o roteiro de investidor. | |
| PG11 | "Tenho uma contabilidade e clientes com precatório" | → Parceria Geral; `[INTUITO]` ou `[P1]`. | |
| PG12 | "Queremos indicar clientes" | Roteiro parceiro: `[P1]` → `[P2]`. | |
| PG13 | `[P2]` → "indicar, uns 40 clientes" | `[FIM-HENZO]`. | |
| PG14 | `[P2]` → "ainda não sei o formato" | `[P2-B]` uma vez; depois `[FIM-HENZO]`. | |
| PG15 | "Sou captador/correspondente" | Roteiro parceiro. | |
| PG16 | "Sou de uma empresa de cobrança" | Roteiro parceiro. | |
| PG17 | "Sou de um banco, queremos conversar" | `[INTUITO-B]` se vago; depois roteiro "outros" ou investidor. | |
| PG18 | "Quero oferecer um serviço de marketing para vocês" | Roteiro **outros**: `[O1]` → `[O2]` → `[FIM-HENZO]`. | |
| PG19 | "Procuro vaga de emprego" | Roteiro outros; sem prometer nada. | |
| PG20 | "Sou jornalista, quero uma entrevista" | Roteiro outros. | |
| PG21 | "Vocês fazem parceria?" (sem dizer quem é) | `[PARC-Q]`; espera; não encerra. | |
| PG22 | `[PARC-Q]` → "empresa de seguros" | Parceria Geral; `[INTUITO]`. | |
| PG23 | "Quero vender um precatório que comprei" | → **Credor**. | |
| PG24 | "Sou investidor e também tenho um precatório meu" | Termina o roteiro de investidor; **então** transfere ao Credor ("parceria já completa"); quem encerra é o Credor. | |
| PG25 | "Quero investir, mas não passo dados por telefone/aqui" | `[X-PENSAR]` ou `[X-CONFIANCA]` + próxima pergunta uma vez; depois `[DESIST]` se recusar. | |
| PG26 | "Vocês são de confiança?" | `[X-CONFIANCA]` (site) + próxima pergunta. | |
| PG27 | "Isso é golpe?" | `[X-CONFIANCA]`/`[X-SEG]` + próxima pergunta. | |
| PG28 | Investidor: "quanto vocês cobram?" | `[X-COND]`. | |
| PG29 | Investidor: "compram de qual estado?" | `[X-ENTE]` + próxima pergunta. | |
| PG30 | Pós-encerramento investidor: "quando é a reunião?" | `[X-COBRA-1]`... alternando; sem prazo inventado. | |
| PG31 | Pós-encerramento: "quais as condições?" | `[X-COND]` + um texto de cobrança. | |
| PG32 | Pós-encerramento: "quem é você, é robô?" | `[X-ROBO-POS]`. | |
| PG33 | Pós-encerramento: "mandei o CNPJ" | `[X-INFO-NOVA]`. | |

## TR. Triagem, anúncio e rotas

| ID | Cliente diz | Esperado | Res. |
|---|---|---|---|
| TR01 | "Olá! Posso ter mais informações sobre isso?" | `[BV]`. | |
| TR02 | "Oi" | `[BV]`. | |
| TR03 | "Bom dia" | `[BV]` (nunca `[VC-Q]`). | |
| TR04 | "Boa tarde, tudo bem?" | Cortesia curta + `[BV]`. | |
| TR05 | "Vim pelo Instagram" | `[BV]`. | |
| TR06 | Só um link | `[BV]`. | |
| TR07 | "teste" | `[BV]`. | |
| TR08 | "?" | `[BV]` ou `[BV-B]`; sem erro. | |
| TR09 | 👍 como 1ª mensagem | `[BV]`/`[BV-B]`; não ignora (não é figurinha). | |
| TR10 | `[BV]` → "quem é você?" | `[X-ROBO]` + `[BV-B]`. | |
| TR11 | `[BV]` → "como funciona?" | Resposta do catálogo + `[BV-B]`; sem pedir documento. | |
| TR12 | `[BV]` → `[BV-B]` → "sim" | → Credor, sem mensagem (rota padrão). | |
| TR13 | `[BV]` → "não sei" | `[BV-B]`; depois → Credor. | |
| TR14 | `[BV-B]` já enviado + "meu pai" | → Herdeiro (`[H-OBITO]` ou direto). | |
| TR15 | "Tenho um processo contra o INSS" | Conforme o prompt: precatório/RPV contra o governo → Credor; não é "engano". | |
| TR16 | "Ganhei na justiça e não recebi" | → Credor. | |
| TR17 | "Quero antecipar meu precatório" | → Credor. | |
| TR18 | "Sou servidor público e tenho retroativo" | → Credor. | |
| TR19 | "Tenho um precatório mas não é para vender, só consultar" | → Credor (consulta é Credor); sem tratar como desistência. | |
| TR20 | "Recebi uma ligação de vocês" | `[X-LIGAMOS]`-like no especialista: pede o nome do titular; nunca `[X-FORA]`. | |
| TR21 | "Recebi a indicação da Recall" / "me indicaram vocês" | Contato novo: `[BV]`; sem humano. | |
| TR22 | Mensagem em inglês | Responde em português com o catálogo. | |
| TR23 | Mensagem em espanhol | Idem. | |
| TR24 | Mensagem de 20 linhas com várias informações | Identifica a situação principal; na dúvida, Credor. | |
| TR25 | "Quero falar com o dono da empresa" | Trata como pedido de pessoa: `[FIM-ATEND]` + humano. | |
| TR26 | "Meu precatório é do INSS/aposentadoria" | → Credor; sem dar orientação jurídica. | |
| TR27 | "É do Banco do Brasil" (cobrança, não precatório) | `[ENGANO-Q]`/`[X-FORA]` conforme o prompt; sem transferir. | |
| TR28 | "Sou procurador do estado" | Roteia pelo que quer: assunto de trabalho → `[BV-B]`; sem Credor automático. | |
| TR29 | "Meu advogado mandou eu falar com vocês" | → `[BV-B]` ou Credor; não é Parceria Adv. | |
| TR30 | "Falei com um rapaz do telefone, o Henzo" | `[BV]`/`[BV-B]`; sem transferir (não pediu atendente). | |

## CO. Comprador e "vocês têm precatório?"

| ID | Cliente diz | Esperado | Res. |
|---|---|---|---|
| CO01 | "Vocês têm precatório?" | `[VC-Q]` (Triagem). | |
| CO02 | `[VC-Q]` → "Quero comprar" | → Parceria Geral (investidor). | |
| CO03 | `[VC-Q]` → "Vender" | → Credor. | |
| CO04 | `[VC-Q]` → "Tem" (vago) | `[VC-INFO]`. | |
| CO05 | `[VC-INFO]` → detalhes de compra | → Parceria Geral. | |
| CO06 | `[VC-INFO]` → sem resposta clara | → Credor. | |
| CO07 | "Quero comprar precatório" (direto) | → Parceria Geral na hora. | |
| CO08 | "Estou comprando, como vou te dar o número do processo?" (no Credor) | → Parceria Geral; sem `[X-FORA]` antes. | |
| CO09 | "Vocês vendem precatório?" | `[VC-Q]`. | |
| CO10 | "Preciso de precatório federal para compensar imposto" | → Parceria Geral (compra); `[I-REUNIAO]` + `[I1]`. | |
| CO11 | "Vocês compram precatório de qual estado?" | Credor: `[X-ENTE]` + documento (não é comprador). | |
| CO12 | Comprador reclama: "vc entendeu minha pergunta?" | Muda a abordagem (`[X-REFAZ]`/`[VC-INFO]`); nunca repete o texto. | |
| CO13 | Comprador na 3ª reclamação | Nunca o mesmo texto 3 vezes; encerra o laço com a pergunta que falta. | |

## LG. Retorno de ligação ou mensagem da Recall

| ID | Cliente diz | Esperado | Res. |
|---|---|---|---|
| LG01 | "Vocês me ligaram" | Especialista pede o nome do titular (`[X-LIGAMOS]`); nunca `[X-FORA]`. | |
| LG02 | → "Meu nome é Maria Souza" | `[X-LIGAMOS-NOME]` + próxima pergunta. | |
| LG03 | "Vc ligou para minha mãe" | Herdeiro ou `[H-OBITO]`; pede o nome dela. | |
| LG04 | "Se vocês ligaram, vocês têm meus dados" | `[X-LIGAMOS]`/`[X-LIGAMOS-NOME]`; nunca o texto genérico de dúvida. | |
| LG05 | Repete "vocês têm os dados" depois de encerrar | `[X-LIGAMOS-POS]`; não repete outro texto. | |
| LG06 | "Recebi uma mensagem de vocês no WhatsApp" | Qualifica como Credor; `[X-LIGAMOS]`. | |
| LG07 | "Fui contactada pela empresa sobre um precatório" | → Credor; não encerra; não diz "vou encaminhar" sem dados. | |
| LG08 | "Me ligaram ontem e disseram que eu tenho dinheiro a receber" | Credor; `[X-LIGAMOS]`. | |
| LG09 | "Atendi uma ligação mas não entendi" | Credor; pergunta o nome ou o documento. | |
| LG10 | "Quem ligou? Foi golpe?" | `[X-CONFIANCA]`/`[X-SEG]` + próxima pergunta. | |
| LG11 | "Já estou esperando faz dias" | "Peço desculpas pela demora." + pergunta; nunca só `[X-COBRA-1]` se ainda não há dados. | |
| LG12 | Retorno de ligação com titular **falecido** e nome completo | `[FIM-OK-HERD]` com o nome registrado; sem pedir CPF mais de uma vez. | |

## DU. Dúvidas durante o roteiro

| ID | Cliente diz | Esperado | Res. |
|---|---|---|---|
| DU01 | "Vocês são robô?" | `[X-ROBO]` + próxima pergunta. | |
| DU02 | "Tem taxa?" | `[X-SEG]` + próxima pergunta. | |
| DU03 | "Pedem senha do banco?" | `[X-SEG]` + volta ao pedido; **não encerra**. | |
| DU04 | "Preciso pagar advogado?" | Sem orientação jurídica; `[X-SEG]`/`[X-FORA]` + pergunta. | |
| DU05 | "Qual o endereço de vocês?" | `[X-FORA]`; sem inventar endereço. | |
| DU06 | "Tem CNPJ?" | `[X-FORA]`/`[X-CONFIANCA]`; sem inventar. | |
| DU07 | "Quem é o Henzo?" | `[X-FORA]`/texto do catálogo; sem inventar cargo detalhado. | |
| DU08 | "O Henzo é uma pessoa de verdade?" | `[X-ROBO]` (Henzo é pessoa da equipe). | |
| DU09 | "Quanto vocês pagam?" | `[X-VALOR]` + pergunta. | |
| DU10 | "Pagam 80%?" | `[X-VALOR]`; não confirma nem nega percentual. | |
| DU11 | "O que é deságio?" | Sem orientação; `[X-FORA]`/`[X-CALC]` + pergunta. | |
| DU12 | "Quando recebo?" | `[X-PAGTO]` + pergunta. | |
| DU13 | "Vocês compram de qualquer estado?" | `[X-ENTE]` + pergunta. | |
| DU14 | "Compram de município?" | `[X-ENTE]` + pergunta. | |
| DU15 | "Compram precatório alimentar?" | `[X-ENTE]`/`[X-COMPRA]` + pergunta. | |
| DU16 | "Vocês compram RPV?" | `[X-ENTE]`/`[X-COMPRA]`; sem prometer. | |
| DU17 | "Devo vender ou esperar?" | Sem orientação jurídica; `[X-FORA]`/`[X-PENSAR]` + pergunta. | |
| DU18 | "Meu precatório vale quanto?" | `[X-VALOR]`. | |
| DU19 | "Isso é do governo?" | Sem afirmar vínculo; `[X-FORA]`. | |
| DU20 | "É seguro enviar meu CPF?" | `[X-CPF]`. | |
| DU21 | "Vocês têm site?" | Site do `[X-CONFIANCA]` (https://recallprecatorios.com.br). | |
| DU22 | Duas dúvidas na mesma mensagem ("tem taxa e quanto demora?") | Junta as duas respostas numa mensagem + pergunta. | |
| DU23 | Dúvida sem resposta no catálogo, duas vezes | `[X-FORA]` depois `[X-FORA-B]`; nunca iguais seguidas. | |
| DU24 | Dúvida após o `[FIM-HENZO]` | Resposta do catálogo, **sem** pergunta de roteiro. | |
| DU25 | "Vocês trabalham aos sábados?" | Sem falar de horário (a Luiza atende sempre); `[X-FORA]` curto. | |
| DU26 | "Posso falar com o Henzo agora?" | Pedido de atendente: `[FIM-ATEND]` + humano. | |

## DE. Hesitação, desistência e "já vendi"

| ID | Cliente diz | Esperado | Res. |
|---|---|---|---|
| DE01 | "Vou pensar" | `[X-PENSAR]` + próxima pergunta (um contorno só). | |
| DE02 | "Não sei se vale a pena" | `[X-PENSAR]` + pergunta. | |
| DE03 | "Prefiro não passar dados agora" | `[X-PENSAR]`/`[X-CONFIANCA]` + pergunta; depois `[DESIST]` se repetir. | |
| DE04 | "Não confio" | `[X-CONFIANCA]` + pergunta. | |
| DE05 | Hesita → recusa de novo | `[DESIST]`. | |
| DE06 | "Não tenho interesse" | `[DESIST]`; sem transferir. | |
| DE07 | "Pare de me mandar mensagem" | `[DESIST]`. | |
| DE08 | "Não quero vender" | `[DESIST]`. | |
| DE09 | "Já vendi para outra empresa" | `[JA-VENDEU]`. | |
| DE10 | "Já recebi" | `[JA-VENDEU]`. | |
| DE11 | `[DESIST]` → "ok" | Sem resposta (V5) ou `[DESIST-OK]` uma vez (conferir o prompt). | |
| DE12 | `[DESIST]` → "já falei" | `[DESIST-OK]` uma vez; depois sem resposta. | |
| DE13 | `[DESIST]` → "mudei de ideia" | Retoma o roteiro de onde parou. | |
| DE14 | `[DESIST]` → "quero falar com atendente" | `[FIM-ATEND]` + humano (ver R1). | |
| DE15 | Desistência depois do `[FIM-HENZO]` | `[DESIST]`; Henzo não é avisado. | |
| DE16 | "Não, obrigado" ao pedido de CPF | Desistência (`[DESIST]`), não "envia depois". | |
| DE17 | "Depois mando" ao pedido de CPF | Envia depois; encerra; **não** é desistência. | |
| DE18 | "Não quero mais, mas me avise se mudar alguma coisa" | `[DESIST]`; sem promessa de aviso. | |

## EN. Engano, assunto sem relação e indicação

| ID | Cliente diz | Esperado | Res. |
|---|---|---|---|
| EN01 | "Quero retirar meu bolo" | `[ENGANO-Q]` (nunca `[X-FORA]`). | |
| EN02 | `[ENGANO-Q]` → "foi engano" | `[ENGANO]`. | |
| EN03 | `[ENGANO]` → insiste no assunto | `[ENGANO-B]`; depois alterna. | |
| EN04 | "Número errado" | `[ENGANO-Q]`. | |
| EN05 | "Ganhei um prêmio?" | `[ENGANO-Q]`. | |
| EN06 | Propaganda de outra empresa | `[ENGANO-Q]`. | |
| EN07 | Cobrança de outra empresa | `[ENGANO-Q]`. | |
| EN08 | `[ENGANO-Q]` → "na verdade tenho um precatório" | Segue as regras normais (Credor). | |
| EN09 | Assunto sem relação depois do encerramento | `[ENGANO-Q]`; sem transferir. | |
| EN10 | Assunto sem relação no meio do Herdeiro | `[ENGANO-Q]` do Herdeiro; retoma o roteiro ao voltar. | |
| EN11 | "Posso indicar um amigo?" | `[IND]` (+ próxima pergunta se o roteiro não terminou). | |
| EN12 | "Ainda posso indicar?" depois do encerramento | `[IND]`, sem transferir. | |
| EN13 | "Meu vizinho tem precatório, o telefone dele é..." | `[IND]` com "não precisa me mandar os dados"; não registra o telefone. | |
| EN14 | "Quero indicar e também tenho um meu" | `[IND]` + segue a rota do caso próprio (Credor). | |

## HU. Pedido de atendente humano

Todos dependem da correção do Broker (R1). Registre o resultado de cada um e também **se o cliente recebeu a frase padrão**.

| ID | Cliente diz | Esperado | Res. |
|---|---|---|---|
| HU01 | "Quero falar com um atendente" (conversa nova) | `[FIM-ATEND]` + humano. | |
| HU02 | "Quero falar com uma pessoa de verdade" | Idem. | |
| HU03 | "Me passa pro Henzo" | Idem. | |
| HU04 | "Pode me ligar?" / "me liga" | Idem. | |
| HU05 | "Prefiro por telefone" | Idem. | |
| HU06 | Pedido no meio do Credor | `[FIM-ATEND]` + humano; motivo no formato `Perfil: ... \| ...`. | |
| HU07 | Pedido no meio do Herdeiro | Idem. | |
| HU08 | Pedido na Parceria Adv. | Idem (era T4 ❌). | |
| HU09 | Pedido na Parceria Geral | Idem. | |
| HU10 | Pedido depois de `[FIM-HENZO]` | Idem (era J6 ❌). | |
| HU11 | Pedido depois de `[DESIST]` | Idem (era S9 ❌). | |
| HU12 | Pedido por áudio (transcrito) | Idem. | |
| HU13 | "Vocês só enrolam" (sem pedir humano) | **Não** transfere; resposta de catálogo. | |
| HU14 | "Quero reclamar" | Não transfere; abertura "Peço desculpas pela demora." + pergunta. | |
| HU15 | Dois pedidos seguidos de atendente | O 2º repete o `[FIM-ATEND]` (exceção permitida); sem frase padrão. | |

## PE. Depois do encerramento

Faça antes um Credor completo (documento → número → `[FIM-HENZO]`) e depois cada cenário abaixo.

| ID | Cliente diz | Esperado | Res. |
|---|---|---|---|
| PE01 | "Quando o Henzo vai me chamar?" | `[X-COBRA-1]` (sem prazo). | |
| PE02 | Repete a pergunta | `[X-COBRA-2]`, depois `-3`... sem repetir. | |
| PE03 | Repete 7 vezes | Recomeça no `[X-COBRA-1]` depois do último; nunca repete seguido. | |
| PE04 | "Alguma novidade?" | Texto de cobrança. | |
| PE05 | "Já faz 3 dias" | Texto de cobrança; sem desculpa inventada. | |
| PE06 | "Quanto tempo demora a proposta?" | `[X-PRAZO-PROPOSTA]`. | |
| PE07 | "Quando recebo a proposta?" | `[X-PRAZO-PROPOSTA]`. | |
| PE08 | "Ok" | Sem resposta. | |
| PE09 | "Certo" / "Beleza" / 👍 | Sem resposta. | |
| PE10 | 1º "Obrigado" | `[X-OBRIG]`. | |
| PE11 | 2º "Obrigado" | Sem resposta. | |
| PE12 | "Valeu" | Igual ao "obrigado". | |
| PE13 | "Oi" | `[X-OI]`. | |
| PE14 | "Feliz Natal!" | Cortesia sóbria curta; sem informação. | |
| PE15 | "Mandei o CPF" | `[X-INFO-NOVA]`. | |
| PE16 | Manda foto de documento | `[X-INFO-NOVA]`/`[X-FOTO]`. | |
| PE17 | Manda o número do processo | `[X-INFO-NOVA]`. | |
| PE18 | "Tenho outro precatório" | Novo atendimento: documento do novo caso; reaproveita CPF já enviado. | |
| PE19 | "Tenho outro precatório do meu pai (falecido)" | → Herdeiro. | |
| PE20 | "Vocês são robô?" | `[X-ROBO-POS]`; sem reapresentação. | |
| PE21 | "Tem taxa?" | `[X-SEG]`; sem pergunta de roteiro. | |
| PE22 | "Posso mandar mais documentos?" | `[X-INFO-NOVA]`-like; aceita. | |
| PE23 | Mensagem da equipe (pós-venda) no histórico + "oi" | Não é encerramento; regras normais. | |
| PE24 | Cliente volta 3 dias depois: "alguma novidade?" | Texto de cobrança; não recomeça. | |
| PE25 | "Sou advogada também" | `[X-ADV-Q]`/`[X-INFO-NOVA]`; nunca `[X-OI]`. | |

## RE. Repetição, "não entendeu" e robustez de diálogo

| ID | Cliente diz | Esperado | Res. |
|---|---|---|---|
| RE01 | "Vc entendeu minha pergunta?" | Releia e responda; ou `[X-REFAZ]`; nunca repete o texto anterior. | |
| RE02 | "Não foi isso que perguntei" | Idem. | |
| RE03 | 2ª reclamação seguida | Texto diferente do primeiro; sem loop. | |
| RE04 | "Já te falei isso" (repetiu um dado já enviado) | Reconhece pelo contexto; não pede de novo. | |
| RE05 | "Você não me escuta" | `[X-REFAZ]` ou próxima pergunta nova. | |
| RE06 | Cliente responde com outra pergunta ao pedido de CPF | Responde à dúvida e repete o pedido (`-B`). | |
| RE07 | Cliente responde com "?" ao pedido | Reformula (`-B`) uma vez. | |
| RE08 | Mesma pergunta 3 vezes ("quanto vocês pagam?") | Nunca 3× o mesmo texto; 2ª com variação ou `[X-ENTE]`. | |
| RE09 | Cliente envia "kkk" ou "rs" | Não trava; próxima pergunta. | |
| RE10 | Cliente envia uma frase sem sentido ("asdf") | `[X-REFAZ]` uma vez. | |

## MI. Mídia e formatos de mensagem

| ID | Cliente manda | Esperado | Res. |
|---|---|---|---|
| MI01 | Foto borrada do processo | `[X-FOTO]`; segue; pode pedir o número uma vez. | |
| MI02 | PDF grande | `[X-FOTO]`/segue. | |
| MI03 | Print de conversa com advogado | `[X-FOTO]`; segue para o número se faltar. | |
| MI04 | Áudio de 1 minuto contando a história | Transcrição vira texto; identifica o perfil. | |
| MI05 | Áudio com ruído, pela 1ª vez | `[X-AUDIO]`. | |
| MI06 | Contato compartilhado (cartão de contato) | Resposta de catálogo; sem registrar. | |
| MI07 | Localização | Idem. | |
| MI08 | Link para site | Resposta de catálogo (`[BV]`/`[BV-B]`). | |
| MI09 | Mensagem muito longa (texto de petição colado) | Identifica o caso; não repete tudo. | |
| MI10 | Texto em MAIÚSCULAS gritando | Resposta neutra; abertura de desculpa só se reclamar de demora. | |
| MI11 | Mensagem com erro de digitação ("presatorio do meu paii") | Entende a intenção e roteia. | |
| MI12 | Emoji de bravo 😡 | Resposta neutra; pergunta que falta. | |

## FO. Estilo, vazamento e formato

| ID | Verificação | Esperado | Res. |
|---|---|---|---|
| FO01 | Qualquer conversa completa | Nenhum `[XX]` no texto do cliente. | |
| FO02 | Qualquer transferência entre agentes | Nenhum JSON, nem "transferindo para...". | |
| FO03 | Pedido de documento | Sem "se puder", "quando puder", "se preferir". | |
| FO04 | Resposta a dúvida | Sem "Boa pergunta", "Poxa", "Que bom". | |
| FO05 | Cliente fala do falecimento | Sem pêsames além do que o prompt permite (V5: estilo profissional, sem "Sinto muito"). Conferir. | |
| FO06 | Resposta ao "tudo bem?" | Cortesia sóbria até 8 palavras. | |
| FO07 | Feminino | "Obrigada", "encaminhada" em todas as respostas. | |
| FO08 | Horário | Nunca fala de horário de atendimento. | |

## HI. Histórico, reinício e conexões

| ID | Situação | Esperado | Res. |
|---|---|---|---|
| HI01 | `#novo` | Ignora o histórico; `[BV]`. | |
| HI02 | Botão de limpeza de contexto | Próxima mensagem recebe `[BV]`. | |
| HI03 | Cliente de ontem manda "oi" depois do encerramento | `[X-OI]`. | |
| HI04 | Cliente de ontem: "tenho outro precatório" | Novo atendimento; sem reapresentar. | |
| HI05 | Histórico com `[FIM-HENZO]` de **outro agente** e cliente continua no especialista | Especialista **não** se reapresenta e **não** trata como encerrado. | |
| HI06 | Conversa transferida entre agentes tem `[DESIST]` antigo no histórico | O agente que recebe nunca considera encerrada; faz a próxima pergunta. | |
| HI07 | Mensagem do Henzo no histórico + cliente responde | Segue as regras normais. | |
| HI08 | Conexão 10 vs 5 | Mesmo resultado; anotar diferenças. | |

## PI. Mensagens picotadas e corridas

| ID | Mensagens (em sequência rápida) | Esperado | Res. |
|---|---|---|---|
| PI01 | "oi" / "tenho" / "um precatório" | Uma resposta coerente; sem duplicar apresentação. | |
| PI02 | "é" / "estadual" / "do paraná" | Uma resposta; sem pergunta extra (ver R10). | |
| PI03 | "meu pai" / "faleceu" / "ano passado" | Uma resposta: Herdeiro; `[H-INV]`. | |
| PI04 | "sou advogado" / "tenho cliente" / "com precatório" | Uma resposta: Parceria Adv.; `[A1]`. | |
| PI05 | CPF em 3 mensagens ("123" / "456" / "789 00") | Não encerra no 1º pedaço; junta e valida (`[C-CONF]` se faltar). | |
| PI06 | Mensagem + figurinha | Responde só ao texto. | |
| PI07 | Duas perguntas em duas mensagens seguidas | Uma resposta que cobre as duas. | |
| PI08 | Mensagem enviada enquanto a IA ainda responde | Não duplica texto; segue a conversa. | |
| PI09 | Mensagem após longo silêncio (6 h) | Continua de onde parou; sem reapresentação. | |
| PI10 | Mensagem logo após o `[FIM-HENZO]` ("espera, mais uma coisa") | `[X-INFO-NOVA]` ao receber a informação. | |

## MD. Dois perfis na mesma conversa

| ID | Situação | Esperado | Res. |
|---|---|---|---|
| MD01 | Investidor com precatório próprio | Roteiro de investidor completo → Credor ("parceria já completa") → Credor encerra. | |
| MD02 | Advogado com precatório próprio | Credor primeiro → Parceria Adv. → Parceria Adv. encerra. | |
| MD03 | Contador com precatório de herança | Parceria Geral completa → Herdeiro → Herdeiro encerra. | |
| MD04 | Cliente com próprio + do pai falecido | Credor completa o próprio → Herdeiro → Herdeiro encerra; sem devolver (R3). | |
| MD05 | Primeira frase é "quero vender" e depois diz que é investidor | Transfere logo para o Credor; sem roteiro de investidor. | |
| MD06 | Agente que recebe "caso já completo" | Nunca devolve; termina o roteiro dele. | |
| MD07 | Voltou ao primeiro agente por engano | Manda o `[FIM-HENZO]`, nunca só "Certo.". | |
| MD08 | Dois perfis + "quero falar com atendente" | `[FIM-ATEND]` + humano, imediatamente. | |

---

## Registro de execução

| Data | Versão / modelo | Conexão | Cenários rodados | ✅ | ⚠️ | ❌ | Observações |
|---|---|---|---|---|---|---|---|
|  |  |  |  |  |  |  |  |
