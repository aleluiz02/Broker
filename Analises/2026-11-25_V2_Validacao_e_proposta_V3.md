# V2: validação dos testes e proposta para a V3

Base: `Testes/Roteiro_1_V2aV5.md`, com os testes de 24/11 e 25/11/2026 (V2, todos os agentes no gpt-6.1-terra, conexão 5). Comparei cada resultado ⚠️ e ❌ com o texto atual dos prompts.

## 1. Validação dos resultados

Resumo: dos 34 resultados ⚠️ e ❌, **22 são lacunas ou regras erradas no prompt** (corrigíveis), **5 são falhas do modelo**, mesmo com a regra escrita (dá para reforçar), **4 são expectativas do roteiro de testes que contrariam o prompt** (precisam de decisão sua) e **3 são da plataforma**.

### 1.1 Lacuna ou regra errada no prompt (corrigir na V3)

| Teste | Causa no prompt |
|---|---|
| J1 pós-desistência ❌ | No Credor, Herdeiro e Parcerias, a DESISTÊNCIA "vem antes das outras regras" e diz "nunca transfira depois de uma desistência". Isso vence o pedido de atendente. |
| J6 ❌ | "Pode me ligar?" não está na lista de pedido de atendente. |
| A9 ❌ | "não quero vender" está na lista de desistência. "Não quero vender, só quero saber quando vou receber" é consulta, não desistência. Não há rota para quem só quer saber do andamento, e "Quais dados?" não tem resposta no catálogo. |
| L2, A8, B5 ❌⚠️ | O prompt diz que "não sei" só responde à esfera **depois da versão -B**. A IA obedeceu. O problema é a [C1]/[H3] usar "estadual, municipal ou federal", que o lead não entende. |
| L16 ❌ | Parceria Adv. não tem pergunta só da UF. |
| B3, B8 ⚠️ | Não existe o caso "precatório de outra pessoa que está viva" (filho ajudando o pai, esposa pelo marido). O Credor só sabe pedir "o seu CPF". |
| B2, N12 ⚠️ | A regra 3 da Triagem manda "do meu pai" para o Herdeiro, presumindo o falecimento. |
| G1 ⚠️ | Na Triagem, a regra 0 (hesitação) vem antes da regra 3 (situação clara). "Tenho um federal, mas vou pensar" recebeu [BV-B] em vez de ir para o Credor. |
| F3 ⚠️ | "Não respondeu depois da -B → não informou" fez a IA encerrar quando a pessoa respondeu ao pedido de CPF com uma **dúvida**. Dúvida não é falta de resposta. |
| L12 ⚠️ | "O meu é X, o dele preciso procurar" deveria contar como "envia depois". A regra só fala em "mandou só um CPF → [H5-TIT] uma vez". |
| K10 ⚠️ | [X-ROBO] diz "Depois destas perguntas" e "Eu sou a Luiza": não serve depois do encerramento e repete a apresentação. |
| K13 ⚠️ | Novo atendimento (outro precatório) pede o CPF de novo. Nada diz que nome e CPF da mesma pessoa continuam valendo. |
| N6 ⚠️ | Triagem não tem resposta para "quem é você?" (a regra 8 só cita "robô"). "Amanhã mando o CPF", dito antes do pedido, não conta como resposta da [C3]. |
| L7 ⚠️ | Não existe resposta para "por que precisam do CPF?". A IA montou a resposta com a [C3-B] e duplicou a frase. |
| L8 ⚠️ | Não existe texto para CPF incompleto. |
| A4 ⚠️ | [X-COMO] descreve o atendimento, não a antecipação. O lead fica sem saber o que é. |
| A6 ⚠️ | [C2-SEM] supõe advogado ("o seu advogado consegue..."). Em ação coletiva quem tem o número é o sindicato ou a associação. |
| C6 ⚠️ | Parceria Adv. não tem resposta para "posso mandar o processo do cliente?". Caiu no [X-FORA]. |
| D4 ⚠️ | Parceria Geral (e Parceria Adv.) não têm [X-CONFIANCA]. "Vocês são de confiança?" recebeu [X-FORA]. |
| L19 ⚠️ | Parceria Geral tratou "ainda não sei o formato" como se respondesse a [P2]. |
| N2, N1 ⚠️ | Quem tem dois perfis (caso próprio + clientes; caso próprio + herança) é transferido no meio do roteiro, antes de terminar o caso atual. Parceria Adv. repetiu "A Recall trabalha com advogados parceiros" porque a frase está no [P-OI] e no [X-PARC]. |
| N3 ⚠️ | Não há rota para quem quer **vender** um precatório em nome de empresa ou comprado por cessão. A Parceria Geral aplicou o roteiro de investidor e encerrou sem pedir o processo. |
| G5 (obs.) | Depois do [DESIST], um novo "pare de mandar mensagem" recebeu o [DESIST] de novo. A regra do [DESIST-OK] só cita "ok, obrigado". |
| N4 (obs.) | "Sem problema!" do [C2-SEM] + "sem problema." do fim da [C3]: a expressão aparece duas vezes na mesma mensagem. |

