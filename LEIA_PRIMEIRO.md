# Leia primeiro: passagem da demanda "Atendimento por IA (Luiza)"

Data da passagem: **27/11/2026**. Este documento serve para duas pessoas:
- **quem assume a demanda**: o que existe, em que pé está e o que fazer agora;
- **o próximo Claude**: o mesmo, mais as regras de trabalho e as armadilhas do ambiente que até agora só existiam na memória local do Claude anterior.

O `CLAUDE.md` desta pasta continua valendo e traz o detalhe (regras de prompt, decisões do usuário, histórico versão a versão). Este arquivo é o resumo e o ponto de partida.

---

## 1. Em um minuto

- A Recall compra precatórios. Os leads chegam pelo WhatsApp e são atendidos por agentes de IA dentro do **BrokerChat** (o omnichannel da empresa). Para o cliente, todos os agentes são a **Luiza**.
- São 5 agentes de conversa: a **Triagem** classifica e transfere, sem escrever, para o **Credor** (precatório no próprio nome), o **Herdeiro** (titular faleceu), a **Parceria Adv.** (advogado parceiro) ou a **Parceria Geral** (empresa, fundo, investidor, captador). Cada um faz poucas perguntas e encerra dizendo que o caso vai para o **Henzo**, gerente comercial (humano). Existe também um agente Instagram (V2), que não foi ativado e que **não deve ser mexido** por enquanto.
- O trabalho é **escrever e testar prompts**. Não há código. Os arquivos desta pasta são colados à mão no Broker.
- A IA só transfere para humano quando a pessoa **pede** atendente, humano ou o Henzo. Em todo o resto, a conversa fica com a IA, e o Henzo acompanha pelo Inbox.

## 2. Onde as coisas estão

```
LEIA_PRIMEIRO.md                      este arquivo
CLAUDE.md                             contexto completo para o Claude (regras, decisões, histórico)
Agentes/V5/                           ★ versão em produção
Agentes/V0_curta/                     V5 resumida (≤ 8000 caracteres), criada em 26/11, sem teste registrado
Agentes/V1/ … V4/                     versões anteriores (só histórico; nunca apagar)
Agentes/V<n>/<Agente> - prompt.md     vai no campo "Prompt do sistema" do agente no Broker
Agentes/V<n>/<Agente> - transferencia.md   vai no campo "papel" (quando passar a conversa para este agente); limite de 480 caracteres
Testes/Roteiro_1_V2aV5.md             169 cenários + o registro de tudo que foi testado de 24 a 26/11
Testes/Roteiro_2_V5.md                próxima bateria: pendentes do Roteiro 1 + 200 cenários novos (nada rodado ainda)
Analises/                             relatórios em ordem de data
```

### Versões e modelos

O modelo saiu do nome do arquivo em 27/11. Ele fica registrado aqui:

| Versão | Data | Triagem | Credor | Herdeiro | Parceria Adv. | Parceria Geral | Observação |
|---|---|---|---|---|---|---|---|
| V1 | até 23/11 | 6.1 Terra | 6.1 Terra | 5.6 Luna | 5.6 Luna | 5.6 Luna | Luna encerrava na 1ª resposta |
| V2 | 23/11 | 6.1 Terra | 6.1 Terra | 6.1 Terra | 6.1 Terra | 6.1 Terra | + Instagram no 6 Luna |
| V3 | 25/11 | 6.1 Terra | 6.1 Terra | 6.1 Terra | 6.1 Terra | 6.1 Terra | |
| V4 | 25/11 | 6.1 Terra | **Venda** (Credor + Herdeiro) no 6.1 Sol | — | 6.1 Terra | 6.1 Terra | "se perdia na conversa"; descartada |
| **V5** | 26/11 | 6.1 Terra (arquivo) / **Sol no Broker** | 6.1 Sol | 6.1 Sol | 6.1 Terra (arquivo) | 6.1 Terra (arquivo) | Nos testes de 26/11, **todos rodaram no Sol**; no último teste, a Parceria Adv. foi trocada para o Terra |
| V0_curta | 26/11 | 6.1 Sol | 6.1 Sol | 6.1 Sol | 6.1 Sol | 6.1 Sol | Não testada |

Custo: o Sol tem peso 12 e o Terra, peso 6 (R$ 1 ≈ 1 milhão de tokens de plataforma). Com cerca de 50 conversas a cada 4 dias, o gasto fica em dezenas de reais por mês. O teto combinado é o Sol. Não use o `gpt-5.6-luna`.

## 3. Estado em 27/11/2026

**No Broker:** os 5 prompts da **V5** (conferidos iguais aos arquivos no fim do dia 26/11), menos o último ajuste da Triagem (ver abaixo). Confirme ao assumir: abra cada agente no Broker e compare com `Agentes/V5/`.

**Nos arquivos, mas ainda NÃO colado no Broker:**
- Triagem V5: quem fala por escritório de contabilidade ou cobrança, banco, fundo, gestora ou financeira vai para a **Parceria Geral**, mesmo depois do `[BV]`/`[BV-B]` (caso U3).

