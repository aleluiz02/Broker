# Cenários de teste — Atendimento IA Recall (Luiza)

Roteiro para validar os agentes depois de cada mudança de prompt. Cada cenário traz o que o "cliente" manda e o que a IA deve fazer.

## Como rodar

0. Rode sempre no contato **Alexandre** (número de teste). Conversas de leads reais servem só de exemplo (grupo Q).
1. Use o botão de limpeza de contexto do Broker (ou "Devolver para IA" e depois `#novo`).
2. Mande as mensagens na ordem. Quando houver `→`, é a próxima mensagem do cliente.
3. Marque o resultado: ✅ passou · ❌ falhou · ⚠️ parcial. Anote a versão e o modelo testados.
4. Teste também com histórico antigo (grupo O), não só com conversa nova.

**Legenda do esperado**
- **Texto:** código do catálogo que deve sair (`[BV]`, `[X-FORA]` etc.). O código nunca aparece para o cliente.
- **Transfere → X:** transferência para o agente X, **sem mensagem**; quem recebe responde na hora com a próxima pergunta.
- **Humano:** só quando a pessoa pede atendente/humano/Henzo.
- **Sem transferência:** a conversa continua com a IA.

## Critérios que valem para todos os cenários

- [ ] Nunca vaza código `[XX]`, JSON, aspas, `”】【` ou nome de ferramenta.
- [ ] Toda mensagem recebe resposta (exceto figurinha e transferência entre agentes).
- [ ] "Eu sou a Luiza" aparece uma única vez na conversa inteira.
- [ ] Quem recebe uma transferência sempre faz a próxima pergunta (nunca encerra na 1ª resposta).
- [ ] Nenhum prazo, valor, percentual ou orientação jurídica.
- [ ] Não pergunta de novo o que já foi respondido (CPF, processo, esfera, cidade/estado).
- [ ] Transferência para humano sempre com texto `[FIM-HENZO]` na mesma resposta.
- [ ] Fala sempre no feminino.

---

## A. Credor (precatório no próprio nome)

| # | Cliente diz | Esperado |
|---|---|---|
| A1 | "Olá! Posso ter mais informações sobre isso?" → "Tenho um precatório no meu nome" | `[BV]`; depois transfere → Credor. Credor pergunta esfera. |
| A2 | "Quero vender meu precatório" (1ª mensagem) | Transfere → Credor direto, sem `[BV]`. Credor se apresenta e pergunta a esfera. |
| A3 | "Quanto vocês pagam?" | Transfere → Credor. Credor não informa valor nem percentual. |
| A4 | "Como funciona a antecipação?" | Transfere → Credor. |
| A5 | "Tenho RPV para receber da prefeitura" | Transfere → Credor (esfera municipal já informada: não perguntar de novo). |
| A6 | "Sou servidor público aposentado e tenho valores atrasados" | Transfere → Credor. |
| A7 | "Ganhei uma ação contra o Estado há anos, ainda não recebi" | Transfere → Credor. |
| A8 | "Não sei se tenho precatório, como descubro?" | Transfere → Credor. |
| A9 | "Quero saber como está meu precatório" | Transfere → Credor. |
| A10 | Cliente manda só um PDF ou foto do processo | Transfere → Credor (motivo: enviou documento sem texto). Credor pergunta o que falta. |
| A11 | Cliente manda só o CPF (11 dígitos) | Transfere → Credor. Credor não pede o CPF de novo. |
| A12 | "Sou advogada e tenho um precatório meu para vender" | Transfere → Credor (não é parceria). |
| A13 | "Tenho um precatório mas é do estado de SP, processo 0001234-56.2015.8.26.0053" | Transfere → Credor. Credor só pede o que falta (CPF). |
| A14 | "Oi" → (BV) → "sim, tenho" | Transfere → Credor. |

## B. Herdeiro (titular faleceu)

| # | Cliente diz | Esperado |
|---|---|---|
| B1 | "O precatório era do meu pai, ele faleceu" | Transfere → Herdeiro. Herdeiro pergunta sobre o titular/inventário. |
| B2 | (após `[BV]`) "da minha mãe" | Transfere → Herdeiro. |
| B3 | (após `[BV]`) "do meu marido" | Transfere → Herdeiro. |
| B4 | "Estamos fazendo o inventário do meu avô, ele tinha precatório" | Transfere → Herdeiro. Não repete a pergunta do inventário. |
| B5 | "Sou herdeira, tem espólio" | Transfere → Herdeiro. |
| B6 | "Meu tio morreu e deixou um processo contra a União" | Transfere → Herdeiro. Esfera federal já respondida. |
| B7 | Herdeira com CPF do titular e dela na mesma mensagem | Herdeiro reconhece os dois CPFs e pergunta só o que falta. |
| B8 | "Meu pai tinha precatório, mas ele ainda está vivo" | Transfere → **Credor** (não é herdeiro; titular vivo). |
| B9 | Herdeiro responde "não sei" ao inventário | Segue o roteiro, sem encerrar e sem transferir para humano. |

## C. Parceria — advogado(a)

| # | Cliente diz | Esperado |
|---|---|---|
| C1 | "Sou advogado, tenho clientes com precatório" | Transfere → Parceria Adv. Pede nome, OAB/UF, forma de trabalho. |
| C2 | "Sou do escritório X, queremos parceria de honorários" | Transfere → Parceria Adv. |
| C3 | "Vocês fazem parceria?" → "Sou advogada" | `[PARC-Q]`; depois transfere → Parceria Adv. |
| C4 | Advogado informa OAB/SP 123456 na primeira resposta | Parceria Adv não pergunta OAB nem UF de novo. |
| C5 | Advogado: "trabalho com cessão de honorários" | Parceria Adv registra a forma de trabalho e segue o roteiro. |
| C6 | "Sou advogada e meu cliente quer vender" | Transfere → Parceria Adv (fala de cliente). |
| C7 | "Sou advogada e o precatório é meu" | Transfere → Credor (caso do item A12). |

## D. Parceria — geral (empresa, fundo, investidor, captador)

| # | Cliente diz | Esperado |
|---|---|---|
| D1 | "Sou investidor, quero comprar precatórios" | Transfere → Parceria Geral. Pergunta intuito/roteiro de investidor. |
| D2 | "Represento um fundo de investimentos" | Transfere → Parceria Geral. |
| D3 | "Sou de uma gestora e queremos conversar" | Transfere → Parceria Geral. |
| D4 | "Tenho uma empresa de contabilidade e clientes com precatório" | Transfere → Parceria Geral (não é escritório de advocacia). |
| D5 | "Sou captador/correspondente, quero indicar casos" | Transfere → Parceria Geral. Roteiro de parceiro. |
| D6 | "Somos uma financeira e queremos comprar carteira" | Transfere → Parceria Geral. |
| D7 | "Vocês fazem parceria?" → "empresa de cobrança" | `[PARC-Q]`; depois transfere → Parceria Geral. |
| D8 | "Quero uma parceria" (sem dizer quem é) | `[PARC-Q]` e **espera**: não encerra, não transfere. |
| D9 | Parceria Geral recebe a conversa | Responde na hora (não transfere para humano nem encerra). Teste no modelo configurado no Broker. |

## E. Leads de anúncio e mensagens vagas (maioria dos contatos)

| # | Cliente diz | Esperado |
|---|---|---|
| E1 | "Olá! Posso ter mais informações sobre isso?" | `[BV]` (com apresentação + pergunta). |
| E2 | "Oi" | `[BV]`. |
| E3 | "Oi, tudo bem?" | Cortesia curta + `[BV]` ("Olá! Tudo ótimo por aqui, obrigada! Eu sou a Luiza..."). |
| E4 | "Bom dia" | `[BV]`. |
| E5 | "Vim pelo Instagram" | `[BV]`. |
| E6 | Envia só um link | `[BV]`. |
| E7 | "teste" | `[BV]`. |
| E8 | `[BV]` já enviado → cliente manda "?" ou "como assim" | `[BV-B]`. |
| E9 | `[BV]` e `[BV-B]` já enviados → cliente segue vago ("sim") | Transfere → Credor, sem mensagem. |
| E10 | A cadência do Broker já mandou a boas-vindas antiga (com "Eu sou a Luiza") → "Olá, posso ter mais informações?" | Vai direto ao `[BV-B]` (sem repetir apresentação). |
| E11 | Mensagem em inglês ou espanhol | Responde com o catálogo em português, sem inventar texto. |
| E12 | Só emojis (👍) | Resposta curta de catálogo (`[BV]` ou `[BV-B]`), sem transferir. |

## F. Dúvidas soltas

| # | Cliente diz | Esperado |
|---|---|---|
| F1 | "Vocês são robô?" / "Estou falando com uma máquina?" | `[X-ROBO]`, sem transferir. |
| F2 | "Tem que pagar alguma taxa?" | `[X-SEG]`. |
| F3 | "Vocês pedem senha ou dados do banco?" | `[X-SEG]`. |
| F4 | "É golpe?" | `[X-SEG]`/`[X-CONFIANCA]` conforme catálogo; sem transferir. |
| F5 | "Quanto tempo demora para receber?" (conversa nova) | `[X-PAGTO]` ("em até 7 dias úteis" depois da escritura, prazo confirmado pelo comercial) + próxima pergunta. Nenhum outro prazo. |
| F6 | Duas dúvidas fora do catálogo seguidas sobre precatórios | `[X-FORA]` na primeira, `[X-FORA-B]` na segunda (nunca duas iguais seguidas). |
| F7 | "Qual o endereço de vocês?" | Resposta do catálogo ou `[X-FORA]`; sem inventar endereço. |
| F8 | "Vocês compram precatório de São Paulo?" | `[X-ENTE]` (V3: "quem confirma é o Henzo") + próxima pergunta; não promete nada. |
| F9 | "Qual a porcentagem que vocês cobram?" | Não informa percentual; `[X-FORA]` + próxima pergunta. |

## G. Hesitação e desistência

| # | Cliente diz | Esperado |
|---|---|---|
| G1 | "Vou pensar" | UM contorno (`[X-PENSAR]` ou `[X-CONFIANCA]`) + roteiro segue. |
| G2 | "Não sei se vale a pena" | Idem G1. |
| G3 | "Depois eu vejo" → "não quero" | Contorno uma vez; na recusa mantida, `[DESIST]`. Nunca insiste duas vezes. |
| G4 | "Não tenho interesse" | `[DESIST]`, sem transferir (nem humano, nem agente). |
| G5 | "Pare de me mandar mensagem" | `[DESIST]`. |
| G6 | "Já vendi para outra empresa" | `[JA-VENDEU]`. |
| G7 | "Já recebi, o precatório foi pago" | `[JA-VENDEU]`. |
| G8 | Depois do `[DESIST]`: "ok" / "obrigado" | `[DESIST-OK]`. |
| G9 | Depois do `[DESIST]`: "mudei de ideia, quero vender" | Segue as regras normais (Credor). |
| G10 | Desistência no meio da qualificação do Herdeiro/Parceria | `[DESIST]` do próprio agente, sem transferir. |
| G11 | Desistência depois do encerramento (`[FIM-HENZO]`) | `[DESIST]`, sem avisar o Henzo. |

## H. Engano e assunto sem relação

| # | Cliente diz | Esperado |
|---|---|---|
| H1 | "Quero retirar meu bolo" | `[ENGANO-Q]` (nunca `[X-FORA]`). |
| H2 | → "Foi engano, desculpa" | `[ENGANO]`. |
| H3 | → insiste no assunto do bolo | `[ENGANO]`; depois `[ENGANO-B]`; depois alterna. |
| H4 | "Número errado, não sou essa pessoa" | `[ENGANO-Q]`. |
| H5 | "Ganhei um prêmio?" / sorteio | `[ENGANO-Q]`. |
| H6 | Propaganda de outra empresa | `[ENGANO-Q]`. |
| H7 | Cobrança de outra empresa | `[ENGANO-Q]`. |
| H8 | `[ENGANO-Q]` → "na verdade tenho um precatório" | Segue as regras normais (Credor). |
| H9 | Assunto sem relação **depois do encerramento** | `[ENGANO-Q]`, sem transferir. |
| H10 | Pedido de produto/entrega, no meio da qualificação do Credor | O especialista também aplica `[ENGANO-Q]` (regra vale em todos os agentes). |

## I. Indicação