Achado extra (não testado): o último exemplo da Parceria Geral responde a um engano com [ENGANO] direto, o que contradiz a própria regra ([ENGANO-Q] primeiro).

### 1.2 Falha do modelo, com a regra escrita

| Teste | O que a regra já diz |
|---|---|
| D4, N3 (sem apresentação) | "Se na conversa inteira não existe 'Eu sou a Luiza', comece com..." |
| F4 ([X-CONFIANCA] sem pergunta) | Regra 3: "nunca mande só a resposta da dúvida sem a próxima pergunta". |
| A6 ("é comum ter essa dúvida" 2x) | Regra 7: nunca o mesmo texto duas vezes. |
| N1 (ignorou o número do processo) | [C2]: número de processo responde. |
| I6 (sem o lembrete) | O [IND] já tem "não precisa me mandar os dados dela". |

Correção proposta: as três primeiras regras são as que mais falham. Na V3 elas vão para uma lista curta **"ANTES DE ENVIAR, CONFIRA"** no fim do prompt, onde o modelo lê por último: apresentação, próxima pergunta e texto repetido.

### 1.3 Expectativa do roteiro de testes que contraria o prompt (decisão sua)

| Teste | Conflito | Minha recomendação |
|---|---|---|
| F5 | [X-PAGTO] informa "em até 7 dias úteis"; o roteiro diz "nenhum prazo". | Manter o [X-PAGTO]: é um dado real da empresa e um argumento de venda. Ajustar o roteiro de testes. **Confirme se os 7 dias úteis estão certos.** |
| F8 | "Compram de SP?" → "Compramos sim!". | Concordo com o roteiro: "Compramos sim!" para um ente específico é promessa. A Recall tem critérios por ente (há até uma consulta "ente atende critérios" no Insights). Na V3: [X-COMPRA] só para "vocês compram precatório?" em geral; para estado ou município específico, um [X-ENTE] sem promessa. |
| C1 | "Tenho clientes" encerra a [A3] (forma de trabalho). | Decidir se "clientes" basta para o Henzo. Se não bastar, a [A3] passa a perguntar quantos clientes e se são honorários. |
| G3 | "Não quero" logo depois do pedido do processo foi lido como "não quero mandar o processo". | A leitura da IA é razoável. Proposta: recusar **dois dados seguidos** vira hesitação ([X-PENSAR]). |

### 1.4 Plataforma (fora do prompt)

- **N11 (mensagens picotadas):** na 2ª rajada o Broker respondeu uma vez só e o agente não viu o "estadual". Perguntar ao suporte do Broker se existe um tempo de agrupamento (debounce) de mensagens e quanto está configurado. O ideal fica entre 8 e 15 segundos.
- **N4 (resposta em 4 mensagens):** o Broker parece quebrar a resposta por parágrafo ou frase. Confirmar com o suporte se dá para mandar em uma mensagem só.
- **H1, N5 (mensagens que não chegaram ao Broker):** perda no canal (conexão 5). Vale observar se acontece na conexão 10.

### 1.5 Configuração no Broker

- **`$R` solto** na linha 15 ou 16 dos prompts V2: **erro meu** ao inserir a regra da figurinha. Já foi removido dos arquivos. Cole de novo os 5 prompts no Broker.
- **"Registrar Lead no CRM" está gravando nas oportunidades** (D1, L20). Decidir: desligar a ferramenta nos agentes ou assumir o uso (aí o texto gravado precisa de formato, como o motivo do Henzo). Hoje ela grava texto livre, inclusive valores ditos pelo lead.
- O agente "Instagram V1" aparece como "Redes V1", com 2 vínculos. Conferir se ele está ligado a alguma conexão de WhatsApp por engano.

## 2. Novo agente?

**Não recomendo criar agente novo.** Os casos sem rota (A9 consulta de andamento, B3/B8 titular vivo, N3 empresa ou cessionário que vende) são **variações do Credor**: as perguntas são as mesmas (esfera, processo, documento) e só muda de quem é o CPF ou o CNPJ. Um agente a mais criaria mais uma fronteira de roteamento, e foi nas fronteiras que mais erramos (V1: Parceria Geral como rota padrão; N1, N2, N3). Em custo, cada transferência é mais uma chamada com o histórico inteiro.

