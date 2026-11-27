# Broker — Atendimento por IA da Recall Precatórios

Esta pasta guarda os prompts dos agentes de IA que atendem os leads da Recall Precatórios (compra de precatórios) no **BrokerChat**, o omnichannel da empresa. O trabalho aqui é melhorar a **assertividade** dos agentes e gastar só o necessário com **modelos**. Este arquivo é contexto para o Claude; o usuário (Marketing) é quem cola os prompts no Broker.

> **Passagem de bastão (27/11/2026):** a demanda mudou de responsável. Leia `LEIA_PRIMEIRO.md` antes de tudo: estado atual, pendências em ordem de prioridade, acessos e o método de teste (que antes só existia na memória local do Claude de quem conduziu).

## Estrutura (reorganizada em 27/11/2026)

```
LEIA_PRIMEIRO.md                      passagem: estado atual, pendências, acessos, como testar
Agentes/V<n>/<Agente> - prompt.md     prompt de sistema do agente (campo "Prompt do sistema" no Broker)
Agentes/V<n>/<Agente> - transferencia.md   campo "papel" do agente no Broker: descrição que os OUTROS agentes leem para decidir a transferência
Agentes/V5/                           versão atual em produção (Triagem, Credor, Herdeiro, Parceria Adv, Parceria Geral)
Agentes/V0_curta/                     V5 resumida (até 8000 caracteres), sem arquivo de transferência próprio (usa o da V5)
Agentes/V4/                           V4 (Venda = Credor + Herdeiro; piorou nos testes, substituída pela V5)
Agentes/V1/ … V3/                     histórico; o agente Instagram só existe na V2
Testes/Roteiro_1_V2aV5.md             169 cenários (grupos A a Q) + registro dos testes de 24 a 26/11
Testes/Roteiro_2_V5.md                pendentes do Roteiro 1 + 200 cenários novos para a V5 (ainda não rodado)
Analises/AAAA-MM-DD_<assunto>.md      análises e relatórios, em ordem de data
```

- O modelo **não** fica mais no nome do arquivo (ficava defasado: a Triagem V5 se chamava "6.1 Terra" e rodava no Sol). A tabela de modelos por versão está em `LEIA_PRIMEIRO.md`.
- Nova versão = nova pasta `Agentes/V<n>/` com o mesmo padrão de nomes.
- **Nunca sobrescreva uma versão.** Mudança relevante = nova versão em novo arquivo. Ajuste pequeno durante testes pode ir na versão atual, com aviso ao usuário.
- Prompts ficam em texto puro (sem Markdown): eles são colados como estão no campo "prompt de sistema" do Broker.

## Fluxo de atendimento

Persona única para o cliente: **Luiza** (feminino). Toda conversa termina no **Henzo**, gerente comercial (humano).

```
WhatsApp (conexão 10, uazapi) ──► Triagem ──► Credor | Herdeiro (V5; na V4 eram um só agente, Venda) | Parceria Adv | Parceria Geral ──► humano (Henzo)
Instagram (conexão 9)         ──► Instagram (só manda o link do WhatsApp)  [V2 — ainda não ativado no Broker]
```

Configuração no Broker em 24/11/2026, **desatualizada** (estado mais recente em `LEIA_PRIMEIRO.md`; o usuário renomeou os agentes; os ids mudaram de função):

| Agente (Broker) | id | Modelo no Broker | Função | Perguntas |
|---|---|---|---|---|
| Recall \| Triagem V2 | 12 | gpt-6.1-terra | classifica e transfere sem escrever | — |
| Recall \| Credor V2 | 8 | gpt-6.1-terra | precatório no próprio nome | esfera, processo, CPF |
| Recall \| Herdeiro V2 | 9 | gpt-6-luna ⚠️ | titular faleceu | titular, inventário, esfera, processo, CPF do herdeiro e do titular |
| Recall \| Parceria Adv. V2 | 10 | gpt-6-luna ⚠️ | advogado(a) parceiro(a) | nome, honorários (V5: sem OAB) |
| Recall \| Parceria Geral V2 | 11 | gpt-6-luna ⚠️ | empresa, fundo, investidor, captador | intuito + roteiro (investidor / parceiro / outros) |
| Recall \| Instagram V1 | 7 | gpt-6-luna | leva o Instagram para o WhatsApp | — |