| # | Cliente diz | Esperado |
|---|---|---|
| I1 | "Tenho um amigo que tem precatório" | `[IND]`. |
| I2 | "Posso indicar vocês?" | `[IND]`. |
| I3 | "Ainda posso indicar vocês?" (depois do encerramento) | `[IND]`, sem transferir. |
| I4 | "Quero indicar e também tenho um precatório meu" | `[IND]` e segue a rota do caso próprio (Credor). |
| I5 | "Recebi a indicação da Recall" / "o Fulano me passou o contato" | Contato novo: `[BV]` (nunca humano). |
| I6 | Indicação mandando os dados da pessoa indicada | `[IND]` ("não precisa me mandar os dados"); não registra os dados. |

## J. Pedido de atendente humano (único caso que transfere para humano)

| # | Cliente diz | Esperado |
|---|---|---|
| J1 | "Quero falar com um atendente" | `[FIM-HENZO]` + transfere para humano, **na mesma resposta**. |
| J2 | "Quero falar com uma pessoa de verdade" | Idem J1. |
| J3 | "Me passa pro Henzo" | Idem J1. |
| J4 | "Quero falar com um humano" no meio da qualificação (Credor/Herdeiro/Parceria) | O especialista escreve o texto e transfere, com motivo no formato `Perfil: ... \| Esfera: ... \| ...`. |
| J5 | Pede atendente e **não** responde mais nada | Henzo recebe o motivo preenchido; o cliente **não** recebe a mensagem padrão "Nossa equipe responderá assim que possível". |
| J6 | "Pode me ligar?" | Trata como pedido de atendente (J1). |
| J7 | Cliente irritado: "vocês só enrolam" (sem pedir humano) | Sem transferência; resposta de catálogo. |

## K. Roteiro completo e pós-encerramento

Faça primeiro um Credor completo (esfera, processo, CPF) até o `[FIM-HENZO]`. Depois, os testes abaixo, **sem transferir e sem recomeçar o roteiro**.

| # | Cliente diz | Esperado |
|---|---|---|
| K1 | "Quando o Henzo vai me chamar?" | `[X-COBRA-1]` (sem número de prazo; V3: textos de COBRANÇA). |
| K2 | Repete a pergunta | `[X-COBRA-2]`: nunca repete um texto de cobrança já enviado; pode abrir com uma frase curta de empatia. |
| K3 | "Alguma novidade?" | Próximo `[X-COBRA-n]`. |
| K4 | "Já faz 3 dias e ninguém me chamou" | Próximo `[X-COBRA-n]`, com empatia que reconheça os 3 dias; sem prazo nem promessa. Se faltou CPF/processo, junta uma vez `[X-COBRA-CPF]`/`[X-COBRA-PROC]`. |
| K4b | 6 cobranças seguidas | Seis textos diferentes; nenhum `[FIM-HENZO]` repetido. |
| K5 | "Quanto tempo demora a proposta?" | `[X-PRAZO-PROPOSTA]`. |
| K6 | "Obrigado" / "ok" / "valeu" / 👍 | `[X-OBRIG]` (sem reforçar que está com o Henzo). |
| K7 | "Oi" / "bom dia" | `[X-OI]`. |
| K8 | "Feliz Natal!" | Só a cortesia ("Feliz Natal para você também!"), sem nada além. |
| K9 | Manda novo CPF / foto / número de processo | `[X-INFO-NOVA]`. |
| K10 | "Vocês são robô?" | `[X-ROBO]`. |
| K11 | "Tem taxa?" | `[X-SEG]`. |
| K12 | Pergunta sobre precatório sem resposta no catálogo | `[X-FORA]` / `-B`. |
| K13 | "Tenho outro precatório, de outra pessoa" | Novo atendimento: Triagem classifica de novo (Credor/Herdeiro). |
| K14 | Mensagem de pós-venda/equipe no histórico + "oi" | Não conta como encerramento; trata pelas regras normais. |

## L. Qualificação dentro dos especialistas

### Credor (esfera, processo, CPF)

| # | Situação | Esperado |
|---|---|---|
| L1 | Responde "federal" | Pergunta o número do processo. |
| L2 | Responde "não sei a esfera" | Segue para o processo sem travar. |
| L3 | "Não tenho o número do processo agora" | Segue para o CPF. |
| L4 | Manda o CPF | Encerra com `[FIM-HENZO]`, sem transferir. |
| L5 | Ao pedido de CPF responde "ok" / "sim" / "amanhã" | Entende como "envia depois" e **encerra**, sem insistir. |
| L6 | Manda CPF, esfera e processo juntos | Reconhece tudo e encerra, sem perguntar de novo. |
| L7 | "Por que precisam do meu CPF?" | Responde com o catálogo (segurança/consulta do processo) e volta ao pedido, sem insistir duas vezes. |
| L8 | CPF com formato errado (10 dígitos) | Pede para conferir uma vez; não trava a conversa. |

### Herdeiro (titular, inventário, esfera, processo, CPF do herdeiro e do titular)

| # | Situação | Esperado |
|---|---|---|
| L9 | Responde sobre o titular e inventário em uma mensagem só | Não repete; pergunta esfera. |
| L10 | Manda só o CPF do herdeiro | Pede o CPF do titular. |
| L11 | Manda os dois CPFs | Encerra com o texto de herdeiro (`[FIM-OK-HERD]` / equivalente), sem transferir. |
| L12 | "Não sei o CPF do falecido" | Segue e encerra sem insistir. |
| L13 | Inventário ainda não aberto | Registra e segue o roteiro. |

### Parceria Adv. (nome, OAB/UF, forma de trabalho)

| # | Situação | Esperado |
|---|---|---|
| L14 | Manda nome + OAB/UF + forma de trabalho juntos | Encerra sem perguntar de novo. |
| L15 | Manda só o nome | Pede OAB/UF; depois a forma de trabalho. |
| L16 | Dá OAB sem UF | Pergunta só a UF. |

### Parceria Geral (intuito + roteiro: investidor / parceiro / outros)

| # | Situação | Esperado |
|---|---|---|
| L17 | Investidor | Roteiro de investidor. |
| L18 | Parceiro/captador | Roteiro de parceiro. |
| L19 | Intuito não claro | Roteiro "outros". |
| L20 | Parceria que, no meio, diz que **é pessoa física com precatório** | Corrige a rota → Credor, sem encerrar como parceria. |

## M. Áudio, figurinha e mídia

| # | Cliente manda | Esperado |
|---|---|---|
| M1 | Áudio claro: "tenho um precatório no meu nome" | Transcrição tratada como texto; transfere → Credor. |
| M2 | Áudio claro sobre herança | Transfere → Herdeiro. |
| M3 | Áudio ininteligível (transcrição vazia) | `[X-AUDIO]`. |
| M4 | 2º áudio ininteligível seguido | `[FIM-HENZO]` **sem** transferir. |
| M5 | Figurinha | Nenhuma resposta, sem transferência. |
| M6 | Figurinha + texto | Responde só ao texto. |
| M7 | Foto de documento (RG/processo) | Transfere → Credor; especialista reconhece o envio. |
| M8 | Vídeo ou localização | Resposta de catálogo (`[BV]`/`[BV-B]`); sem transferir. |
| M9 | Áudio longo com várias informações (esfera + processo + nome) | Especialista não repete as perguntas já respondidas. |

## N. Perfis misturados e casos limite

| # | Cliente diz | Esperado |
|---|---|---|
| N1 | "Tenho precatório do meu pai (falecido) e também um meu" | Prioriza o caso mais claro; decide uma rota e não se perde (na dúvida, Credor). |
| N2 | "Sou advogado, meu precatório próprio e dos meus clientes" | Credor primeiro (precatório próprio); não trata como parceria apenas. |
| N3 | "Sou investidor e quero vender um precatório que comprei" | Credor/Parceria Geral conforme a frase; confirmar que não cai em loop. |
| N4 | Mensagem enorme, confusa, com várias frases | Identifica a situação principal; na dúvida, Credor. |
| N5 | Cliente muda de assunto no meio ("e a vacina?") | `[ENGANO-Q]` do especialista; ao voltar ao tema, continua o roteiro. |
| N6 | Cliente responde ao `[BV]` com "quem é você?" | `[X-ROBO]` + `[BV-B]` na mesma mensagem. |
| N7 | Cliente agressivo / xinga | Resposta de catálogo neutra; não transfere; não discute. |
| N8 | Cliente pergunta "isso é do governo?" | `[X-FORA]` ou resposta de catálogo; não afirma vínculo com governo. |
| N9 | Pedido de "quanto vale meu precatório de R$ 80 mil?" | Não calcula nem opina sobre valor; segue o roteiro. |
| N10 | Pede orientação jurídica ("devo vender ou esperar?") | Não orienta; `[X-FORA]` + próxima pergunta. |
| N11 | Cliente manda mensagens picotadas ("oi" / "tenho" / "um precatório") em sequência | Responde de forma coerente, sem duplicar apresentação nem repetir `[BV]`. |
| N12 | Lead idoso, texto com erros de digitação ("presatorio", "presatório do meu paii") | Entende a intenção e roteia corretamente. |

## O. Histórico e reinício (conversas com passado)

| # | Situação | Esperado |
|---|---|---|
| O1 | Contato que já teve atendimento encerrado há dias e manda "oi" | `[X-OI]` (não recomeça o roteiro). |
| O2 | Mesmo contato diz "tenho outro precatório" | Novo atendimento pela Triagem; sem repetir "Eu sou a Luiza". |
| O3 | `#novo` | Ignora o histórico anterior; responde `[BV]` (única exceção para reapresentação). |
| O4 | Após `#novo`, mensagem normal de Credor | Fluxo normal do zero. |
| O5 | Número já atendido por humano (mensagens do Henzo/assistente no histórico) + "oi" | Não trata mensagens do humano como encerramento. |
| O6 | Histórico com `[FIM-HENZO]` de outro agente + cliente continua no especialista | O especialista **não** se reapresenta. |
| O7 | Botão de limpeza de contexto no Broker | Próxima mensagem recebe `[BV]` como contato novo. |
| O8 | Conversa na conexão 5 (Meta oficial) vs. 10 (uazapi) | Testar na conexão 10; anotar diferenças. |

## P. Regressão: falhas já vistas

Reteste obrigatório a cada versão.

| # | Falha de origem | Teste | Esperado |
|---|---|---|---|
| P1 | V1: encerrava na 1ª resposta | Transferência para qualquer especialista | Especialista sempre faz a próxima pergunta. |
| P2 | V1: Parceria Geral como rota padrão | "Posso ter mais informações?" | `[BV]` e depois Credor; nunca "você é investidor?". |
| P3 | V1: vazou `[POS-ENC]`, `”】【` e JSON | Qualquer conversa | Nenhum código ou JSON no texto ao cliente. |
| P4 | V1: CPF pedido de novo | Mandar o CPF antes de ser pedido | Não pede de novo. |
| P5 | V1: advogada com precatório próprio virou parceira | A12 / C7 | Credor. |
| P6 | V2 (24/11): Parceria Geral transferiu para humano ao receber a conversa | D9 | Responde na hora e faz pergunta; sem mensagem padrão do Broker. |
| P7 | V2 (24/11): transferência para humano sem texto | J1, J4 | Texto `[FIM-HENZO]` + ferramenta, na mesma resposta. |
| P8 | 24/11: Credor se apresentou de novo no meio da conversa | O6 | Sem segunda apresentação. |
| P9 | 24/11: "quero retirar meu bolo" recebeu `[X-FORA]` várias vezes | H1–H3 | `[ENGANO-Q]` → `[ENGANO]` → `[ENGANO-B]`. |
| P10 | 24/11: `[POS-ENC]` repetido | K1–K12 | Respostas variam conforme o catálogo, sem repetir o mesmo texto. |
| P11 | Desistência que acabava no Henzo | G4, G11 | Sem transferência; Henzo não é avisado. |
| P12 | Mensagem padrão "Nossa equipe responderá assim que possível." | J1, J5, M4 | Nunca aparece para o cliente. |

## Q. Conversas reais que deram errado (reproduzir no contato Alexandre)

Casos tirados do Inbox em 26/11/2026 (conexão 5, agentes V4/V5). Rode no contato "Alexandre", mandando as mensagens do cliente na ordem. Os dados pessoais foram trocados por `[...]`.