Se o volume de "só quero saber do andamento" crescer, vale reavaliar um agente **Andamento**. Antes disso, é preciso decidir com o comercial o que a Recall oferece para quem não quer vender.

## 3. Melhorias propostas por agente (V3)

### Todos os agentes de conversa

1. **Ordem de prioridade:** pedido de atendente (inclusive "pode me ligar?", "me liga", "quero falar por telefone") vem **antes** de DESISTÊNCIA. Depois de uma desistência, o pedido de atendente transfere normalmente.
2. **Depois do [DESIST]:** qualquer mensagem que não seja retomada de interesse → [DESIST-OK] (não repetir o [DESIST]).
3. **Dúvida não é falta de resposta:** se a pessoa responder a uma pergunta do roteiro com uma dúvida, responda à dúvida e repita a mesma pergunta (versão -B). Isso não conta como "não informou".
4. **Dados da mesma pessoa valem para sempre:** nome e CPF de quem fala, ditos em qualquer atendimento da conversa, não são pedidos de novo. Dados do precatório (esfera, processo) valem só para o atendimento atual.
5. **Dois perfis:** termine primeiro o roteiro do caso atual. Ao encerrar, se houver outro perfil pendente (clientes, herança), transfira para o agente certo sem mensagem. O agente que recebe não se apresenta.
6. **[X-ROBO-POS]** para depois do encerramento, sem "Eu sou a Luiza" e sem "depois destas perguntas": "Sou o atendimento virtual da Recall. O nosso gerente comercial Henzo, que é uma pessoa da nossa equipe, segue com você por aqui."
7. **"ANTES DE ENVIAR, CONFIRA"** (fim do prompt, 4 linhas): apresentação feita uma vez · tem a próxima pergunta (se o roteiro não terminou) · nenhum texto repetido · nenhum código ou colchete.

### Triagem

1. **Hesitação com situação clara** ("tenho um federal, mas vou pensar") → transfira para o especialista; o contorno fica com ele. A regra 0 vale só quando a situação ainda não está clara.
2. **"Do meu pai / da minha mãe / do meu marido" sem falar em falecimento** → transfira para o HERDEIRO com o motivo "falecimento não confirmado". O Herdeiro confirma primeiro (abaixo).
3. **Regra 8:** "quem é você?", "quem fala?", "que empresa é essa?" → [X-ROBO] + [BV-B] (ou só [BV], se ainda não foi enviado).
4. **Regra 5b (foi indicada):** sem mudança. Funcionou (I5).

### Credor

1. **[C1] em linguagem simples:** "O seu processo é contra o Estado, contra a Prefeitura ou contra a União (governo federal)? Se não souber, tudo bem." "Não sei" na primeira vez já responde. Some o problema do L2.
2. **Titular vivo (representante):** se o precatório é de outra pessoa viva (pai, mãe, marido), a [C3] vira [C3-TIT]: "Para consultarmos no tribunal, pode me enviar o CPF de quem é o titular do precatório? Se preferir mandar depois, tudo bem." Motivo: "Perfil: representante do titular".
3. **Empresa ou cessionário que quer vender** (precatório em nome de empresa, comprado por cessão): continua no Credor com [C3-CNPJ] ("Pode me enviar o CNPJ da empresa que é titular do precatório?"). Motivo: "Perfil: cessionário/PJ".
4. **Consulta de andamento** ("não quero vender, só quero saber quando recebo"): não é desistência. Mande [X-ANDAMENTO] + a próxima pergunta. Só é desistência se a pessoa recusar de novo. Novo [X-DADOS] para "quais dados?": "Só o número do processo (ou um documento dele) e o seu CPF. Com eles a nossa equipe consulta o tribunal."
5. **Novas respostas no catálogo:**
   - [X-CPF] para "por que precisam do CPF?": "Usamos o seu CPF só para consultar o processo no tribunal. Nunca pedimos senha nem dados bancários."
   - [C3-CONF] para CPF com dígitos faltando: "Acho que faltou algum número no CPF. Pode conferir, por favor?" (uma vez; depois segue).
   - [X-ENTE] para um estado ou município específico: "Analisamos precatórios de vários estados e municípios. Para confirmar o seu, preciso de alguns dados."
   - [X-COMO] passa a explicar a antecipação: "Nós compramos o seu precatório: você recebe agora, à vista, em vez de esperar a fila de pagamento do governo. Fazemos a análise e enviamos uma proposta, sem compromisso e sem nenhum custo para você."