⚠️ No teste de 24/11, a Parceria Geral no gpt-6-luna repetiu a falha do Luna 5.6 da V1: transferiu para humano logo ao receber a conversa, com a mensagem padrão do Broker. A recomendação é Terra para todos os agentes de conversa. Os testes de 24/11 rodaram na conexão 5 (Meta oficial).

- A transferência entre agentes é **sem mensagem** (decisão do usuário, para soar natural): o agente que recebe responde na hora. Isso funciona; não mude.
- **Regra do usuário (24/11/2026): a IA só transfere para atendente humano quando a pessoa PEDE para falar com atendente, pessoa, humano ou com o Henzo.** Roteiro completo, encerramento, pós-encerramento, dúvida, desistência, engano e áudio não entendido recebem só o texto, sem transferência. A conversa continua com a IA, e o Henzo acompanha os leads qualificados pelo Inbox.
- A transferência para humano grava um evento `transferencia_humano` com o `motivo`, que é o resumo que o Henzo lê. Hoje isso só acontece no FIM-PEDIDO. O CRM/Bitrix **não** é usado por enquanto. A ferramenta `registrar_lead` estava gravando texto livre nas oportunidades; o usuário vai desligá-la (25/11/2026).
- Cada agente tem no Broker um campo `configuracao.papel` (arquivo `Agentes/V<n>/<Agente> - transferencia.md`), que descreve o agente para os outros na hora de transferir. Ele pesa tanto quanto o prompt na escolha da rota. O campo do Broker corta o texto em **480 caracteres** (visto em 25/11/2026). Escreva-o curto e excludente ("Não use para..."), sem frases do tipo "qualquer assunto que não se encaixa". Na V1, essa frase transformou a Parceria Geral na rota padrão. O prompt e o papel de um agente precisam estar alinhados: se mudar um, revise o outro.
- Os áudios são transcritos pelo Broker (whisper-1) antes de chegar ao agente.
- O mesmo número de WhatsApp (+55 41 99186-1463) está em duas conexões: 5 (Meta oficial, sem agente) e 10 (uazapi, `agente_ia_id: 7`). Por isso toda conversa aparece duplicada no Inbox. Ao analisar, use só a conexão 10.
- Leads de anúncio chegam com a mensagem padrão "Olá! Posso ter mais informações sobre isso?" (cerca de 75% das conversas).

## Modelos e custo

O Broker cobra por "peso" (multiplicador de tokens; R$ 1 ≈ 1 milhão de tokens de plataforma). A lista atual vem de `GET /admin/agentes-ia/modelos`. Os relevantes:

| Modelo | Peso | Uso |
|---|---|---|
| gpt-6-luna | 0,6 | tarefas triviais (agente Instagram) |
| gpt-5.6-luna | 1,2 | **não usar**: na V1 encerrava o atendimento na primeira resposta |
| gpt-6.1-terra | 6,0 | **padrão** para todos os agentes de conversa |
| gpt-6.1-sol / gpt-5.6-terra / gpt-4.1 | 12 | não compensa: custa mais que o Terra |

- Teto definido pelo usuário: nada mais caro que o `gpt-6.1-terra`. **Atualizado em 25/11/2026:** o usuário liberou o `gpt-6.1-sol` (peso 12). Na V4, o Sol é usado só no agente Venda; os demais continuam no Terra.
- Ordem de grandeza: cerca de 50 conversas a cada 4 dias. Com tudo no Terra, o gasto fica em dezenas de reais por mês. Um lead perdido custa muito mais que a economia de usar um modelo mais fraco. Gaste otimizando o **tamanho do prompt**, não rebaixando o modelo.
- Consumo real: `GET /admin/consumo-ia`.

## Princípios de prompt (aprendidos com a V1)