### Q1. Retorno de ligação da Recall, titular já falecido (caso "Delanne")

Sequência: "Bom dia" → (boas-vindas da cadência) → "Vc ligou para minha mãe dizendo que ela tem um precatório para receber." → "sim" (a mãe faleceu) → "já foi concluído há anos" (inventário) → "não" (sem arquivo) → "vc ligou para minha MÃE: [nome da mãe], se vc ligou, vcs têm os dados aí" → "não" (sem CPF nem processo) → "se vcs ligaram, vcs têm aí." → "Sou advogado também".

**O que aconteceu:** o Herdeiro pediu arquivo do processo mesmo com inventário concluído; usou `[X-FORA]` duas vezes para "vocês ligaram, vocês têm os dados"; ignorou o nome da mãe, que é um dado para a consulta; tratou o "não" ao pedido de CPF como fim do roteiro e encerrou; "sou advogado também" recebeu só `[X-INFO-NOVA]`.

| # | Esperado |
|---|---|
| Q1a | Triagem manda para o Herdeiro quando a pessoa confirma o falecimento (não pergunta "faleceu?" se ela já falou "minha mãe" e "ligou para ela" no passado). |
| Q1b | Inventário "concluído há anos" é resposta válida: segue para esfera/processo, sem pedir o arquivo como se fosse a próxima etapa. |
| Q1c | "Vocês ligaram, vocês têm os dados" recebe uma resposta direta: a equipe vai consultar com o nome informado. Não usa `[X-FORA]` para isso. |
| Q1d | O **nome completo do titular** vale como identificação: o agente pede só o que falta (CPF do titular ou processo), uma vez, e aceita seguir sem eles. |
| Q1e | "Não" ao pedido de CPF/processo = "não tenho agora": encerra com o resumo e o nome do titular no motivo. |
| Q1f | "Sou advogado também" depois do encerramento → `[X-INFO-NOVA]` aceitável, mas o motivo/Inbox deve registrar que é advogado. |
| Q1g | Nenhuma resposta repete a mesma frase duas vezes seguidas ("Quem responde isso é o Henzo" + "Essa dúvida o Henzo responde"). |

### Q2. Comprador procurando precatório (caso "Khaled")

Sequência: "Bom dia, tem precatório alimentar Paraná" → (boas-vindas da cadência) → "Tem" → "Precatório alimentar Paraná" → "Vc entendeu minha pergunta" → "Vc entende minha pergunta" → "Que número processo" → "Estou comprando, como vou te dar número de processo".

**O que aconteceu:** a Triagem mandou para o Credor/Venda. A IA pediu processo ou CPF **quatro vezes**, ignorou "vc entendeu minha pergunta" e só percebeu que era comprador quando ele disse "estou comprando". Aí passou para a Parceria Geral e ainda respondeu com `[X-FORA]` antes da pergunta.

| # | Esperado |
|---|---|
| Q2a | "Tem precatório alimentar do Paraná?" (pergunta, sem dizer que é dono) **não** vai direto para o Credor: a Triagem usa `[BV-B]` para descobrir se a pessoa quer vender ou comprar. |
| Q2b | Ao dizer "estou comprando", "procuro precatório para comprar", "quero comprar": transfere → Parceria Geral (investidor) na hora. |
| Q2c | Quando a pessoa reclama ("vc entendeu minha pergunta?"), o agente **muda de abordagem** e responde ao que ela perguntou, em vez de repetir o pedido. |
| Q2d | O pedido de processo/CPF **nunca** é feito mais de duas vezes. |
| Q2e | Quem recebe a transferência faz a pergunta do seu roteiro (nome, empresa/fundo) sem `[X-FORA]` antes. |
| Q2f | A resposta à pergunta "como vou te dar o número do processo?" deixa claro que **ele** é o comprador e que o roteiro de venda não se aplica. |

### Q3. Retorno de contato da empresa, sem nenhum dado (caso "Ana Paula")

Sequência: "Bom dia" → (boas-vindas da cadência) → "Fui contactada pela empresa sobre um precatório" → "Bom dia. Ainda n consegui ser atendida por vcs".

**O que aconteceu:** a Triagem respondeu `[FIM-HENZO]` ("vou encaminhar suas informações para o Henzo") sem ter **nenhuma informação** (nem nome, nem processo, nem CPF, nem esfera). Em seguida a cliente reclamou que não foi atendida e recebeu só o texto de prazo. A conversa ficou aberta na Triagem, sem humano e sem dado para o Henzo agir.

| # | Esperado |
|---|---|
| Q3a | "Fui contactada pela empresa" **não** encerra: o primeiro passo é saber se o precatório é dela (Credor) ou de um falecido (Herdeiro). Transfere ou pergunta com `[BV-B]`. |
| Q3b | A IA **nunca** diz "vou encaminhar suas informações" quando não tem nenhuma informação. |
| Q3c | "Ainda não consegui ser atendida" depois de cadência/IA sem resposta útil: reconhece a espera e **continua a qualificação** (pergunta o que falta). Se a pessoa pedir pessoa/atendente, transfere para humano. |
| Q3d | Depois de reclamar da demora, a resposta não é só `[X-PRAZO-CONTATO]`: precisa vir junto uma pergunta que avance o caso. |
| Q3e | Nenhum lead termina em `[FIM-HENZO]` com o motivo vazio ou genérico. O motivo traz pelo menos o perfil e o que a pessoa disse. |

### Q4. Padrões comuns aos três casos (testar em qualquer agente)

| # | Padrão | Teste | Esperado |
|---|---|---|---|
| Q4a | Resposta que não responde à pergunta | "Você entendeu minha pergunta?" | Responde à pergunta de fato; não repete o último texto. |
| Q4b | Loop de pedido | Cliente diz "não" a CPF, processo e arquivo | Para de pedir e fecha o caso com o que tem. |
| Q4c | `[X-FORA]` em assunto do próprio caso | "Se vocês ligaram, vocês têm meus dados" | Resposta específica ao caso; `[X-FORA]` só para dúvida geral sobre precatórios ou a Recall. |
| Q4d | Encerramento sem dado | Cliente só diz "fui contactada" | Nunca encerra sem ter feito ao menos a pergunta de perfil. |
| Q4e | Cliente que volta do outbound | "Vocês me ligaram" / "recebi mensagem de vocês" | Contato conhecido: qualifica como Credor/Herdeiro, sem tratar como lead frio nem prometer retorno. |
| Q4f | Comprador no lugar de vendedor | "Vocês têm precatório?" | Detecta o intuito de compra antes de pedir dados de vendedor. |

---

## Registro de execução

| Data | Versão / modelo | Conexão | Cenários rodados | ✅ | ❌ | Observações |
|---|---|---|---|---|---|---|
| 24/11/2026 | V2 / todos os agentes no gpt-6.1-terra | 5 (Meta oficial, contato Alexandre) | A, B, C, E, K completos; D1; F, G, H, I, J e N parciais (ver tabela) | 71 | 6 | ⚠️ 22 · — 1. Cada cenário conduzido até o encerramento antes de zerar o contexto. Interrompido às 18h; continua em 25/11. |

| 25/11/2026 | V2 / todos os agentes no gpt-6.1-terra (Parceria Geral corrigida no Broker) | 5 (Meta oficial, contato Alexandre) | Grupo D completo, L1, L9, L12, L17–L20, F2, F7, G2, G4–G7, H4–H7, H10/N5, I2, I5, J2, K4, N1, N3, N4, N6, N11, N12, O3, O4, O7 | 34 | 0 | ⚠️ 7. Não rodados: E10, O5, O8 (exigem escrita no Broker ou outra conexão), grupo M e A10 (mídia). |

| 25/11/2026 (tarde) | V3 / todos no gpt-6.1-terra | 5 (Meta oficial, contato Alexandre) | Reteste dos ❌ e ⚠️ de 24 e 25/11 | 25 | 3 | ⚠️ 4. Novos ❌: N1 e N2 (pingue-pongue entre agentes) e D4 (contabilidade foi para o Credor). |

| 26/11/2026 | V5 / Triagem, Credor e Herdeiro no gpt-6.1-sol; Parcerias no gpt-6.1-terra | 5 (Meta oficial, contato Alexandre) | Grupo Q (Q1–Q4) | 10 | 10 | ⚠️ 4 · — 1 (os ❌ incluem o "extra" do Q2). ❌: comprador vai para o Credor (Q2a, Q4f); "vocês ligaram, têm os dados" com texto genérico (Q1c, Q1d, Q4c); "vc entendeu?" sem mudar a abordagem (Q2c, Q4a); frase repetida (Q1g); abertura genérica na Parceria Geral (Q2e). Extra: `[FIM-HENZO]` sem dado no Q2. |

| 26/11/2026 (reteste) | V5 ajustada / todos no gpt-6.1-sol | 5 (Meta oficial, contato Alexandre) | Q1, Q2, Q3 + "vocês me ligaram", "Bom dia", "vocês compram do Paraná?", "vocês têm precatório?" | 4 | 0 | ⚠️ 3 (cada um com 1 ou 2 falhas pontuais). Resolvidos: comprador, "vc entendeu?" sem `[X-FORA]`, "vocês ligaram", Parceria Geral sem `[X-FORA]`, nada de `[FIM-HENZO]` sem dado. Ficaram: texto repetido, "sou advogado(a) também" pós-encerramento, "Bom dia" → `[VC-Q]` intermitente. |

| 26/11/2026 (tarde) | V5 com `[VC-INFO]` / todos no gpt-6.1-sol | 5 (Meta oficial, contato Alexandre) | S1–S9: Q1, Q2, Q3, Credor completo, lead de anúncio, advogado, investidor, "vocês têm precatório?", desistência + atendente | 5 | 1 | ⚠️ 3. ❌ S9: pedido de atendente depois de desistência no Credor saiu sem texto (mensagem padrão do Broker). ⚠️: pingue-pongue comprador (S1), "sou advogado também" → `[X-OI]` (S2), apresentação na 3ª mensagem (S6). |

| 26/11/2026 (tarde, 2) | V5 com regra do "ok" e da apresentação / todos no gpt-6.1-sol | 5 (Meta oficial, contato Alexandre) | T1 "ok"/"obrigado" no Credor, T2 advogado parceiro, T3 atendente no Credor sem desistência, T4 atendente na Parceria Adv. | 1 | 3 | ⚠️ 1. ❌ T3 e T4: o pedido de atendente transfere **sem o `[FIM-ATEND]`** no Credor e na Parceria Adv. (com e sem desistência). ⚠️ T1: o 1º "obrigado" ficou sem o `[X-OBRIG]`. |

**Pendente:**
- E10, O5 e O8 (ver "Não rodados em 25/11").
- Grupo M e A10: exigem mídia real (áudio, figurinha, foto, PDF, vídeo).

### Resultados — 24/11/2026 (V2, gpt-6.1-terra)

Método: cada cenário foi conduzido como conversa real pelo WhatsApp (respondendo a cada pergunta da Luiza até o encerramento). Só depois o contexto foi zerado e a Triagem V2 reatribuída (o "Zerar contexto da IA" não troca o agente).