6. **[C2-SEM] sem supor advogado:** "Sem problema! O número do processo costuma estar com o advogado, o sindicato ou a associação que entrou com a ação. Quando tiver, é só me mandar aqui."
7. **[C3] sem "sem problema" no fim:** "...Se preferir, pode mandar depois."
8. **[C3] já respondida** também quando a pessoa disse, em qualquer momento, que manda o CPF depois ("amanhã te mando o CPF").
9. **Recusa de dois dados seguidos** → [X-PENSAR] + a próxima pergunta. Uma terceira recusa → [DESIST].

### Herdeiro

1. **[H0] confirma o falecimento** quando o motivo ou a conversa não deixam claro: "Só para eu entender: a pessoa que era titular do precatório faleceu?" Se a pessoa disser que está viva → transfira para o CREDOR (motivo: "titular vivo, representante"), sem mensagem.
2. **"Espólio" responde à [H2]** (o espólio existe quando o inventário está aberto).
3. **[H5]:** "o dele eu não sei" ou "preciso procurar" = "envia depois". Não use [H5-TIT] nesse caso.
4. **[H3]** em linguagem simples (igual à [C1]).
5. Os mesmos [X-CPF], [X-DADOS] e [C3-CONF] do Credor.

### Parceria Adv.

1. **[A2-UF]:** "E a sua OAB é de qual estado?" Use quando o número veio sem a UF.
2. **[X-MANDAR]** para "posso mandar o processo do meu cliente?": "Pode mandar sim! Eu deixo junto com o seu contato para o nosso gerente comercial Henzo." + a próxima pergunta.
3. **[X-PARC]** sem "A Recall trabalha com advogados parceiros" quando o [P-OI] já foi enviado.
4. **[X-CONFIANCA]** adaptado: "Entendo, e é bom ter cuidado! Você pode conhecer a Recall no nosso site: https://recallprecatorios.com.br."
5. **[A3]:** depende da sua decisão sobre "clientes" (seção 1.3).

### Parceria Geral

1. **Quer VENDER** um precatório (próprio, da empresa, comprado por cessão) → transfira para o CREDOR, mesmo que seja fundo ou investidor. Hoje a seção "PRIMEIRO, CONFIRA" só cita pessoa física.
2. **[P2] só é pulada** quando a pessoa descreveu o formato. "Ainda não sei", "depende" ou "queremos conversar" não respondem.
3. **[X-CONFIANCA]** (igual ao da Parceria Adv.).
4. Corrigir o último exemplo: engano → [ENGANO-Q] primeiro.
5. Exemplo de primeira mensagem **com apresentação** + [INTUITO] (D4 saiu sem).

### Instagram

Sem mudanças pelos testes (não foi testado). Pendente: ativar na conexão 9.

## 4. Custo

As mudanças aumentam os prompts dos especialistas em cerca de 10 a 15% (de 2.200 a 2.900 palavras hoje). Com cerca de 50 conversas a cada 4 dias no Terra, a diferença é de centavos por dia. O que reduz custo de verdade é evitar transferências desnecessárias (G1, N1, N2): cada uma é mais uma chamada com o histórico inteiro.

## 5. Testes ainda pendentes

E10 (cadência), O5 (histórico com mensagens do Henzo), O8 (conexão 10), grupo M e A10 (mídia: áudio, figurinha, foto, PDF). A regra de figurinha da V2 depende do M5: confirmar que o Broker não manda a mensagem padrão quando a IA não responde.

Depois da V3, retestar: todos os ❌ e ⚠️ da seção 1.1 e a regressão P1 a P12.

## 6. Perguntas que faltam responder

Agrupei por quem precisa responder. As marcadas com **(bloqueia a V3)** mudam o texto dos prompts. As outras podem esperar.

### Marketing (você)

1. **(bloqueia a V3)** [X-COMPRA]: quando o lead perguntar de um estado ou município específico ("compram de SP?"), posso trocar o "Compramos sim!" por uma resposta sem promessa ([X-ENTE])?
R: Sim, fale que essa pergunta o nosso gerente comercial é quem responderá

2. **(bloqueia a V3)** Concorda em **não criar um agente novo** e tratar no Credor o titular vivo, a empresa ou cessionário que quer vender e a consulta de andamento?
R: Sim!

3. **(bloqueia a V3)** Aprova os textos novos propostos na seção 3: [X-COMO] (explica a antecipação), [C2-SEM] (advogado, sindicato ou associação), [X-CPF], [X-DADOS], [C3-CONF], [H0], [A2-UF], [X-MANDAR], [X-ROBO-POS] e [X-CONFIANCA] das Parcerias?
R: Sim!