1. **Catálogo fechado.** O agente só copia textos do catálogo (mais uma confirmação de até 6 palavras, ou uma cortesia de até 12 palavras para "tudo bem?", "Feliz Natal" etc.: regra 2b). Os códigos `[XX]` nunca vão para o cliente. Diga explicitamente "sem aspas, sem JSON, sem o código".
2. **Nada de mensagem só de apresentação.** O texto "Eu sou a Luiza" sempre vai junto com uma pergunta. Na V1, o `[APRES]` sozinho deixou leads sem pergunta.
   - **A apresentação acontece uma vez na conversa INTEIRA**, nunca "no atendimento atual". Em 24/11, o Credor se apresentou de novo no meio da conversa porque o [FIM-HENZO] de outro agente "zerou" o atendimento atual.
   - **Apresentação padrão (decisão do usuário, 25/11/2026):** "Olá! Eu sou a Luiza, responsável pelo primeiro contato aqui na Recall." É o mesmo texto da boas-vindas da cadência do Broker. O [BV] da Triagem é essa frase + "Como posso te ajudar?", e os especialistas usam a mesma frase quando recebem a primeira mensagem. A frase "Nós compramos precatórios: assim você recebe..." foi removida a pedido do usuário.
   - A cadência conta como apresentação: depois dela, a Triagem vai direto para o [BV-B].
3. **Quem recebe uma transferência nunca encerra na primeira resposta.** Sempre faz a próxima pergunta. Esse era o pior defeito da V1.
4. **Dúvida não é motivo para chamar o humano.** Responda com `[X-FORA]` mais a próxima pergunta. Só se encerra com o roteiro completo ou quando a pessoa pede atendente.
   - O `[X-FORA]` só serve para dúvidas **sobre precatórios ou sobre a Recall**, e nunca sai duas vezes seguidas (existe a `[X-FORA-B]`). Em 24/11, "quero retirar meu bolo" recebeu `[X-FORA]` várias vezes.
   - Assunto sem relação → `[ENGANO-Q]` (pergunta se foi engano), depois `[ENGANO]` e `[ENGANO-B]`.
   - Indicação ("posso indicar vocês?") → `[IND]`.
   - Essas regras ficam em **todos** os agentes, não só na Triagem: depois de uma transferência, quem responde é o especialista.
5. **Na dúvida, a rota é CREDOR** (a maioria dos leads). A Parceria Geral nunca recebe pessoa física nem lead de anúncio genérico.
6. **Regras de "já respondido" explícitas** (lista de palavras e formatos: CPF de 11 dígitos, número de processo, nome de cidade ou estado). Isso evita perguntar de novo.
7. **"Ok", "sim", "amanhã" em resposta ao pedido de CPF = "envia depois"** → encerra. Não insista.
8. Prompt curto, numerado e sem regras que se contradizem. Cada "SEMPRE/NUNCA" a mais reduz a obediência às outras regras.
9. **Transferir para humano sem escrever texto faz o Broker mandar o padrão "Nossa equipe responderá assim que possível."** Todo prompt precisa dizer: escreva o texto e use a ferramenta na mesma resposta; se a ferramenta pedir mensagem, coloque o mesmo texto. A V2 perdeu essa linha da V1 e falhou no primeiro teste.
10. Só frases de encerramento da Luiza encerram o atendimento. Mensagens de pós-venda ou de outras pessoas da equipe no histórico não encerram nada.
11. Teste com conversas que já têm histórico (cliente que volta, número já atendido por humano), não só com conversa nova.
12. O motivo da transferência para humano segue um formato fixo (`Perfil: ... | Esfera: ... | ...`) para o Henzo ler rápido.
13. **Desistência:**
    - Hesitação ("vou pensar") recebe UM contorno sem pressão ([X-PENSAR] ou [X-CONFIANCA]) e o roteiro segue.
    - Recusa firme, ou mantida depois do contorno, recebe [DESIST] ou [JA-VENDEU] e **nunca é transferida** (nem para o humano, nem para outro agente), como qualquer situação que não seja um pedido de atendente.
    - O Henzo não é avisado: se o caso já estava com ele, ele só descobre lendo a conversa.
    - Nunca insista duas vezes.
    - "respeito a sua decisão" e "obrigada por avisar" contam como frases de encerramento.

## Como testar