| # | Resultado | O que aconteceu |
|---|---|---|
| A1 | ✅ | `[BV]` → Credor → esfera → processo → CPF → `[FIM-HENZO]`. |
| A2 | ✅ | Credor direto: apresentação + esfera; roteiro completo. |
| A3 | ✅ | Não informou valor nem percentual, nem pressionado ("uns 70%?"). Roteiro completo. |
| A4 | ⚠️ | Roteiro completo, mas não explicou o que é a antecipação: só apresentou o roteiro e pediu a esfera. A dúvida do cliente ficou sem resposta. |
| A5 | ✅ | "Prefeitura" → não perguntou a esfera; processo → CPF → `[FIM-HENZO]`. |
| A6 | ⚠️ | Roteiro completo, mas "Tudo bem, é comum ter essa dúvida!" saiu duas vezes seguidas sem o cliente ter perguntado nada. Numa ação coletiva de sindicato, disse "o seu advogado consegue te passar o número". |
| A7 | ✅ | "Contra o Estado" → pulou a esfera; respondeu dúvida no meio (`[X-PAGTO]`) e retomou o roteiro. |
| A8 | ⚠️ | Roteiro completo, mas após "não sei" + "prefeitura" perguntou a esfera de novo (ver L2); supôs que o lead tem advogado; "vocês me avisam se eu tiver?" sem resposta direta. |
| A9 | ❌ | "Não quero vender, só quero saber quando vou receber" → `[DESIST]` direto, ignorando a pergunta. Depois, "Quais dados?" → "Essa dúvida o Henzo responde", sem próxima pergunta. O lead que quer andamento fica sem caminho. |
| A10 | — | Não rodado (exige envio de PDF/foto). |
| A11 | ✅ | Só CPF → Credor; esfera → processo → encerrou sem pedir o CPF de novo. |
| A12 | ✅ | Advogada com precatório próprio → Credor. |
| A13 | ✅ | Esfera + processo na 1ª mensagem → pediu só o CPF → encerrou. |
| A14 | ✅ | "Oi" → `[BV]` → "sim, tenho" → Credor. |
| B1 | ✅ | Pêsames + inventário → esfera → processo → CPF do herdeiro e do titular → encerramento de herdeiro. |
| B2 | ✅ | `[BV]` → "da minha mãe" → Herdeiro (perguntou o inventário direto, sem confirmar se a mãe faleceu; os pêsames só vieram depois de "ela faleceu"). Roteiro completo. |
| B3 | ⚠️ | "do meu marido" → Herdeiro ✅; ao saber que o marido está vivo → corrigiu para Credor ✅. Mas pediu "o seu CPF" (de quem fala, não do titular) e, à pergunta "o meu ou o do meu marido?", respondeu "Essa dúvida o Henzo responde" e pediu "o seu CPF" de novo. Falta regra para precatório de terceiro vivo (representante). |
| B4 | ✅ | Inventário já citado → não perguntou de novo; pêsames + esfera → processo → encerrou. |
| B5 | ⚠️ | → Herdeiro, roteiro completo. Mas: perguntou se o inventário foi aberto apesar de "tem espólio"; sem pêsames; após "não sei" a esfera perguntou de novo ("contra o Estado, a Prefeitura ou a União?"), como no L2. |
| B6 | ✅ | → Herdeiro com pêsames; "União" → não perguntou a esfera; processo → CPF do herdeiro → pediu o do titular → "depois eu mando" → encerrou. |
| B8 | ⚠️ | "Meu pai tinha precatório, mas ele ainda está vivo" → a Triagem respondeu com o `[BV]` genérico, sem transferir. Só foi para o Credor depois de "ele quer vender, estou ajudando". Credor tratou como "o seu precatório" e pediu "o seu CPF" (mesmo problema do B3). |
| B9 | ✅ | (no B6) "não sei" ao inventário → seguiu o roteiro, sem encerrar nem transferir. |
| B7 | ✅ | (no B4) Processo + CPF do herdeiro + CPF do titular numa mensagem → reconheceu tudo e encerrou. |
| L12 | ⚠️ | "O da minha mãe eu não sei" → pediu o CPF do titular mais uma vez ("pode ser depois"); só encerrou quando o cliente repetiu que não sabe. Esperado: encerrar sem insistir. |
| L13 | ✅ | Inventário ainda não aberto → seguiu para a esfera. |
| C1 | ⚠️ | → Parceria Adv. ✅ (nome completo). Depois de "OAB 123456" pediu "o número da OAB e o estado" de novo (L16). Com a UF, encerrou sem perguntar a forma de trabalho: o prompt diz que "tenho clientes" já responde à [A3]. Correto pelo prompt; vale decidir se "clientes" basta para o Henzo. |
| C3 | ✅ | "Vocês fazem parceria?" → `[PARC-Q]` (com apresentação) → "Sou advogada" → Parceria Adv. → nome. |
| C4 | ✅ | (no C3) OAB/UF informada junto → não perguntou de novo. |
| C5 | ✅ | (no C3) "Cessão de honorários e clientes" → forma de trabalho registrada → encerrou. |
| C6 | ⚠️ | "Meu cliente quer vender" → Parceria Adv. ✅. Mas "quer que eu já mande o processo do meu cliente?" → "Essa dúvida o Henzo responde" (pergunta simples, cabia "pode mandar"). Encerrou após OAB/UF. |
| C7 | ✅ | "Sou advogada e o precatório é meu" → Credor; esfera → processo → CPF → `[FIM-HENZO]`. |
| L14 | ✅ | (no C3) Nome + OAB/UF + forma juntos → encerrou sem perguntar nada. |
| C2 | ✅ | "Parceria de honorários" → Parceria Adv.; nome → OAB/UF → encerrou (forma de trabalho já dita). |
| L15 | ✅ | (no C2) Só o nome → pediu OAB/UF. |
| L16 | ❌ | (no C1) OAB sem UF → repetiu a pergunta inteira (número + UF), não só a UF. |
| D1 | ❌ | → Parceria Geral ✅, mas: sem apresentação na 1ª mensagem; textos improvisados fora do catálogo ("Qual valor você pretende investir", explicação de rentabilidade, "Registrei seu interesse em investir cerca de R$ 2 milhões", perguntas sobre prazo e carteira); terminou sem `[FIM-HENZO]` e sem pedir nome/empresa. **Causa: configuração no Broker** (ver abaixo). |
| E1 | ✅ | `[BV]` com apresentação + pergunta. |
| E8 | ✅ | `[BV]` → "?" → `[BV-B]`. |
| E4 | ✅ | "Bom dia" → "Olá! Bom dia! Eu sou a Luiza..." + `[BV]`. Continuação: "tenho um precatório estadual" → Credor pulou a esfera e pediu o processo. |
| J4 | ✅ | (no E4) "Prefiro falar com um humano" no meio da qualificação → `[FIM-HENZO]` + transferência "Credor V2 → Atendente humano" na mesma resposta; sem a mensagem padrão do Broker (cobre P7/P12). O motivo não aparece na tela do chat (não conferido). |
| E5 | ✅ | "Vim pelo Instagram" → `[BV]`. |
| G1 | ⚠️ | (no E5) "Tenho um precatório federal, mas ainda vou pensar" → contorno ✅, mas a Triagem mandou o `[BV-B]` ("você tem um precatório no seu nome...?") em vez de transferir para o Credor; só transferiu depois de "já falei, tenho um no meu nome". |
| G3 | ⚠️ | (no E5) "Depois eu vejo" → seguiu o roteiro sem insistir ✅. Em seguida, "não quero" foi lido como "não quero mandar o processo": pediu o CPF. Só "não quero vender, já falei" gerou o `[DESIST]`. |
| E6 | ✅ | Só um link → `[BV]`. |
| I1 | ✅ | (no E6) "Tenho um amigo que tem precatório" → `[IND]`, sem transferir. |
| I4 | ✅ | (no E6) "Eu também tenho um precatório meu" → Credor; roteiro completo. |
| I6 | ⚠️ | (no E6) Mandou nome e telefone do indicado junto → ignorou os dados (bom), mas sem o lembrete "não precisa me mandar os dados". |
| E7 | ✅ | "teste" → `[BV]`. |
| E9 | ✅ | (no E7) `[BV]` → "?" → `[BV-B]` → "sim" → Credor sem mensagem de transferência. |
| F1 | ✅ | (no E7, dentro do Credor) "Estou falando com uma máquina?" → `[X-ROBO]` + próxima pergunta (esfera na versão -B). |
| F4 | ⚠️ | "Isso não é golpe?" → `[X-CONFIANCA]` (site + "nunca paga taxa"), mas **sem a próxima pergunta**. |
| F9 | ✅ | "Qual a porcentagem que vocês cobram?" → `[X-SEG]` (sem percentual) + próxima pergunta. |
| N8 | ✅ | "Vocês são do governo?" → `[X-FORA]` + próxima pergunta; não afirma vínculo. |
| N10 | ✅ | "Devo vender ou esperar?" → `[X-FORA-B]` + próxima pergunta; sem orientação. |
| N9 | ✅ | "R$ 80 mil, quanto pagariam?" → "O valor depende da análise..." + CPF; sem valor. |
| F3 | ⚠️ | "Vocês pedem senha ou dados do banco?" → `[X-SEG]` ✅, mas na mesma resposta **encerrou com `[FIM-HENZO]` sem o CPF** (já pedido duas vezes → "não informou"). O cliente estava se certificando antes de mandar. O CPF enviado logo depois virou `[X-INFO-NOVA]`. |
| E11 | ✅ | Inglês → `[BV]` em português. Espanhol ("tengo un precatorio federal a mi nombre") → Credor, pulou a esfera. |
| F5 | ⚠️ | "Quanto tempo demora para eu receber?" → `[X-PAGTO]` ("em até 7 dias úteis") + próxima pergunta. Informou prazo: conflito catálogo × "nenhum prazo". |
| J6 | ❌ | "Pode me ligar?" (depois do encerramento) → `[X-FORA]`; **não transferiu para humano**. Esperado: tratar como pedido de atendente. |
| E12 | ✅ | Só "👍" → `[BV]`. |
| H1 | ✅ | (no E12) "Quero meu bolo de chocolate" → `[ENGANO-Q]`. Obs.: a mensagem anterior, "Quero retirar meu bolo", saiu do WhatsApp às 17:49 e **não chegou ao Broker** (perdida no canal, não na IA). |
| H2 | ✅ | "Aqui não é a confeitaria?" → `[ENGANO]`. |
| H3 | ✅ | Insistiu no bolo → `[ENGANO-B]` → `[ENGANO]` (alterna; cobre P9). |
| H8 | ✅ | "Na verdade tenho um precatório do meu pai que faleceu" → Herdeiro com pêsames + inventário. |
| G10 | ✅ | (no H8) "Não quero mais, obrigada" no meio do Herdeiro → `[DESIST]`, sem transferir. |
| J1 (pós-desistência) | ❌ | Depois do `[DESIST]`, "Quero falar com um atendente" → `[DESIST-OK]`, **sem transferir**. A regra "Nunca transfira depois de uma desistência" está vencendo o FIM-PEDIDO. |
| J1 | ✅ | Conversa nova: "Quero falar com um atendente" → `[FIM-HENZO]` + transferência para humano na mesma resposta; sem mensagem padrão (cobre J5/P12). |
| J3 | ✅ | "Tenho um precatório municipal" → Credor (pulou a esfera) → "Me passa pro Henzo" → `[FIM-HENZO]` + transferência para humano. |
| J7 / N7 | ✅ | "Vocês só enrolam, que atendimento horrível" no Credor → "Sinto muito." + próxima pergunta; sem transferir, sem discutir. |
| H10 / N5 | ⚠️ | "Meu pedido de pizza já saiu?" no meio do Credor → `[ENGANO-Q]` do especialista ✅. A volta ao tema ("Ops, mensagem errada. É estadual") **não chegou ao Broker** (2ª mensagem perdida no canal hoje), então a retomada do roteiro não foi validada. |
| E3 | ✅ | "Oi, tudo bem?" → "Olá! Tudo ótimo por aqui, obrigada! Eu sou a Luiza..." + `[BV]`. Continuação como herdeira (mãe falecida, federal) → Herdeiro → inventário → processo + 2 CPFs → encerramento. Conversa usada para os testes K abaixo. |
| K1 | ✅ | "Quando o Henzo vai me chamar?" → `[X-PRAZO-CONTATO]`, sem número. |
| K2 | ✅ | Repetiu ("Mas quando? Hoje?") → `[X-PRAZO-CONTATO-B]`. |
| K3 | ✅ | "Alguma novidade?" → voltou para `[X-PRAZO-CONTATO]` (alternou). |
| K5 | ✅ | "Quanto tempo demora a proposta?" → `[X-PRAZO-PROPOSTA]` sem prazo. |
| K6 | ✅ | "Obrigado" → `[X-OBRIG]`, sem reforçar o Henzo. |
| K7 | ✅ | "Oi" → `[X-OI]`, sem recomeçar o roteiro (cobre O1). |
| K8 | ✅ | "Feliz Natal!" → só "Feliz Natal para você também!". |
| K9 | ✅ | Informação nova (nome da mãe) → `[X-INFO-NOVA]`. |
| K10 | ⚠️ | "Vocês são robô?" → `[X-ROBO]`, mas o texto diz "Depois destas perguntas..." com o roteiro já encerrado, e repete "Eu sou a Luiza" (2ª vez na conversa: o próprio catálogo traz a frase). |
| K11 | ✅ | "Tem taxa?" → `[X-SEG]`. |
| K12 / F6 | ✅ | Duas dúvidas fora do catálogo seguidas → `[X-FORA]` e depois `[X-FORA-B]`. |
| I3 | ✅ | "Ainda posso indicar vocês?" depois do encerramento → `[IND]`, sem transferir. |
| H9 | ✅ | "Quero retirar meu bolo" depois do encerramento → `[ENGANO-Q]`. |
| K13 | ⚠️ | "Foi engano. Mas tenho outro precatório no meu nome" → novo atendimento no Credor, sem se reapresentar ✅ (cobre O2, O6, P8). Mas pediu o CPF de novo, e a "é o mesmo que já mandei" respondeu pedindo o CPF outra vez: não aproveita dados do atendimento anterior. |
| G11 | ✅ | "Não tenho mais interesse" depois do encerramento → `[DESIST]`, sem transferir. |
| G8 | ✅ | "ok" depois do `[DESIST]` → `[DESIST-OK]`. |
| G9 | ✅ | "Mudei de ideia, quero vender" → "Que bom ter você de volta!" + encerrou (todos os dados já tinham sido enviados). |
| F8 | ⚠️ | "Compram de SP?" → "Compramos sim!" (`[X-COMPRA]`, está no catálogo). Conflito: este roteiro espera "não promete nada". |
| L2 | ❌ | "Não sei a esfera" → perguntou de novo com outras palavras ("contra o Estado, a Prefeitura ou a União?") em vez de seguir para o processo. |
| L3 | ✅ | Sem número do processo → seguiu para o CPF. |
| L5 | ✅ | "ok" e "amanhã eu mando" ao pedido de CPF → encerrou sem insistir. |
| L6 | ✅ | Esfera, processo e CPF juntos → encerrou sem perguntar de novo. |
| L7 | ⚠️ | "Por que precisam do CPF?" → a frase "Com ele conseguimos consultar o processo no tribunal" saiu duplicada na mesma mensagem. |
| L8 | ⚠️ | CPF com 10 dígitos → repetiu o pedido de CPF, mas não avisou que o número parece incompleto. |
| L10 | ✅ | Só o CPF do herdeiro → pediu o CPF do titular. |
| L11 | ✅ | CPF do titular → encerrou. |
| N2 | ⚠️ | Advogada citou clientes no meio do Credor → transferiu para Parceria Adv. antes de pedir o CPF do caso próprio. Parceria repetiu "A Recall trabalha com advogados parceiros" duas vezes. O caso próprio só foi retomado quando a cliente perguntou (o Credor retomou do CPF, sem repetir esfera/processo). |
| P4 | ✅ | Ver A11. |
| P5 | ✅ | Ver A12. |