**Não confirmado:**
- se a Parceria Adv. voltou para o Sol depois do teste no Terra (U4);
- se a V0_curta chegou a ser colada no Broker (não há teste registrado dela).

## 4. O que fazer agora (por prioridade)

1. **Abrir chamado no suporte do Broker sobre a transferência para humano (R1/R2).** Quando a pessoa pede atendente, o agente deveria escrever o `[FIM-ATEND]` e transferir. O cliente recebe só "Nossa equipe responderá assim que possível." (ou nada). A falha acontece no Sol e no Terra, com o prompt já reforçado: o Broker parece descartar o texto da IA quando há transferência para humano. Pedido ao suporte: enviar o texto da IA nessa transferência ou permitir configurar a mensagem padrão. **É a falha mais visível para o cliente.**
2. **Colar a Triagem V5 atualizada** e retestar o R5 (contabilidade).
3. **Rodar a Parte 1 do `Testes/Roteiro_2_V5.md`** (R1 a R31): falhas abertas, ressalvas e cenários nunca rodados. Os mais importantes:
   - pingue-pongue entre agentes: R3 e R4 (dois perfis na mesma conversa) e R6 (comprador que fala de "processo");
   - mídia real (áudio, foto, PDF, figurinha): R22 a R31, nunca testados;
   - cadência (R19), histórico com mensagem do Henzo (R20) e conexão 10, que é a de produção (R21).
4. **Pegar as decisões que estão com o Marketing** (estão no Roteiro 2):
   - R11: "depois eu vejo" logo depois de um contorno é "mando depois" (encerra com o Henzo) ou desistência?
   - R14: no Herdeiro, manter "documento primeiro" quando o inventário já foi concluído?
   - Onde registrar informação nova para o Henzo (o CRM está desligado).
5. **Decidir entre V5 e V0_curta.** A V0 é a V5 resumida (sem exemplos e com menos textos). Se for usada, rode nela pelo menos o grupo Q e o Roteiro 2, Parte 1, antes de colocá-la em produção.
6. **Depois de alguns dias em produção**, medir com leads reais (conexão 10): a taxa de resposta à primeira mensagem (59% não respondiam na V1), a taxa de roteiro completo e o número de mensagens por conversa.

### Pendências que dependem de outras áreas

| Com quem | O quê |
|---|---|
| Comercial (Henzo) | critérios de qualificação; lista de entes que a Recall compra; prazo real de retorno e da proposta (hoje os textos não têm prazo de propósito) |
| Suporte do Broker | R1 (texto na transferência para humano); tempo de agrupamento de mensagens picotadas (N11); resposta quebrada em várias mensagens (N4); mensagens que não chegaram ao Broker em 24 e 25/11 (H1, N5) |
| Marketing | fluxo de follow-up para quem não responde, que **nunca** pode ir para quem desistiu (filtrar pela última mensagem da IA: "respeito a sua decisão" ou "obrigada por avisar"); desligar a ferramenta `registrar_lead` (gravava texto livre no CRM); conferir se a linha solta `$R` ainda existe nos prompts colados no Broker (ela existia na V2; os arquivos V5 não a têm) |

## 5. Como mudar um prompt

1. Mudança relevante = **nova pasta de versão** (`Agentes/V6/`), copiando os 10 arquivos da V5. Ajuste pequeno durante testes pode ir na própria V5, registrado no `CLAUDE.md` (seção Histórico).
2. Regra comum (estilo, desistência, pedido de atendente, pós-encerramento) muda **nos 5 prompts**. Todos seguem as mesmas 12 partes (`=== N. ... ===`).
3. Se mudar o prompt de um agente, revise o arquivo de transferência dele (campo "papel" no Broker), e vice-versa. Esse texto tem no máximo 480 caracteres, é escrito de forma excludente ("Não use para...") e nunca diz "qualquer assunto que não se encaixa".
4. Os prompts são **texto puro** (sem Markdown), porque são colados como estão. A extensão `.md` é só para abrir no editor.
5. O agente só usa textos do catálogo (códigos `[XX]`). Texto novo entra no catálogo de todos os agentes que podem usá-lo.
6. Cole no Broker, teste e registre o resultado no roteiro de testes (seção "Resultados — <data>").

## 6. Como testar

- **Número de teste:** até aqui, o "cliente" era o WhatsApp pessoal de quem conduziu a demanda (contato "Alexandre" no Broker), conversando com o número da Recall (+55 41 99186-1463). **Quem assume precisa definir o próprio número de teste** e trocá-lo no roteiro.
- **Reiniciar o contexto:** botão "Zerar contexto da IA" no chat do Broker. Ele **não** troca o agente: depois de zerar, reatribua a Triagem V5 em "Atribuir agente IA" (no cabeçalho do chat). A alternativa antiga é "Devolver para IA" + mandar `#novo`.
- **Método (pedido do Marketing):** cada cenário é uma conversa real, turno a turno, até o encerramento. Só depois zere. Registre como ⚠️ quando a Luiza deixar uma pergunta sem resposta.
- Teste também com histórico (cliente que volta, conversa que já passou por humano), não só com conversa nova.
- Os testes de 24 a 26/11 rodaram na **conexão 5** (Meta oficial). A produção é a **conexão 10** (uazapi). O mesmo número está nas duas, então cada conversa aparece duplicada no Inbox. Na análise de leads reais, use só a conexão 10.
- Leads reais têm CPF e dados pessoais. Extrações de conversa ficam fora desta pasta.