- Número de teste: o do próprio usuário (contato "Alexandre"). O Broker mantém **todo o histórico** desse contato (centenas de mensagens desde 17/11), e o agente recebe tudo a cada chamada.
- Depois de um encerramento ([FIM-HENZO] ou [FIM-OK-HERD]), a IA **continua respondendo**, sem transferir e sem recomeçar o roteiro (decisão do usuário, 24/11/2026). A [POS-ENC] repetida foi removida.
  - prazo de contato, "alguma novidade?" ou cobrança → textos de COBRANÇA [X-COBRA-1] a [X-COBRA-6], em ordem e sem repetir na conversa, com uma frase de empatia opcional (até 12 palavras). No Credor e no Herdeiro, junta uma vez o pedido do CPF ou do processo que faltou (V3, 25/11/2026; textos-base do usuário);
  - prazo da análise ou da proposta → [X-PRAZO-PROPOSTA];
  - outras dúvidas → as respostas do catálogo;
  - informação nova → [X-INFO-NOVA];
  - "obrigado", "agradeço", "ok" → [X-OBRIG] ("Disponha! Entraremos em contato assim que tivermos novidades!", texto do usuário). Nunca reforce que o caso está com o Henzo;
  - só "oi" ou "olá" → [X-OI].
- Os textos de prazo **não têm números**: o comercial não informou o prazo de retorno do Henzo nem o da proposta (25/11/2026). Não invente prazo. A única exceção é o [X-PAGTO] ("em até 7 dias úteis" depois da escritura), confirmado pelo comercial.
- Figurinha sozinha não recebe resposta. Segundo o usuário, o Broker não manda a mensagem padrão quando a IA não responde (ainda não testado: M5).
- Roteiro de testes: `Testes/Roteiro_1_V2aV5.md`; a bateria seguinte, ainda não rodada, é `Testes/Roteiro_2_V5.md`. Validação da V2 e decisões do usuário: `Analises/2026-11-25_V2_Validacao_e_proposta_V3.md`.
- Pelo que se observou, o agente não recebe o número de telefone do contato (nenhum motivo de transferência cita o número), então regras do tipo "se for o número X" não são confiáveis.
- Para reiniciar um teste:
  1. No Broker, use "Devolver para IA" (volta para a Triagem V2).
  2. Mande `#novo`. A Triagem V2 ignora o que veio antes e responde com o [BV].
  3. Mande a mensagem de teste.
- O suporte do Broker adicionou um botão de **limpeza de contexto** (24/11/2026). Ele é o jeito preferido de reiniciar; o `#novo` continua como alternativa.

## Acesso ao Broker para análise

- Painel: https://brokerchat.w3groupltda.com.br · API: https://api.brokerchat.w3groupltda.com.br (documentação OpenAPI em `/openapi.json`).
- O usuário deixa o Edge aberto e logado com depuração remota na **porta 9222**. Conecte via CDP (`http://127.0.0.1:9222/json/list` → WebSocket da página `brokerchat`). Execute JS com `Runtime.evaluate`.
- A API exige `Authorization: Bearer <token>`. O token não fica em `localStorage`: capture-o envolvendo `window.fetch` na página e esperando a próxima requisição (o app faz polling).
- Endpoints úteis (leitura):
  - `GET /inbox/chats?limit=100&offset=N`
  - `GET /admin/chats/{id}/mensagens?limit=500`
  - `GET /admin/chats/{id}/eventos` (transferências e motivos)
  - `GET /admin/agentes-ia?empresa_id=1`
  - `GET /admin/agentes-ia/modelos`
  - `GET /admin/consumo-ia`
  - `GET /admin/conexoes?empresa_id=1`
- **Somente leitura.** `PUT/POST/PATCH` (alterar agente, modelo, prompt ou conexão) só com confirmação explícita do usuário naquele momento.
- As conversas têm CPF e dados pessoais: salve as extrações só no scratchpad da sessão, nunca nesta pasta.
- Na análise, use `autor_tipo: "ia"` nas mensagens da conexão 10 e cruze com os eventos `transferencia_agente` e `transferencia_humano`.

## Pendências