**Problemas de configuração encontrados no Broker (24/11, só leitura, nada alterado):**
- **Recall | Parceria Geral V2:** o campo "Prompt do sistema" contém só o texto do papel (`V2_Transferencia.md`, 389 caracteres), não o prompt `V2 - 6.1 Terra.md`. O campo "Quando passar a conversa para este agente" ainda tem o papel da V1, com a frase "assunto que não se encaixa nos outros agentes". Resultado: a Parceria Geral responde sem catálogo nem roteiro (D1). O grupo D só pode ser validado depois de corrigir.
- **Linha `$R`** na linha 15/16 de todos os prompts V2 (Triagem, Credor, Herdeiro, Parceria Adv., Parceria Geral): parece um marcador de substituição esquecido. Conferir se foi colado assim no Broker.
- O agente "Recall | Instagram V1" aparece no Broker como "Recall | Redes V1" (2 vínculos).
- A ferramenta "Registrar Lead no CRM" está ativa e escrevendo: depois do D1, as Observações da oportunidade do contato ficaram "Investidor pretende aplicar cerca de R$ 2 milhões...". O CLAUDE.md diz que o CRM não é usado por enquanto.

**Conflitos entre catálogo e este roteiro** (a IA obedeceu ao prompt; decidir qual vale):
- `[X-PAGTO]` diz "em até 7 dias úteis", mas os critérios gerais dizem "nenhum prazo".
- `[X-COMPRA]` ("Compramos sim!") contra F8 ("não promete nada").

### Resultados — 25/11/2026 (V2, gpt-6.1-terra)

Antes de começar, conferi (só leitura) a Parceria Geral V2 no Broker. O "Prompt do sistema" agora é idêntico ao `V2 - 6.1 Terra.md`, inclusive com a linha `$R` solta, que também está no arquivo local. O campo de transferência tem um papel curto e excludente ("Empresa ou profissional que não é advogado(a)..."). Mesmo método de ontem.

| # | Resultado | O que aconteceu |
|---|---|---|
| D1 | ✅ | (reteste) "Sou investidor" → Parceria Geral com apresentação + `[I1]` → `[I2]` → deságio perguntado no meio: `[X-COND]` + `[I3]` → `[FIM-HENZO]`. Depois, "obrigado, em quanto tempo ele me chama?" → "Por nada!" + `[X-PRAZO-CONTATO]`. Cobre D9 e L17. |
| D9 | ✅ | Ver D1: respondeu na hora, sem encerrar nem transferir para humano. |
| L17 | ✅ | Ver D1: roteiro de investidor completo, em ordem. |
| D5 | ✅ | "Sou captador e correspondente, quero indicar casos" → apresentação + `[P1]`; comissão perguntada → `[X-COND]` + `[P3]` (pulou `[P2]`: "indicar casos" já responde) → `[FIM-HENZO]`. Cobre L18. |
| L18 | ✅ | Ver D5. |
| D4 | ⚠️ | Contabilidade com clientes → Parceria Geral ✅ → `[INTUITO]` → nome → `[P3]` → "não sei" aceito → `[FIM-HENZO]`. Mas **não se apresentou** (no D1, D2, D3, D5 e D6 se apresentou). "Vocês são de confiança?" → só `[X-FORA]`: o catálogo da Parceria Geral não tem resposta de confiança (o do Credor tem `[X-CONFIANCA]`). |
| D7 | ✅ | "Vocês fazem parceria?" → `[PARC-Q]` da Triagem com apresentação → "empresa de cobrança, passar clientes" → Parceria Geral `[P1]` → nome + "40 clientes" → `[FIM-HENZO]`. |
| D8 | ✅ | "Quero uma parceria" → `[PARC-Q]` e esperou (não encerrou, não transferiu). |
| L19 | ⚠️ | (no D8) "Represento uma empresa, ainda não sei o formato" → foi para o roteiro de parceiro (`[P1]`, `[P3]`) e **pulou o `[P2]`** ("como seria a parceria"), que era justamente o que faltava. Não usou o `[INTUITO]` nem o roteiro "outros". Encerrou com "empresa de software jurídico, pensamos em integrar". |
| D2 | ✅ | "Represento um fundo" → Parceria Geral com apresentação + `[I1]`. |
| L20 | ✅ | (no D2) "Fernando, do Fundo Horizonte. Mas também tenho um precatório federal no meu nome, quero vender" → Credor **sem mensagem e sem reapresentação**, pulou a esfera → processo → CPF → `[FIM-HENZO]`. Obs.: o interesse do fundo ficou sem roteiro (só no histórico). |
| D3 | ✅ | "Sou de uma gestora e queremos conversar" → roteiro de investidor completo; "Tanto faz. Posso falar direto com o Henzo?" → `[FIM-HENZO]` + transferência "Parceria Geral V2 → Atendente humano" na mesma resposta. |
| D6 | ✅ | "Financeira, comprar carteira" → `[I1]`; "vou pensar antes de passar dados" → `[X-PENSAR]` + `[I1-B]`; nome + experiência + esfera numa mensagem → `[FIM-HENZO]`. |
| F2 | ✅ | "Tem que pagar alguma taxa?" (depois do `[BV]`) → `[X-SEG]` + `[BV-B]`. |
| L1 | ✅ | (no F2) "Federal" → pediu o processo. |
| F7 | ✅ | (no F2) "Qual o endereço de vocês?" → `[X-FORA]` + processo na versão -B; não inventou endereço. |
| K4 | ✅ | (no F2, depois do encerramento) "Já faz 3 dias e ninguém me chamou" → `[X-PRAZO-CONTATO]`; repetiu → `[X-PRAZO-CONTATO-B]`. |
| N12 | ✅ | "boa tarde moça tenho um presatório do meu paii" → Herdeiro com apresentação + inventário. Obs.: pela regra da Triagem, "do meu pai" já é Herdeiro, então ela presumiu o falecimento sem confirmar (os pêsames só vieram depois de "ele faleceu"). |
| L9 | ✅ | (no N12) Titular + falecimento + "inventário ainda não abrimos" numa mensagem → pêsames + esfera, sem repetir nada. Depois: processo ("não tenho") → CPFs. |
| L12 | ⚠️ | (no N12, repetiu) "O meu é ..., o dele preciso procurar" → pediu o CPF do titular de novo ("pode ser depois"); só encerrou no "ok depois mando". |
| G2 | ✅ | "Não sei se vale a pena vender" no Credor → contorno + próxima pergunta (-B); depois "não quero mais vender" → `[DESIST]`; "ok obrigado" → `[DESIST-OK]`. Sem transferir. |
| G4 | ✅ | `[BV]` → "Ah, não tenho interesse" → `[DESIST]` na Triagem, sem transferir. |
| G5 | ✅ | "Pare de me mandar mensagem" no meio do Credor → `[DESIST]`. Depois, "mudei de ideia" → "Que bom ter você de volta! Vamos seguir de onde paramos." + processo (-B) → sem processo + CPF → `[FIM-HENZO]`. Obs.: na conversa do G4, a mesma frase depois do `[DESIST]` recebeu o `[DESIST]` de novo (texto repetido), não o `[DESIST-OK]`. |
| G6 | ✅ | "Já vendi para outra empresa" → `[JA-VENDEU]` na Triagem. |
| I2 | ✅ | (no G6) "Tenho uma amiga que também tem, posso indicar vocês?" → `[IND]`. |
| G7 | ✅ | "Já recebi, o precatório foi pago" no meio do Credor → `[JA-VENDEU]`. |
| H4 | ✅ | "Número errado, não sou essa pessoa" → `[ENGANO-Q]`; "foi engano sim" → `[ENGANO]`. |
| H5 | ✅ | "Ganhei um prêmio no sorteio" → `[ENGANO-Q]` → `[ENGANO]` → `[ENGANO-B]`. |
| H6 | ✅ | Propaganda de internet → `[ENGANO-Q]`; "QUERO" → `[ENGANO]`. |
| H7 | ✅ | Cobrança de fatura de outra empresa → `[ENGANO-Q]` → `[ENGANO]`. |
| H10 / N5 | ✅ | (reteste) "Meu pedido de pizza já saiu?" no meio do Credor → `[ENGANO-Q]` do especialista; "Ops, mensagem errada. É estadual" → retomou com o processo, sem repetir a esfera → "amanhã te mando o CPF" → `[FIM-HENZO]`. |
| I5 | ✅ | "O Joaquim me passou o contato de vocês" → `[BV]` (contato novo, sem humano). |
| J2 | ✅ | (no I5) Credor → "Quero falar com uma pessoa de verdade" → `[FIM-HENZO]` + transferência para humano. |
| N1 | ⚠️ | "Precatório do meu pai, que faleceu, e também um meu" → Credor (esfera) → "o meu é federal, o do meu pai é municipal" → **trocou para o Herdeiro no meio**, sem pêsames, antes de pedir o processo/CPF do caso próprio. No fim coletou tudo (processo próprio, CPF dele e do pai) e encerrou. Mas a "Do meu é 1009876-..." respondeu "Se você não tiver esses dados, o advogado... consegue te passar o número", ignorando o número recebido. |
| N3 | ⚠️ | "Sou investidor e quero vender um precatório que comprei" → Credor ✅, mas **sem apresentação**. "Está no nome da minha empresa, comprei por cessão" → Parceria Geral (roteiro de **investidor**: "Que ótimo! Qual é o seu nome e o nome... do fundo") → "Mas eu quero VENDER, não comprar" → `[FIM-HENZO]` só com nome e empresa, sem pedir o processo. Não houve loop. Falta rota para empresa/cessionário que quer vender. |
| N4 | ✅ | Mensagem longa e sem pontuação (prefeitura, sindicato, "é golpe?") → Credor com apresentação, pulou a esfera, respondeu a `[X-CONFIANCA]` + processo → sem número → CPF → `[FIM-HENZO]`. Obs.: a resposta veio em 4 mensagens separadas; e "Sem problema! ... Se preferir mandar depois, sem problema." repete a expressão. |
| N6 | ⚠️ | `[BV]` → "quem é você?" → só `[BV-B]`; **a pergunta ficou sem resposta** (a regra 8 da Triagem só cita "robô"). Depois, no Credor, "Não sei o número, amanhã mando o CPF" → pediu o CPF mesmo assim (não leu o "amanhã mando"); encerrou no "ok". |
| N11 | ⚠️ | "oi" / "tenho" / "um precatório" em rajada → um `[BV]` + a esfera do Credor, sem apresentação dupla ✅. Mas na 2ª rajada ("é" / "estadual" / "do paraná") o Broker respondeu só uma vez, com a esfera de novo (versão -B), ignorando o "estadual". |
| O3 | ✅ | `#novo` (sem zerar, depois de um encerramento, Triagem reatribuída) → `[BV]` com apresentação. |
| O4 | ✅ | (no O3) Credor do zero: esfera dita → processo → CPF → `[FIM-HENZO]`; não reaproveitou nada do atendimento anterior. |
| O7 | ✅ | Em todos os cenários de hoje, depois do "Zerar contexto da IA", a primeira mensagem foi tratada como contato novo. Exceções: D4 e N3 sem apresentação (ver acima). |

