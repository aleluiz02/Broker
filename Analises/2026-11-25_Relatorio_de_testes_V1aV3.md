# Relatório de testes: atendimento por IA (Luiza)

**Data:** 25/11/2026, dados até as 14h25.

**Fontes:**
- o roteiro e os resultados registrados em `Testes/Roteiro_1_V2aV5.md`;
- as mensagens e os eventos da conversa de teste no Broker (contato "Alexandre"), lidos pela API sem alterar nada;
- a análise da V1 feita com 63 conversas reais.

## Resumo

| | V1 (até 23/11) | V2 (24 e 25/11) | V3 (25/11, à tarde) |
|---|---|---|---|
| Base | 63 conversas reais de leads | 140 cenários avaliados em 74 conversas de teste | 27 cenários avaliados em 14 conversas de teste (1 ainda em andamento) |
| Passou (✅) | — | 105 (75%) | 20 (74%)* |
| Parcial (⚠️) | — | 29 (21%) | 6 (22%) |
| Falhou (❌) | vários defeitos graves (ver seção 5) | 6 (4%) | 1 (4%) |
| Mensagens da IA por conversa (média) | — | 5,1 (mediana 4) | 5,6 (mediana 6) |
| Transferências para humano | também em desistências e dúvidas | 5, todas a pedido | 3, todas a pedido |

\* Os resultados da V3 não são comparáveis aos da V2. A V3 foi testada quase só nos casos que **falharam** na V2, que são os mais difíceis. A medida que vale para a V3 está na seção 4: de 25 problemas da V2 retestados, 19 (76%) foram resolvidos por completo e 6 melhoraram em parte.

## 1. Quantos testes foram feitos

**O roteiro** (`Testes/Roteiro_1_V2aV5.md`) tem **169 cenários**, divididos em 16 grupos:
- perfis: credor, herdeiro, parceria com advogado, parceria geral;
- situações: leads de anúncio, dúvidas soltas, desistência, engano, indicação, pedido de atendente, pós-encerramento, qualificação, mídia, casos limite, histórico;
- regressão das falhas antigas.

**Testes rodados:**

| Dia | Versão | Conversas de teste* | Cenários avaliados | ✅ | ⚠️ | ❌ |
|---|---|---|---|---|---|---|
| 24/11 | V2 | 48 | 99 (1 não rodado) | 71 | 22 | 6 |
| 25/11 (manhã) | V2 | 26 | 41 | 34 | 7 | 0 |
| 25/11 (tarde) | V3 | 15 | 27 (26 + 1 regressão) | 20 | 6 | 1 |
| **Total** | | **89** | **167** | **125** | **35** | **7** |

\* Cada conversa vai de uma limpeza de contexto até a próxima. A limpeza foi usada 92 vezes nos dois dias. Uma conversa pode cobrir vários cenários: por exemplo, um roteiro completo de credor seguido de perguntas pós-encerramento.

**Método:** cada cenário foi conduzido como uma conversa real pelo WhatsApp, respondendo a cada pergunta da Luiza até o encerramento. Só depois o contexto era zerado.

**Ainda não testados:**
- E10: a boas-vindas da cadência;
- O5: um histórico com mensagens do Henzo;
- O8: a conexão 10 (uazapi);
- o grupo M: áudio, figurinha, foto e PDF reais.

## 2. Dez cenários testados

| # | O que o "cliente" disse | O que se esperava | V2 | V3 |
|---|---|---|---|---|
| 1 | "Olá! Posso ter mais informações sobre isso?" (mensagem do anúncio) | Apresentação + "Como posso te ajudar?" | ✅ | ✅ |
| 2 | "Quero saber como está meu precatório… não quero vender, só quero saber quando vou receber" | Explicar que a consulta precisa do processo e seguir o roteiro, sem tratar como desistência | ❌ tratou como desistência e encerrou | ⚠️ seguiu o roteiro, mas mandou dois textos que não tinham a ver com a pergunta |
| 3 | "Meu pai tem um precatório, ele ainda está vivo, estou ajudando ele a vender" | Ir para o Credor e pedir o CPF **do titular** | ⚠️ não transferiu de início e pediu "o seu CPF" | ✅ |
| 4 | "Do meu marido" (sem dizer se faleceu) | Confirmar se o titular faleceu antes de tratar como herança | ⚠️ presumiu o falecimento | ✅ perguntou, viu que estava vivo e passou para o Credor |
| 5 | Advogado: "OAB 123456" (sem o estado) | Perguntar só o estado | ❌ repetiu a pergunta inteira | ✅ "E a sua OAB é de qual estado?" |
| 6 | "Vocês compram precatório de São Paulo?" | Não prometer: quem confirma é o Henzo | ⚠️ respondeu "Compramos sim!" | ✅ |
| 7 | "Isso não é golpe?" | Resposta de confiança + próxima pergunta | ⚠️ esqueceu a próxima pergunta | ✅ |
| 8 | "Não tenho interesse" e, depois, "Quero falar com um atendente" | Respeitar a desistência; no pedido de atendente, transferir para o Henzo | ❌ não transferiu | ⚠️ transferiu, mas a mensagem padrão do Broker apareceu para o cliente |
| 9 | "Sou investidor e quero vender um precatório que comprei" (em nome da empresa, por cessão) | Credor, pedindo o CNPJ | ⚠️ foi para a Parceria Geral, que tratou como investidor | ✅ pediu esfera, processo e CNPJ |
| 10 | "Quero retirar meu bolo" (assunto sem relação) | Perguntar se foi engano; se a pessoa insistir, alternar os textos de engano | ✅ | não retestado (passou na V2) |