- **Critérios de qualificação** e **lista de entes que a Recall compra**: o comercial não vai responder por enquanto (25/11/2026). Todo caso completo vai para o Henzo; pergunta sobre ente específico → [X-ENTE] ("quem confirma é o Henzo").
- **Prazos reais** de retorno do Henzo e da proposta: sem resposta do comercial por enquanto.
- **Suporte do Broker:** tempo de agrupamento de mensagens picotadas (N11), resposta quebrada em várias mensagens (N4), mensagens que não chegaram ao Broker em 24 e 25/11 (H1, N5).
- **Testes não rodados:** E10 (cadência), O5 (mensagem do Henzo no histórico), O8 (conexão 10), grupo M e A10 (mídia real).
- Agente Instagram: o usuário pediu para não mexer por enquanto.
- Follow-up para quem não responde à primeira mensagem (cerca de 59% na V1): o usuário vai montar o fluxo no Broker. O follow-up **nunca** pode ser enviado a quem desistiu. Como a desistência não gera evento de transferência, o filtro precisa olhar o texto da última mensagem da IA ("respeito a sua decisão" ou "obrigada por avisar").

## Histórico

- **V1** (até 23/11/2026): Triagem e Credor no Terra; Herdeiro e as duas Parcerias no Luna 5.6. Problemas medidos em 63 conversas:
  - 59% dos leads não responderam à primeira mensagem;
  - 10 leads de anúncio foram parar na Parceria Geral ("você é investidor?");
  - os agentes no Luna encerravam logo depois de receber a transferência;
  - vazaram para o cliente `[POS-ENC] "..."`, `”】【` e o JSON da transferência;
  - o CPF foi pedido de novo para quem já tinha mandado;
  - advogada com precatório próprio foi tratada como parceira.
- **V2** (23/11/2026): todos os agentes no Terra (Instagram no gpt-6-luna). Prompts cerca de 30% menores. Mudanças:
  - `[BV]` explica o que a Recall faz e faz uma pergunta binária;
  - rota padrão CREDOR e nova mensagem `[ENGANO]`;
  - regra de primeira resposta após transferência;
  - CPF do herdeiro e do titular;
  - "ok" ao pedido de CPF encerra o atendimento;
  - novo agente Instagram.
- **V3** (25/11/2026): feita a partir dos testes de 24 e 25/11 (`Testes/Roteiro_1_V2aV5.md`) e das decisões do usuário em `Analises/2026-11-25_V2_Validacao_e_proposta_V3.md`. Todos no Terra. Prompts de 15 a 20% maiores. Mudanças:
  - pedido de atendente (inclusive "pode me ligar?") vem antes da desistência, em todos os agentes;
  - nova apresentação padrão, igual à da cadência;
  - o Credor atende três perfis novos: representante de titular vivo ([C3-TIT]), empresa ou cessão que quer vender ([C3-CNPJ]) e consulta de andamento (não é desistência);
  - o Herdeiro confirma o falecimento com a [H0] quando a pessoa só disse "do meu pai";
  - na Parceria Adv., honorários é pergunta obrigatória (o número de clientes é opcional); nova [A2-UF];
  - a Parceria Geral manda quem quer VENDER para o Credor;
  - "dúvida não é falta de resposta"; nome e CPF da pessoa valem entre atendimentos; quem tem dois perfis termina o roteiro atual antes de ser transferido;
  - novos textos: [X-ENTE], [X-DADOS], [X-CPF], [C3-CONF], [X-MANDAR], [X-ROBO-POS] e [X-CONFIANCA] nas Parcerias; o [X-COMO] explica a antecipação;
  - checagem final "ANTES DE ENVIAR, CONFIRA" no fim de cada prompt.
- **V4** (25/11/2026, em `Agentes/V4/`): feita a partir do teste da V3 (`Analises/2026-11-25_Relatorio_de_testes_V1aV3.md`) e de pedidos do usuário. O usuário liberou o **gpt-6.1-sol**. Agentes e modelos:
  - Triagem no Terra;
  - **Venda** no Sol: novo agente que junta Credor e Herdeiro, para acabar com o vaivém de transferências entre os dois;
  - Parceria Adv. no Terra;
  - Parceria Geral no Terra.

  Pedidos do usuário:
  - agentes mais diretos: roteiro de venda = documento → número do processo ou CPF (a esfera não é mais perguntada), e as Parcerias com 2 perguntas;
  - tom menos informal: sem "Que bom falar com você", "Que ótimo" etc.;
  - nunca oferecer "se não souber", "pode mandar depois", "se preferir";
  - avisar que a análise interna pode ser um pouco lenta (no [FIM-HENZO] e na cobrança);
  - no Venda, priorizar o arquivo ou a foto do processo e, só sem ele, o número do processo ou o CPF.

  Correções:
  - novo [FIM-ATEND] para o pedido de atendente, que é escrito mesmo depois de encerramento ou desistência. A V3 deixou sair a mensagem padrão do Broker;
  - "depois eu vejo" ao pedido de dado = envia depois, não desistência;
  - [X-NAOSEI] só quando a pessoa pergunta se tem precatório.