**Configuração V3 no Broker (25/11, 13h30, só leitura):** os 5 agentes estão como "Recall | ... V3", todos no gpt-6.1-terra, com prompts idênticos aos arquivos `V3 - 6.1 Terra.md`. O campo "Quando passar a conversa para este agente" **corta o texto em 480 caracteres**: o papel do Credor V3 perdeu "não sabe se tem precatório, enviou CPF, número de processo ou documento. É o destino padrão quando houver dúvida." e o da Parceria Geral V3 perdeu "tudo isso vai para o CREDOR.". Os testes da V3 ainda não foram rodados.

**Não rodados em 25/11:**
- **E10** (boas-vindas antiga da cadência): exige iniciar uma cadência no contato (ação de escrita no Broker).
- **O5** (histórico com mensagens do Henzo): exige mandar mensagem como atendente no Broker.
- **O8** (conexão 10 × 5): o contato de teste está na conexão 5 (`whatsapp_oficial`); não há como testar a 10 por este número.
- **Grupo M e A10**: exigem mídia real (áudio, figurinha, foto, PDF).

### Resultados — 25/11/2026, tarde (V3, gpt-6.1-terra): reteste dos ❌ e ⚠️

Configuração conferida (só leitura): os 5 agentes V3 com prompts idênticos aos arquivos; os papéis do Credor e da Parceria Geral já encurtados (475 e 448 caracteres) e completos no Broker. Antes de cada envio, o script confere que a conversa aberta no WhatsApp é a da Recall.

| # | Resultado | O que aconteceu |
|---|---|---|
| A9 | ✅ | (era ❌) "Quero saber como está meu precatório" → Credor explica que consulta o processo e pergunta a esfera; "não quero vender, só quero saber quando vou receber" **não** virou desistência: seguiu com o processo; "Quais dados?" → explicou (processo + CPF do titular) + pergunta; CPF → `[FIM-HENZO]`. |
| L2 | ✅ | (era ❌) "Não sei" à esfera → foi direto para o processo. |
| L7 | ✅ | (era ⚠️) "Por que precisam do meu CPF?" → explicação sem frase duplicada + pedido do CPF. |
| L8 | ✅ | (era ⚠️) CPF com 10 dígitos → "Acho que faltou algum número. Pode conferir?"; CPF correto → encerrou. |
| A6 | ⚠️ | "Sou servidor público aposentado e tenho valores atrasados" → Credor; mas ainda sai "É comum ter essa dúvida!" sem a pessoa ter perguntado nada. |
| J6 | ⚠️ | (era ❌) "Pode me ligar?" depois do encerramento → `[FIM-HENZO]` + transferência para humano ✅. Mas a mesma mensagem terminou com **"Nossa equipe responderá assim que possível."** (texto padrão do Broker, P12). |
| J1 pós-desistência | ⚠️ | (era ❌) `[DESIST]` → "Quero falar com um atendente" → `[FIM-HENZO]` + transferência ✅. Mas veio uma 2ª mensagem: **"Nossa equipe responderá assim que possível."** (P12). Na Triagem (J1 em conversa nova) não aparece: só nas transferências feitas pelo Credor V3. |
| J1 | ✅ | Conversa nova, Triagem: `[FIM-HENZO]` + transferência, sem texto padrão. |
| C1 | ✅ | (era ⚠️) Nome → OAB/UF → `[A3-HON]` (honorários + nº de clientes) → `[FIM-HENZO]`. |
| L16 | ✅ | (era ❌) "OAB 123456" → "E a sua OAB é de qual estado?" (só a UF). |
| C6 | ✅ | (era ⚠️) "Quer que eu já mande o processo de um cliente?" → "Pode mandar sim!" + próxima pergunta. |
| B8 | ✅ | (era ⚠️) "Meu pai tem um precatório, ele está vivo, estou ajudando" → Credor direto; pediu o **CPF do titular**. |
| B3 | ✅ | (era ⚠️) `[BV]` → "do meu marido" → Herdeiro pergunta se o titular faleceu → "está vivo" → Credor; pede o CPF do titular. Obs.: diz "compramos o **seu** precatório" e, a "o CPF é o meu ou o do meu marido?", respondeu "o do titular" sem dizer "do seu marido". |
| B5 | ✅ | (era ⚠️) "Sou herdeira, minha mãe faleceu e tem espólio aberto" → pêsames, **não** perguntou o inventário; "não sei" à esfera → seguiu. |
| L12 | ✅ | (era ⚠️) "O da minha mãe eu não sei" → encerrou sem insistir. |
| G1 | ✅ | (era ⚠️) "Tenho um precatório federal, mas ainda vou pensar" → Credor direto + `[X-PENSAR]` + processo. Em seguida, "Depois eu vejo" → `[DESIST]` (contorno já usado; não insistiu). |
| G3 | ✅ | (era ⚠️) "Depois eu vejo isso" → contorno + processo (-B); "não quero" → `[DESIST]`. |
| F8 | ✅ | (era ⚠️) "Vocês compram precatório de São Paulo?" → `[X-ENTE]` ("quem confirma é o Henzo") + processo. |
| F4 | ✅ | (era ⚠️) "Isso não é golpe?" → `[X-CONFIANCA]` + próxima pergunta. |
| F5 | ✅ | "Quanto tempo demora para receber?" → `[X-PAGTO]` (7 dias úteis após a escritura) + próxima pergunta. |
| F3 | ✅ | (era ⚠️) "Antes de mandar: vocês pedem senha ou dados do banco?" → `[X-SEG]` + pedido do CPF (não encerrou); CPF → `[FIM-HENZO]`. |
| K10 | ✅ | (era ⚠️) "Vocês são robô?" depois do encerramento → "Sou o atendimento virtual... O seu caso segue com o Henzo", sem "depois destas perguntas" e sem "Eu sou a Luiza". |
| K13 | ✅ | (era ⚠️) "Tenho outro precatório no meu nome, municipal" → processo → encerrou **sem pedir o CPF de novo**. |
| N6 | ✅ | (era ⚠️) `[BV]` → "quem é você?" → `[X-ROBO]` + `[BV-B]`. "Não sei o número, amanhã mando o CPF" → encerrou sem pedir o CPF. |
| N3 | ✅ | (era ⚠️) "Sou investidor e quero vender um precatório que comprei" → Credor; "no nome da minha empresa, por cessão" → ficou no Credor, processo → **pediu o CNPJ da empresa titular** → `[FIM-HENZO]`. |
| L19 | ✅ | (era ⚠️) "Represento uma empresa, ainda não sei o formato" → Parceria Geral: nome → `[P2]` → `[P3]` → `[FIM-HENZO]`. |
| I6 | ✅ | (era ⚠️) Indicação com nome e telefone da tia → `[IND]` com "não precisa me mandar os dados dela". |
| A4 | ✅ | (era ⚠️) "Como funciona a antecipação?" → explicação ("Nós compramos o seu precatório: você recebe agora, à vista...") + esfera; roteiro completo. |
| N11 | ⚠️ | "oi / tenho / um precatório" → um `[BV]` + esfera ✅. "é / estadual / do paraná" → duas respostas (esfera -B depois do "é", e o processo depois do "estadual"): coerente, mas com uma pergunta a mais. |
| N1 | ❌ | Credor fez o caso próprio (esfera, processo, CPF) e, sem `[FIM-HENZO]`, passou para o Herdeiro (o caso do pai) ✅. Herdeiro: inventário → pediu o CPF do titular → ao recebê-lo, **devolveu para o Credor**, que respondeu só **"Certo."** e parou. Só depois de "E agora?" a conversa voltou ao Herdeiro e encerrou. Pingue-pongue entre agentes. |
| N2 | ❌ | Advogada com precatório próprio + clientes → Credor fez o caso próprio e passou para a Parceria Adv. (sem `[FIM-HENZO]`); nome/OAB → honorários → ao responder, **voltou para o Credor**, que mandou "Certo. Que bom ter você de volta! Vamos seguir de onde paramos." e parou, sem pergunta e sem encerramento. |
| D4 | ❌ | (regressão) "Tenho uma empresa de contabilidade e clientes com precatório" → **Credor** (esfera), não Parceria Geral. "Não é meu, são dos clientes do meu escritório de contabilidade" → Credor perguntou a esfera de novo. Só com "Quero indicar meus clientes" foi para a Parceria Geral, que fez o roteiro e, a "Vocês são de confiança?", respondeu com o site ✅. |

**Causas prováveis (V3):**
- **N1 e N2:** a regra "DOIS PERFIS" está nos dois agentes. O segundo agente também vê os dois perfis e devolve a conversa ao primeiro, que já terminou o roteiro dele e não tem o que perguntar. Além disso, a regra 6 ("para outro AGENTE, não escreva nada") impede o `[FIM-HENZO]` na passagem. Sugestão: dizer ao agente que recebe que, se o motivo disser "caso ... já encaminhado", ele não devolve: termina o roteiro dele com o `[FIM-HENZO]`; e dizer ao Credor/Herdeiro que, se receber de volta um caso já encerrado, mande o `[FIM-HENZO]` (ou `[X-OBRIG]`) em vez de "Certo.".
- **D4:** a V3 restringiu a Parceria Geral a quem "quer COMPRAR, investir, indicar clientes ou fazer parceria" (Credor linha 35, Triagem linha 68, papel). "Tenho uma contabilidade e clientes com precatório" não diz "indicar". Sugestão: escritório que não é de advocacia (contabilidade, cobrança) que fala de **clientes** → PARCERIA GERAL, mesmo sem dizer "indicar"; e no Credor, "não é meu, é dos clientes" → PARCERIA GERAL.
- **J6/J1 pós-desistência:** quando o Credor V3 transfere para humano, o Broker acrescenta o texto padrão (a ferramenta provavelmente vai sem o campo de mensagem). Conferir nos eventos da conversa.

**Ajustes feitos na própria V3 (25/11, fim da tarde; falta colar no Broker e retestar N1, N2, D4 e A6):**
- DOIS PERFIS (Credor, Herdeiro, Parceria Adv., Parceria Geral): quem termina o 1º caso transfere sem `[FIM-HENZO]`, com motivo "... já completo"; quem recebe **nunca devolve** e encerra no fim do seu roteiro. Credor: se a conversa voltar com o roteiro já completo, manda o `[FIM-HENZO]`, nunca só "Certo." ou `[X-VOLTOU]`. Herdeiro: o CPF enviado no roteiro dele não é motivo para transferir.
- D4: Triagem (regra 4), Credor (O QUE VOCÊ NÃO ATENDE) e papel da Parceria Geral (462 caracteres) passam a mandar para a PARCERIA GERAL o escritório não jurídico ou a empresa que fala dos precatórios dos clientes, mesmo sem dizer "indicar".
- A6: `[X-NAOSEI]` só quando a pessoa diz ou pergunta que não sabe se tem precatório; quem só conta que tem retroativo ou atrasados recebe só a próxima pergunta.

Outras observações de 25/11:
- A ferramenta "Registrar Lead no CRM" continua gravando: depois do L20, as Observações da oportunidade ficaram "Fernando quer vender um precatório federal em seu próprio nome...".