## 3. Eficiência da V2

**Resultado:** 75% dos cenários passaram, 21% passaram em parte e 4% falharam.

**Onde a V2 acertou:**
- **Rota:** o lead de anúncio caiu sempre no Credor e nunca mais na Parceria Geral, que era o pior problema da V1.
- **Primeira resposta após transferência:** o especialista sempre fez a próxima pergunta.
- **Mensagens sem relação:** engano, propaganda, cobrança e sorteio foram tratados com os textos certos.
- **Pós-encerramento:** prazo, "obrigado", "oi" e "Feliz Natal" receberam respostas variadas, sem repetir texto.
- **Sem vazamento:** nenhum código ou JSON apareceu para o cliente.
- **Valores:** a IA nunca informou valor nem percentual, nem sob pressão.

**As 6 falhas da V2:**
1. Quem só queria saber o andamento foi tratado como desistência (A9).
2. "Pode me ligar?" não foi entendido como pedido de atendente (J6).
3. Depois de uma desistência, o pedido de atendente foi ignorado (J1).
4. "Não sei a esfera" fez a IA repetir a pergunta (L2).
5. A OAB sem o estado fez a IA repetir a pergunta inteira (L16).
6. A Parceria Geral improvisou textos fora do catálogo (D1). A causa era um erro de configuração no Broker, corrigido em 25/11.

**Parciais mais comuns:** perguntar de novo um dado já informado, pedir "o seu CPF" a quem falava por um parente vivo, e encerrar cedo quando o lead ainda estava tirando dúvidas.

## 4. Desempenho da V3

A V3 entrou no Broker em 25/11, por volta das 13h30, e foi testada das 13h50 às 14h25.

**Problemas da V2 que a V3 resolveu (19 de 25 retestados):**
- "Não sei" para a esfera agora segue para o processo (L2).
- OAB sem estado: pergunta só o estado (L16).
- Pergunta "por que precisam do CPF?" sem repetir frase (L7). CPF incompleto: avisa "acho que faltou algum número" (L8).
- Parente vivo: vai direto para o Credor e pede o CPF do titular (B3, B8).
- "Do meu marido": confirma se faleceu (B3).
- "Tem espólio": não pergunta o inventário de novo e dá os pêsames (B5).
- "O CPF da minha mãe eu não sei": encerra sem insistir (L12).
- "Ainda vou pensar" com a situação clara: transfere e faz um contorno (G1). "Não quero": desistência certa (G3).
- Compram de SP? Quem confirma é o Henzo (F8).
- "É golpe?": resposta + próxima pergunta (F4). Pergunta de prazo: "em até 7 dias úteis" + próxima pergunta (F5).
- "Pedem senha?": responde e continua pedindo o CPF, sem encerrar (F3).
- "Vocês são robô?" depois do encerramento: sem se reapresentar (K10).
- Segundo precatório: reaproveita o CPF já enviado (K13).
- "Quem é você?": explica e repete a pergunta (N6). "Amanhã mando o CPF": encerra sem insistir (N6).
- Advogado que quer mandar processo de cliente: "Pode mandar sim!" (C6). Honorários: perguntado (C1).
- Empresa ou cessão que quer vender: Credor pede o CNPJ (N3).

**Melhoraram, mas ainda estão parciais (6):**
- **Andamento (A9):** não trata mais como desistência, mas mandou dois textos fora de contexto antes de encerrar.
- **"É comum ter essa dúvida!" sem dúvida do cliente (A6):** continua acontecendo na primeira resposta.
- **"Pode me ligar?" (J6) e pedido de atendente depois de desistir (J1):** a IA agora transfere, mas a frase padrão do Broker saiu junto (ver falha abaixo).
- **"Depois eu vejo" logo depois do contorno (G1):** virou desistência ("respeito a sua decisão"). Pode ser só "mando depois". Como a desistência fica fora do follow-up, esse lead pode se perder.
- **Dois precatórios, um próprio e um do pai falecido (N1):** a conversa passou quatro vezes entre o Credor e o Herdeiro. Uma resposta saiu só como "Certo." e o cliente teve que perguntar "E agora?". No fim, coletou tudo.

**Falha nova (1):**
- **P12, mensagem padrão do Broker:** quando o pedido de atendente vem **depois** de um encerramento ou de uma desistência, a frase "Nossa equipe responderá assim que possível." apareceu para o cliente. Isso aconteceu duas vezes: uma junto com o texto de encerramento e outra como mensagem separada. Numa conversa nova (J1), não apareceu.
- Correção sugerida: deixar explícito no prompt que o texto de encerramento é escrito na transferência mesmo que a conversa já tenha sido encerrada.