- **V5** (26/11/2026, em `Agentes/V5/`): o usuário relatou que a V4 "se perdia na conversa". A V5 volta à lógica e aos agentes da V3 (Credor e Herdeiro separados; o Venda foi descartado) e mantém as melhorias da V4 (documento primeiro, sem esfera, tom formal, sem "se não souber", aviso de análise lenta, [FIM-ATEND], "depois eu vejo" = envia depois, desistência ampliada, pêsames só para pais/filhos/cônjuges, investidor com reunião e [FIM-INV], "fui contactada pela empresa" → Credor). Modelos: Credor e Herdeiro no Sol; Triagem e Parcerias no Terra.
  - Todos os prompts seguem a mesma divisão em 12 partes (`=== N. ... ===`): quem você é · como escrever · leitura da conversa · o que fazer agora (lista de prioridade, primeira regra que se aplica) · roteiro · desistência · fim · transferência · fora do roteiro (engano, pós-encerramento, cobrança) · catálogo agrupado · exemplos · conferência final. Ao mudar uma regra comum, mude nos 5 prompts.
  - **Ajuste em 26/11/2026** (a pedido do usuário, na própria V5), depois do grupo Q do `Testes/Roteiro_1_V2aV5.md` (conversas reais que deram errado). Mudanças:
    - quem pergunta se a Recall TEM ou VENDE precatório recebe o `[VC-Q]`/`[VC-Q-B]` (vender ou comprar?);
    - "vc entendeu minha pergunta?" → `[X-REFAZ]`, nunca `[X-FORA]`;
    - "vocês ligaram, vocês têm os dados" → `[X-LIGAMOS]`, e o nome do titular vale como identificação;
    - o Credor nunca encerra sem caso;
    - a Parceria Geral não abre com `[X-FORA]` depois de uma transferência.
  - **Ajuste em 26/11/2026, tarde** (pedidos do usuário):
    - depois do encerramento, "ok", "certo" ou emoji ficam sem resposta; o `[X-OBRIG]` sai uma vez só;
    - a apresentação só sai na primeira resposta do especialista;
    - no pedido de atendente, o texto do `[FIM-ATEND]` vem escrito antes da ferramenta (no Sol, os agentes transferiam sem texto);
    - a Parceria Adv. não pergunta mais a OAB nem o estado: só o nome e os honorários;
    - as perguntas saem diretas, sem "Para o registro", "Para eu entender", "Para direcionar" e sem justificativa no fim;
    - no Credor e no Herdeiro, "sou advogado também" depois do encerramento recebe o `[X-ADV-Q]` ("Você gostaria de fazer uma parceria com a Recall?"); se sim, vai para a Parceria Adv.
  - No Broker, a Triagem V5 roda no **gpt-6.1-sol** (visto em 26/11), embora o arquivo se chame "6.1 Terra".
- **V0** (26/11/2026, em `Agentes/V0_curta/`): pedido do usuário, versão curta de cada agente com no máximo **8000 caracteres** (Triagem 7649, Credor 7952, Herdeiro 7995, Parceria Adv 7024, Parceria Geral 7795). Mesma lógica da V5, sem exemplos, sem conferência final e com o catálogo reduzido (2 textos de cobrança; X-CALC, X-ONDE, X-NAOSEI, X-CPF, X-DADOS, X-PRAZO-PROPOSTA, X-ROBO-POS e ENGANO-B fundidos ou retirados). A indicação de uso fica dentro do colchete do código (ex.: `[X-VALOR: valor, cálculo]`). O papel de transferência é o mesmo da V5.