### Resultados — 26/11/2026 (V5): grupo Q

Configuração no Broker: Triagem V5 **gpt-6.1-sol** (o arquivo local se chama "6.1 Terra"), Credor V5 e Herdeiro V5 no Sol, Parcerias V5 no Terra. Contato Alexandre (chat 1073, conexão 5). Contexto zerado e Triagem V5 atribuída antes de cada caso. A boas-vindas da cadência não foi disparada: no lugar dela, a Triagem respondeu o "Bom dia" com o `[BV]`.

**Q1 (Delanne).** Fluxo: Triagem → Credor (pediu o documento) → "Ela já faleceu" → Herdeiro: pêsames + inventário → "concluído há anos" → "Se tiver o arquivo..." → "não" → processo ou CPF do titular → "vc ligou para minha MÃE: [nome], vcs têm os dados" → "Essa dúvida o Henzo responde" + pede de novo → "não" → `[FIM-HENZO]` → "se vcs ligaram, vcs têm aí" → "Quem responde isso é o Henzo" → "Sou advogado também" → `[X-INFO-NOVA]`.

| # | Resultado | O que aconteceu |
|---|---|---|
| Q1a | ⚠️ | "Vc ligou para minha mãe..." foi para o **Credor**, que pediu o documento sem perguntar se a mãe está viva. O "sim" do roteiro não tinha a que responder; mandei "Ela já faleceu" e a conversa foi para o Herdeiro na hora. |
| Q1b | ⚠️ | "Concluído há anos" foi aceito (não perguntou de novo), mas a próxima pergunta é o arquivo do processo ("Se tiver..."). Isso segue a regra da V5/V4 (documento primeiro) e conflita com o esperado do Q1b. Decisão do usuário. |
| Q1c | ❌ | "Vocês ligaram, vocês têm os dados aí" → "Essa dúvida o nosso gerente comercial Henzo responde" (texto genérico de dúvida) + pedido de novo. |
| Q1d | ❌ | O nome completo da mãe foi ignorado; pediu o processo ou CPF do titular de novo (2ª vez). |
| Q1e | ✅ | "Não" ao CPF/processo → `[FIM-HENZO]`, sem insistir. Não há motivo para conferir: sem pedido de atendente não há transferência para humano (regra de 24/11). |
| Q1f | ⚠️ | "Sou advogado também" → `[X-INFO-NOVA]` ✅. Não fica registrado em lugar nenhum para o Henzo (não há motivo de transferência; o CRM está desligado). |
| Q1g | ❌ | Mesma família de frase duas vezes: "Essa dúvida o Henzo responde..." e, depois, "Quem responde isso é o Henzo...". |

**Q2 (Khaled).** Fluxo: "tem precatório alimentar Paraná" → Credor: "Quem confirma isso é o Henzo" + documento → "Tem" → **"Olá! Eu sou a Luiza..."** + documento (-B) → "Precatório alimentar Paraná" → **`[FIM-HENZO]` sem nenhum dado** → "Vc entendeu minha pergunta" → "Quem confirma isso é o Henzo" → "Vc entende minha pergunta" → "Essa dúvida o Henzo responde" → "Que número processo" → "costumam estar com o advogado..." → "Estou comprando..." → Parceria Geral → nome → investimento → `[FIM-INV]`.

| # | Resultado | O que aconteceu |
|---|---|---|
| Q2a | ❌ | A Triagem mandou direto para o Credor (sem `[BV-B]`). Causa provável: a Triagem V5 manda para o Credor quem "pergunta se compram de um estado ou cidade" (linha 36) e não tem regra para "vocês têm/vendem precatório X?". |
| Q2b | ✅ | "Estou comprando" → Parceria Geral na hora. |
| Q2c | ❌ | As duas reclamações ("vc entendeu minha pergunta?") receberam textos genéricos ("Quem confirma isso é o Henzo", "Essa dúvida o Henzo responde"). Não mudou de abordagem nem perguntou se ele quer vender ou comprar. |
| Q2d | ✅ | O documento foi pedido 2 vezes, e o processo/CPF nenhuma (o "Que número processo" recebeu a explicação de onde encontrar). |
| Q2e | ❌ | A Parceria Geral abriu com "Quem responde isso é o nosso gerente comercial Henzo" e só depois explicou a reunião e perguntou o nome (3 mensagens). |
| Q2f | ✅ | "Para compra de precatórios e investimentos, o atendimento é feito em uma reunião com o Henzo..." deixa claro que o roteiro é outro. |
| extra | ❌ | O Credor se apresentou ("Eu sou a Luiza") na **2ª** mensagem dele, e não na 1ª. E encerrou com `[FIM-HENZO]` ("Vou encaminhar suas informações") tendo só "alimentar Paraná": mesmo defeito do Q3b. |

**Q3 (Ana Paula).** Fluxo: "Bom dia" → `[BV]` → "Fui contactada pela empresa sobre um precatório" → Credor: documento → "Ainda n consegui ser atendida por vcs" → "Entendi." + documento (-B) → "Não tenho nada disso, só me ligaram" → processo ou CPF → CPF de teste → `[FIM-HENZO]`.

| # | Resultado | O que aconteceu |
|---|---|---|
| Q3a | ✅ | Não encerrou; foi para o Credor (regra da V5: "fui contactada pela empresa" → Credor). Não perguntou se o precatório é dela ou de falecido. |
| Q3b | ✅ | Neste caso, o `[FIM-HENZO]` só saiu depois do CPF. (No Q2 saiu sem dado: ver acima.) |
| Q3c | ✅ | Reconheceu com "Entendi." e continuou a qualificação. Poderia reconhecer a espera com mais empatia. |
| Q3d | ✅ | Veio junto a pergunta que avança o caso. |
| Q3e | — | Não se aplica: sem pedido de atendente, não há transferência nem motivo. |

**Q4 (padrões).**

| # | Resultado | Onde |
|---|---|---|
| Q4a | ❌ | Q2c. |
| Q4b | ✅ | Q1: parou de pedir depois do "não" e encerrou. |
| Q4c | ❌ | Q1c. |
| Q4d | ✅ | Q3a. Mas o Q2 encerrou sem dado (ver "extra"). |
| Q4e | ⚠️ | Q1 e Q3: qualificou como Credor/Herdeiro ✅; mas não reconhece que foi a Recall que ligou ("vocês têm os dados"). |
| Q4f | ❌ | Conversa nova, "Vocês têm precatório?" → Credor: "Sim, compramos." + documento. Não perguntou se ele quer vender ou comprar. |

**Causas prováveis e sugestões (V5):**
- **Comprador (Q2a, Q4f):** falta na Triagem uma regra "pergunta se a Recall TEM ou VENDE precatório ('tem precatório alimentar do Paraná?', 'vocês têm precatório?'), sem dizer que é dono → pergunta se quer vender ou comprar". No Credor e no Herdeiro: "a pessoa diz que está comprando ou pergunta se vocês vendem → PARCERIA GERAL".
- **"Vocês ligaram, vocês têm os dados" (Q1c, Q1d, Q4c, Q4e):** precisa de um texto próprio no Credor e no Herdeiro: o nome do titular fica registrado para a equipe localizar o contato, e a pessoa pode mandar o CPF ou o processo se tiver. Esse texto não pode ser o genérico de dúvida.
- **"Vc entendeu minha pergunta?" (Q2c):** regra comum: reclamação de não ser entendido → nunca repete o texto anterior; responde de outro jeito ou pergunta o que a pessoa quer (vender ou comprar).
- **`[FIM-HENZO]` sem dado (Q2):** só encerrar com o roteiro completo, ou com o "não tenho/envio depois" a um pedido de dado. Uma frase solta ("Precatório alimentar Paraná") não é resposta ao pedido de documento.
- **Abertura com texto genérico depois da transferência (Q2e):** a Parceria Geral tratou o "como vou te dar número de processo" como dúvida. Regra 3 (primeira resposta depois de transferência): sem texto de dúvida antes da pergunta do roteiro quando a mensagem só explica o perfil.
- **Apresentação na 2ª mensagem (Q2):** quando a Triagem transfere sem `[BV]`, o especialista precisa se apresentar na **primeira** resposta, não depois.

**Ajustes feitos na própria V5 (26/11; falta colar no Broker e retestar o grupo Q):**
- Triagem: nova regra 6 + `[VC-Q]`/`[VC-Q-B]` ("você tem um precatório para vender ou está procurando precatórios para comprar?") para quem pergunta se a Recall TEM ou VENDE precatório; "estou comprando" → Parceria Geral; "vocês ligaram para a minha mãe" segue a regra do familiar (Herdeiro); NÃO ENTENDEU + `[X-REFAZ]`.
- Credor e Herdeiro: CASOS ESPECIAIS (comprador → `[VC-Q-B]`, nunca `[X-COMPRA]`; NÃO ENTENDEU → `[X-REFAZ]`, nunca `[X-FORA]`/`[X-ENTE]`; "vocês ligaram, vocês têm os dados" → `[X-LIGAMOS]` / `[X-LIGAMOS-NOME]` / `[X-LIGAMOS-POS]`); o nome completo do titular vale como identificação; Credor: NUNCA ENCERRE SEM CASO; Herdeiro: inventário concluído é resposta válida (segue para o documento, como manda a V5); abertura "Peço desculpas pela demora." para reclamação de atendimento.
- Parceria Geral: SEM `[X-FORA]` NA CHEGADA (pergunta feita ao agente anterior só mostra o perfil) + exemplo do comprador vindo do Credor.
- Todos os especialistas: NÃO ENTENDEU + `[X-REFAZ]`; conferência final exige a apresentação já na primeira resposta.

### Resultados — 26/11/2026, reteste (V5 ajustada; todos os agentes no gpt-6.1-sol)

Broker conferido (só leitura): os 5 prompts iguais aos arquivos, já com as edições do usuário (sem frases de empatia). Casos críticos e confusos, contexto zerado e Triagem V5 atribuída antes de cada um.

| Caso | Resultado | O que aconteceu |
|---|---|---|
| Q2 completo | ⚠️ | "tem precatório alimentar Paraná" → `[VC-Q]` ✅. "Tem" → `[VC-Q-B]`. "Precatório alimentar Paraná" → Credor: `[X-REFAZ]` (sem `[FIM-HENZO]` ✅). "Vc entendeu minha pergunta" → `[VC-Q-B]` e "Vc entende minha pergunta" → **o mesmo `[VC-Q-B]` de novo** ❌ (3ª vez na conversa). "Que número processo" → `[X-ONDE]` + documento: tratou como vendedor sem ele ter dito ❌. "Estou comprando..." → Parceria Geral: `[I-REUNIAO]` + `[I1]`, **sem `[X-FORA]`** ✅. |
| "Bom dia" | ✅ / ⚠️ | Agora `[BV]`. Mas no reteste do Q1, minutos antes, o mesmo "Bom dia" recebeu o `[VC-Q]`: intermitente. |
| "Vocês compram precatório do Paraná?" | ✅ | Credor: `[X-ENTE]` + documento (vendedor, não comprador). |
| "Vocês têm precatório?" → "Quero comprar" | ✅ | `[VC-Q]` → Parceria Geral com o roteiro de investidor. |
| Q1 completo | ✅ / ⚠️ | "Vc ligou para minha mãe" → Herdeiro: `[H-OBITO]` ✅ → "sim" → pêsames + inventário ✅ → "concluído há anos" → documento (-B) → "não" → número/CPF → nome da mãe + "vcs têm os dados" → `[X-LIGAMOS-NOME]` + `[FIM-OK-HERD]` ✅ → "se vcs ligaram, vcs têm aí" → `[X-LIGAMOS-POS]` ✅. "Sou advogado também" → `[X-OBRIG]` ("Disponha!") ❌, deveria ser `[X-INFO-NOVA]`. |
| Q3 + "vocês me ligaram" | ✅ / ⚠️ | "Fui contactada" → Credor (documento). "Ainda n consegui ser atendida" → "Peço desculpas pela demora." ✅ + **o mesmo `[C-DOC]` de novo** ❌ (devia ser o `[C-DOC-B]`). "Vocês me ligaram, vocês têm meus dados" → `[X-LIGAMOS]` (pede o nome) ✅ → nome → `[X-LIGAMOS-NOME]` + `[C-NUM]` ✅ → "Não tenho" → `[FIM-HENZO]` ✅. "Sou advogada também" → `[X-OI]` ❌. |