### Para o Claude: automação do teste pelo navegador

O Claude anterior conduzia os testes pelo Edge (WhatsApp Web + Broker), via CDP. Tudo isto é da máquina do responsável anterior: **confirme com a nova pessoa antes de repetir qualquer passo**.
- Edge com depuração em perfil separado: `msedge.exe --remote-debugging-port=9222 --user-data-dir="%LOCALAPPDATA%\EdgeDebugProfile" --no-first-run`. O Edge 158 ignora a porta no perfil padrão. **Nunca feche o Edge principal do usuário** (já aconteceu por engano).
- **Antes de enviar qualquer mensagem**, confira que o cabeçalho da conversa aberta no WhatsApp Web (`#main header`) é a da Recall (contém "9186-1463"). O WhatsApp era pessoal, e em 25/11 uma mensagem de teste foi enviada a um contato real porque a conversa aberta tinha mudado. Mantenha essa trava em qualquer script.
- A janela do Edge precisa estar visível (se estiver coberta, os diálogos do Broker travam). O aviso "Contexto zerado" cobre o botão ⋮: feche-o com `button[aria-label="Close toast"]`.
- O leitor do WhatsApp falha com links e emojis: confira no Broker. Às vezes uma mensagem sai do WhatsApp e não chega ao Broker.
- Os scripts (cdp.js, lib.js, step.js, zerar.js) ficavam no scratchpad da sessão e **não existem mais**: recrie-os.
- API do Broker: o `CLAUDE.md` descreve como capturar o token envolvendo o `fetch` da página, mas em 26/11 o classificador de segurança negou isso. O caminho que funcionou foi ler pela interface (WhatsApp Web + texto do chat no Broker).
- **Somente leitura no Broker.** Alterar agente, modelo, prompt ou conexão (PUT/POST/PATCH) só com a confirmação explícita da pessoa naquele momento.

## 7. Regras de prompt que mais pesaram

O resumo das lições (o detalhe está em "Princípios de prompt", no `CLAUDE.md`):
- **Catálogo fechado**: nada de texto livre, e o código `[XX]` nunca vai para o cliente.
- A apresentação ("Olá! Eu sou a Luiza, responsável pelo primeiro contato aqui na Recall.") sai **uma vez na conversa inteira** e sempre junto com uma pergunta.
- Quem recebe uma transferência **nunca encerra na primeira resposta**.
- Dúvida não encerra nem transfere: responde e segue o roteiro.
- Na dúvida sobre a rota, vai para o **Credor**. A Parceria Geral nunca recebe pessoa física nem lead de anúncio genérico.
- Desistência firme recebe `[DESIST]`, sem insistir duas vezes e sem transferir.
- Tom formal e direto: sem "Que ótimo", sem "se não souber", sem "pode mandar depois" e sem prazos inventados.
- Prompt mais curto e sem regras contraditórias obedece melhor que um modelo mais caro.

## 8. Renomeação de 27/11/2026 (antigo → novo)

Só os nomes mudaram. O conteúdo dos prompts é o mesmo.

| Antes | Agora |
|---|---|
| `Agentes/V3/<Agente>/V1/V1 - <modelo>.md` (Triagem: `6.1 Terra.md`) | `Agentes/V1/<Agente> - prompt.md` |
| `Agentes/V3/<Agente>/V2/...`, `.../V3/...` | `Agentes/V2/...`, `Agentes/V3/...` |
| `Agentes/V3/Instagram/V2 - 6 Luna.md` | `Agentes/V2/Instagram - prompt.md` |
| `Agentes/V4/<Agente>/V4 - <modelo>.md` | `Agentes/V4/<Agente> - prompt.md` |
| `Agentes/V5/<Agente>/V5 - <modelo>.md` | `Agentes/V5/<Agente> - prompt.md` |
| `.../V<n>_Transferencia.md` | `.../<Agente> - transferencia.md` |
| `Agentes/V0/<Agente>/V0 - 6.1 Sol.md` | `Agentes/V0_curta/<Agente> - prompt.md` |
| `testes.md` | `Testes/Roteiro_1_V2aV5.md` |
| `testes_v2.md` | `Testes/Roteiro_2_V5.md` |
| `Relatorio_Testes_IA.md` | `Analises/2026-11-25_Relatorio_de_testes_V1aV3.md` |
| `Analises/V1.md` | `Analises/2026-11-23_V1_Exemplos_de_leads.md` |
| `Analises/V2_Validacao_Proposta_V3.md` | `Analises/2026-11-25_V2_Validacao_e_proposta_V3.md` |

Os registros antigos dentro dos relatórios ainda citam os nomes antigos (ex.: `V2_Transferencia.md`). Use a tabela acima para achar o arquivo.