4. **Ferramenta "Registrar Lead no CRM":** desligar nos agentes ou passar a usar, com um formato fixo como o do motivo do Henzo? Hoje ela grava texto livre nas oportunidades, inclusive valores ditos pelo lead.
R: Eu mesmo vou desligar

5. **Copy do [BV]** da Triagem ("Nós compramos precatórios: assim você recebe o seu dinheiro sem precisar esperar a fila de pagamento do governo..."): está validada?
R: Remova esse "Nós compramos precatórios: assim você recebe o seu dinheiro sem precisar esperar a fila de pagamento do governo..."

6. **Cadência do Broker:** atualizar a boas-vindas antiga ("Olá! Eu sou a Luiza, responsável pelo primeiro contato... Como posso te ajudar??") ou desligar? Ela compete com o [BV] e gera apresentação dupla (teste E10).
R: Utilize sempre essa apresentação, certifique-se de que não haverá apresentação dupla.

7. **Agente Instagram:** quando ativar na conexão 9? Conferir também por que ele aparece como "Redes V1" com 2 vínculos.
R: Não precisa se preocupar com o agente do Instagram no momento.

8. **Follow-up** para quem não responde à primeira mensagem (cerca de 59% dos leads na V1): vamos montar pelos Fluxos de Mensagens/Cadência do Broker?
R: Eu mesmo irei fazer o fluxo.

### Comercial (Henzo)

9. **(bloqueia a V3)** [X-PAGTO]: "o pagamento acontece depois do envio dos documentos e da assinatura da escritura pública, em até 7 dias úteis" está correto? Se estiver, mantemos e corrigimos o roteiro de testes (F5).
R: Sim, está correto.

10. **(bloqueia a V3)** Parceria Adv.: "tenho clientes com precatório" basta como forma de trabalho, ou o Henzo precisa saber também quantos clientes e se o advogado atua com honorários?
R: Saber se o advogado atua com honorários é essencial, o número de clientes não é obrigatório mas seria bom.

11. **(bloqueia a V3)** Titular vivo (o filho ajudando o pai): o Henzo atende quem não é o titular, ou o titular precisa entrar na conversa? Muda o texto da [C3-TIT].
R: O Henzo atuará com a pessoa que está representando o Titular, sem problemas.

12. **(bloqueia a V3)** Empresa ou cessionário que quer vender: a Recall compra precatório em nome de empresa ou adquirido por cessão? Se não comprar, a resposta é outra (não pedir CNPJ).
R: Sim!

13. **Consulta de andamento:** o que a Recall oferece a quem só quer saber "quando vou receber" e não quer vender? Hoje a IA pede os dados e passa para o Henzo. Isso serve ou gasta o tempo dele?
R: Isso serve.

14. **Prazos reais**, para colocar número nos textos (hoje eles não têm número):
    - em quanto tempo o Henzo chama o lead depois do encerramento ([X-PRAZO-CONTATO])?
    - em quanto tempo sai a proposta ([X-PRAZO-PROPOSTA])?
R: No momento não é possível dizer nenhuma das respostas.

15. **Critérios de qualificação:** quais casos o Henzo quer receber primeiro (esfera, ente, valor, fase do processo)? Hoje todo caso completo vai para ele.
R: No momento não responderemos perguntas específicas sobre.

16. **Entes que a Recall compra:** existe uma lista de estados e municípios fora dos critérios? Com ela, o [X-ENTE] pode responder sem promessa e sem perder o lead.
R: No momento não responderemos perguntas específicas sobre.

### Suporte do Broker

17. Existe um **tempo de agrupamento** (debounce) para mensagens enviadas em sequência? Quanto está configurado? Ideal: 8 a 15 segundos (teste N11).
18. Dá para a resposta da IA sair **em uma mensagem só**, sem ser quebrada em várias (teste N4)?

19. Se a IA não responder nada (figurinha), o Broker manda a mensagem padrão "Nossa equipe responderá assim que possível."? (teste M5)
R: Não deve fazer nada

20. Mensagens que saíram do WhatsApp e **não chegaram ao Broker** em 24/11 e 25/11 (H1, N5): é instabilidade conhecida da conexão 5?
R: Não sei dizer
### Testes que só nós conseguimos rodar

21. E10 (cadência), O5 (mensagem do Henzo no histórico) e O8 (conexão 10): precisam de ação de escrita no Broker ou de outro número.
22. Grupo M e A10: mandar áudio, figurinha, foto, PDF e vídeo reais.