**O que ainda falha e sugestão:**
- **Pergunta repetida (`[VC-Q-B]` e `[C-DOC]`):** a regra "nunca o mesmo texto duas vezes" não segurou. Sugestão: `[VC-Q-B]` no máximo uma vez por agente; na 2ª vez, um texto novo que responde de fato ("A Recall compra precatórios de quem tem um para vender. Se você procura precatórios para comprar, me diga que eu explico como funciona."). No exemplo do "ainda não fui atendida", escrever o `[C-DOC-B]` por extenso.
- **Comprador tratado como vendedor ("Que número processo"):** enquanto a pessoa não disse que tem precatório, nenhum pedido de documento/processo.
- **Informação nova depois do encerramento ("sou advogado(a) também"):** recebeu `[X-OBRIG]` e `[X-OI]`. Sugestão: listar "profissão, parentesco, outro herdeiro, 'sou advogado(a)'" como informação nova → `[X-INFO-NOVA]`, com exemplo.
- **"Bom dia" → `[VC-Q]` (intermitente):** dizer na regra 6 da Triagem que cumprimento sozinho nunca recebe `[VC-Q]`.

**Aplicado em 26/11 (pedido do usuário; falta colar no Broker e retestar o Q2):**
- Novo `[VC-INFO]` (Triagem, Credor, Herdeiro), texto do usuário: "A Recall compra precatórios de quem tem um para vender. Se você procura precatórios para comprar, me dê mais detalhes para eu encaminhar para nosso gerente comercial Henzo." O `[VC-Q]`/`[VC-Q-B]` sai uma vez na conversa; na insistência, `[VC-INFO]` (uma vez); depois, `[X-REFAZ]`. Na Triagem, detalhes de compra → Parceria Geral; sem resposta depois do `[VC-INFO]` → Credor.
- Credor ("ANTES DE TUDO") e Herdeiro: nenhum pedido de documento, processo, CPF ou inventário antes de a pessoa dizer que tem precatório.
- Triagem: cumprimento sozinho nunca recebe o `[VC-Q]`.

### Resultados — 26/11/2026, tarde (V5 com `[VC-INFO]`; todos no gpt-6.1-sol)

Broker conferido (só leitura) antes da rodada. Contexto zerado e Triagem V5 atribuída ao fim de cada cenário.

| Cenário | Resultado | O que aconteceu |
|---|---|---|
| S1 Q2 (comprador) | ⚠️ | `[VC-Q]` uma vez ✅ → "Tem" → `[VC-INFO]` ✅ → "Precatório alimentar Paraná" → Parceria Geral (`[I-REUNIAO]` + `[I1]`) ✅. "Vc entendeu minha pergunta" → `[X-REFAZ]`. "Que número processo" → **Parceria Geral passou para o Credor**, que mandou o **mesmo `[X-REFAZ]`**, e no turno seguinte a conversa voltou para a Parceria Geral (pingue-pongue) ❌. Encerrou com `[FIM-INV]` ✅. |
| S2 Credor completo | ⚠️ | `[BV]` → documento → número/CPF → CPF → `[FIM-HENZO]` ✅. "Sou advogado também" → `[X-OI]` ❌. "Alguma novidade?" → `[X-COBRA-1]` ✅. |
| S3 Q1 (Delanne) | ✅ | `[H-OBITO]` → inventário → documento → número/CPF → nome da mãe → `[X-LIGAMOS-NOME]` + `[FIM-OK-HERD]` → `[X-LIGAMOS-POS]`. |
| S4 Q3 (Ana Paula) | ✅ | Documento → "Peço desculpas pela demora." + `[C-DOC-B]` (não repetiu) → `[X-LIGAMOS]` → nome → `[X-LIGAMOS-NOME]` + `[C-NUM]` → "Não tenho" → `[FIM-HENZO]`. |
| S5 lead de anúncio | ✅ | `[BV]` → "Como funciona?" → `[X-COMO]` + `[VC-Q-B]` (sem pedir documento antes) → "tenho um do estado" → documento → "Depois eu vejo" → `[FIM-HENZO]`. |
| S6 advogado parceiro | ⚠️ | Nome/OAB → OAB/UF → honorários → `[FIM-HENZO]` ✅. Mas a apresentação ("Olá! Eu sou a Luiza...") saiu na **3ª** mensagem, no meio da conversa ❌. |
| S7 investidor | ✅ | Apresentação + `[I-REUNIAO]` + `[I1]` → `[I2]` → `[FIM-INV]`. |
| S8 "Vocês têm precatório?" | ✅ | `[VC-Q]` → "vc entendeu?" → `[VC-INFO]` → "que número processo" → `[X-REFAZ]`, sem pedir documento. |
| S9 desistência + atendente | ❌ | Credor: apresentação + documento → "Não tenho interesse" → `[DESIST]` ✅ → "Quero falar com um atendente" → transferiu para humano **sem o `[FIM-ATEND]`**: o cliente recebeu "Nossa equipe responderá assim que possível." (P12, igual ao J6/J1 da V3). O teste do mesmo pedido sem desistência não rodou (a aba do Broker mudou para Agentes IA). |

**Ajustes aplicados nos arquivos durante a rodada (falta colar no Broker):**
- Pedido do usuário: depois do encerramento ou da desistência, "ok", "certo", "beleza", "tá" ou emoji sozinhos ficam **sem resposta** (como a figurinha); "obrigado(a)" recebe o `[X-OBRIG]` uma vez só; o `[DESIST-OK]` sai uma vez só; profissão ou parentesco ("sou advogado também") é informação nova → `[X-INFO-NOVA]`, nunca `[X-OI]`/`[X-OBRIG]`. Nos 5 prompts.
- Especialistas: a apresentação só pode sair na **primeira** resposta depois de receber a conversa; depois disso, nunca (S6).

**Ainda abertos:** S9 (transferência para humano sem texto, só no Credor e depois de desistência até agora); pingue-pongue Parceria Geral ↔ Credor quando o comprador fala de "processo" (S1).

### Resultados — 26/11/2026, tarde 2 (V5 com a regra do "ok" e da apresentação; todos no gpt-6.1-sol)

Broker conferido (só leitura): os 5 prompts iguais aos arquivos. Contexto zerado e Triagem V5 atribuída antes de cada cenário.

| Cenário | Resultado | O que aconteceu |
|---|---|---|
| T1 Credor + "ok" | ⚠️ | Documento → número do processo → `[FIM-HENZO]` ✅. "Ok" → sem resposta ✅. 1º "Obrigado" → **sem resposta** ❌ (esperado `[X-OBRIG]` uma vez). 2º "Obrigado" → sem resposta ✅. "Sou advogado também" → `[X-INFO-NOVA]` ✅ (era ❌ no S2). |
| T2 advogado parceiro | ✅ | Nome/OAB → UF → honorários → versão -B de honorários (a resposta não falou de honorários) → `[FIM-HENZO]`. A apresentação **não** voltou no meio da conversa (era ❌ no S6). "Obrigado" → `[X-OBRIG]` ✅; "Ok" → sem resposta ✅. |
| T3 atendente no Credor, sem desistência | ❌ | Documento → "Quero falar com um atendente" → transferiu para humano **sem o `[FIM-ATEND]`**; o cliente recebeu "Nossa equipe responderá assim que possível.". Isola o S9: não depende da desistência. |
| T4 atendente na Parceria Adv. | ❌ | Nome/OAB → "Quero falar com um atendente" → transferiu para humano **sem nenhum texto** (nem a mensagem padrão: o cliente ficou sem resposta). |

**Diagnóstico:** com o gpt-6.1-sol, os agentes chamam a ferramenta de transferência para humano sem escrever o texto. No V2/V3 (Terra) o texto saía junto (J1, J4). A opção "Registrar Lead no CRM" não explica: está desligada no Credor e ligada na Parceria Adv., e os dois falharam.

**Ajustes aplicados nos arquivos (falta colar no Broker):**
- Os 5 prompts, parte 4, item 1: o pedido de atendente tem DUAS partes, nesta ordem: (a) o texto do `[FIM-ATEND]` escrito por extenso; (b) a ferramenta. "Nunca chame a ferramenta sem escrever esse texto antes".
- Os 5 prompts, parte 4, item 2: "ok obrigado" saiu da lista de confirmações; "obrigado", "obrigada" e "valeu" NÃO são confirmação: o primeiro depois do encerramento recebe o `[X-OBRIG]`.

**Se o ajuste não resolver:** testar o pedido de atendente com um agente no gpt-6.1-terra (só com a confirmação do usuário para trocar o modelo).

### Resultados — 26/11/2026, tarde 3 (V5 com perguntas diretas, sem OAB e com o `[X-ADV-Q]`; todos no gpt-6.1-sol)

Broker conferido (só leitura): os 5 prompts iguais aos arquivos. Contexto zerado e Triagem V5 atribuída antes de cada cenário.

| Cenário | Resultado | O que aconteceu |
|---|---|---|
| U1 Credor + advogado | ⚠️ | Apresentação + `[C-DOC]` direto (sem "Com ele a nossa equipe...") → número → `[FIM-HENZO]` ✅. "Obrigado" → `[X-OBRIG]` ✅ (era ❌ no T1). "Ok" → sem resposta ✅. "Sou advogado também" → `[X-ADV-Q]` ✅ → "Sim, tenho uns clientes" → Parceria Adv. → "Qual é o seu nome completo?" (sem OAB) ✅ → `[A2-HON]` → `[FIM-HENZO]` ✅. "Quero falar com um atendente" → **sem o `[FIM-ATEND]`**: o cliente recebeu "Nossa equipe responderá assim que possível." ❌ (a instrução nova no item 1 não resolveu). |
| U2 Herdeiro + advogada | ⚠️ | "Quero vender o precatório do meu pai" → "A pessoa que era titular do precatório faleceu?" (direta) ✅ → `[H-INV]` → `[H-DOC]` → `[H-NUM]` (sem "para a nossa equipe consultar") → `[FIM-OK-HERD]` ✅. "Sou advogada também" → `[X-ADV-Q]` ✅ → "Não" → sem resposta ✅. A primeira resposta saiu **sem a apresentação** (a conversa de teste já tinha "Eu sou a Luiza" de cenários anteriores; verificar numa conversa nova). |
| U3 contabilidade | ❌ | Triagem: "Trabalho num escritório de contabilidade" → `[BV]`; "Queremos conversar" → `[BV-B]` (direta, sem "Para eu direcionar") ✅; "Ana, da Contábil Sul" → **CREDOR** ❌ (regra "depois do [BV-B] → CREDOR") → `[VC-Q-B]` → "Ainda não sei" → `[VC-INFO]` → "Indicaríamos clientes, uns 40" → Parceria Geral → `[FIM-HENZO]` (roteiro já completo). |

**Ajuste aplicado na Triagem (falta colar):** quem diz que trabalha ou fala por escritório de contabilidade ou cobrança, banco, fundo, gestora ou financeira vai para a PARCERIA GERAL mesmo sem dizer o que quer, inclusive depois do `[BV]`/`[BV-B]` (nunca para o CREDOR).

**Aberto:** o `[FIM-ATEND]` continua sem sair no Sol (T3, T4, U1). Próximo passo proposto: testar um agente no gpt-6.1-terra (precisa da confirmação do usuário para trocar o modelo).

### Teste de modelo — 26/11/2026 (Parceria Adv. no gpt-6.1-terra, trocado pelo usuário)

| Cenário | Resultado | O que aconteceu |
|---|---|---|
| U4 atendente no Terra | ❌ | Advogado → apresentação + "Qual é o seu nome completo?" ✅ → "Quero falar com um atendente" → transferiu para humano e o cliente recebeu só "Nossa equipe responderá assim que possível.", sem o `[FIM-ATEND]`. |

**Conclusão:** a falha acontece no Sol e no Terra, com o mesmo prompt. Não é o modelo. Na V3 (J6, J1 pós-desistência), o texto da IA saía e o Broker acrescentava a mensagem padrão; agora sai só a mensagem padrão (ou nada, no T4). O Broker parece descartar o texto da IA quando há transferência para humano. Levar ao suporte do Broker: na transferência para humano, enviar o texto escrito pela IA (ou permitir configurar a mensagem padrão).