**Não testado na V3:** o grupo D (Parceria Geral), engano, pós-encerramento completo e o caso N2 (advogada com precatório próprio e clientes), que ainda estava em andamento.

## 5. Mensagens enviadas pela IA

| | V2 | V3 |
|---|---|---|
| Conversas de teste | 74 | 14 completas |
| Mensagens da IA | 381 | 79 |
| **Média por conversa** | **5,1** (4,4 sem as 2 conversas exploratórias mais longas) | **5,6** |
| Mediana | 4 | 6 |
| Mínimo e máximo | 1 e 37 | 1 e 10 |
| Mensagens da IA para cada mensagem do cliente | 1,10 | 1,27 |
| Transferências entre agentes | 75 | 18 |
| Transferências para humano | 5 (todas a pedido) | 3 (todas a pedido) |

**Como ler:**
- **Roteiro completo:** um atendimento com esfera, processo e CPF fica entre 4 e 8 mensagens da IA.
- **Média maior na V3:** a V3 foi testada com cenários mais longos (retestes de falhas). Além disso, ela parte mais as respostas: manda a apresentação numa mensagem e a pergunta em outra. Por isso a proporção subiu de 1,10 para 1,27 mensagem por mensagem do cliente.
- **Respostas partidas:** é um ajuste do Broker (pendência N4 com o suporte), não do prompt.

## 6. Exemplos de falhas encontradas

**V1 (63 conversas reais, até 23/11):**
- 59% dos leads não responderam à primeira mensagem.
- 10 leads de anúncio foram parar na Parceria Geral e ouviram "você é investidor?".
- Os agentes no modelo Luna encerravam o atendimento logo depois de receber a transferência.
- Vazaram para o cliente códigos e símbolos internos (`[POS-ENC] "..."`, `”】【`) e o JSON da transferência.
- O CPF foi pedido de novo a quem já tinha mandado.
- Uma advogada com precatório próprio foi tratada como parceira.
- Respostas muito parecidas, enviadas entre aspas.
- A IA pedia informações antes de se apresentar.
- Leads foram passados ao comercial cedo demais.

**V2 (testes de 24 e 25/11):**
- A Parceria Geral transferiu para humano logo ao receber a conversa, no modelo gpt-6-luna. Depois, mesmo no Terra, um erro de configuração fez o campo de prompt ficar só com o texto do papel.
- O Credor se apresentou de novo no meio da conversa.
- "Quero retirar meu bolo" recebeu várias vezes a resposta de dúvida sobre precatórios, em vez da de engano.
- A IA encerrou com o Henzo quando o cliente só estava se certificando ("vocês pedem senha?") antes de mandar o CPF.
- Na pergunta "o CPF é o meu ou o do meu marido?", a IA respondeu "essa dúvida o Henzo responde" e pediu o CPF errado.
- O mesmo texto saiu duas vezes seguidas ("Tudo bem, é comum ter essa dúvida!").
- Uma advogada com caso próprio foi passada para a parceria antes de terminar o roteiro do caso dela.
- Uma linha solta `$R` foi colada nos prompts. Foi erro na edição dos arquivos, já corrigido.
- A ferramenta "Registrar Lead no CRM" gravou texto livre nas oportunidades.

**V3 (teste de 25/11 à tarde):** os itens da seção 4. Resumindo:
- a mensagem padrão do Broker na transferência pedida depois de um encerramento;
- a conversa passando quatro vezes entre o Credor e o Herdeiro quando há dois precatórios;
- "depois eu vejo" tratado como desistência;
- "É comum ter essa dúvida!" sem o cliente ter perguntado nada.

**Problemas de plataforma (Broker/WhatsApp, não da IA):**
- Duas mensagens enviadas pelo WhatsApp não chegaram ao Broker em 24/11.
- Mensagens picotadas em sequência ("é" / "estadual" / "do paraná") receberam uma única resposta, que ignorou parte do conteúdo.
- O campo do papel de transferência corta o texto em 480 caracteres.

## 7. Próximos passos

1. **Corrigir a mensagem padrão (P12):** ajuste pequeno no prompt dos 5 agentes.
2. **Duas regras novas:**
   - "Depois eu vejo" logo depois de um contorno vira "mando depois" e encerra com o Henzo, não como desistência.
   - Com dois precatórios, o agente termina um roteiro e transfere uma única vez.
3. **Testar na V3** o grupo D, o caso N2, o pós-encerramento e a regressão P1 a P12.
4. **Testes pendentes:** cadência (E10), histórico com o Henzo (O5), conexão 10 (O8) e mídia real (grupo M).
5. **Depois de alguns dias em produção:** medir com leads reais a taxa de resposta à primeira mensagem, a de roteiro completo e as mensagens por conversa, para comparar com os números da V1.
