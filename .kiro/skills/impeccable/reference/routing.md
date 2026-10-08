# Orientação sobre comandos

<a id="workflow-questions"></a>
## Perguntas de fluxo de trabalho

Dê conselhos sem executar comandos; o menu abaixo serve apenas para invocações sem argumento. Consulte as referências dos comandos relevantes conforme necessário para pré-requisitos e escopo. Inclua um link para a [documentação](https://impeccable.style/docs/) para o guia mais amplo do fluxo de trabalho. Se o usuário também pedir execução, atenda a esse pedido.

## Roteamento sem argumento: o menu sensível ao contexto

Leia isto quando o usuário invocar `/impeccable` sem nenhum argumento. Ele está perguntando "o que eu devo fazer?". Torne o menu sensível ao contexto em vez de estático.

A etapa de Setup já executou `impeccable context`. Se ela relatou `NO_PRODUCT_MD`, o projeto ainda não tem contexto capturado: abra o menu com `/impeccable init` como recomendação principal (com uma linha explicando o porquê) e ainda assim mostre o restante abaixo; não pule para o init silenciosamente. Caso contrário, execute `.kiro/skills/impeccable/scripts/impeccable signals` uma vez e leia o JSON resultante; depois, abra com os **2-3 próximos comandos de maior valor**, cada um com um motivo de uma linha extraído dos sinais, seguidos do menu completo (a tabela de Comandos do SKILL.md, agrupada por categoria). **Nunca execute um comando automaticamente; a recomendação é uma sugestão que o usuário confirma.**

Raciocine sobre os sinais; não há nenhuma pontuação a obedecer:

- `setup.hasDesign` falso enquanto `setup.hasCode` é verdadeiro → `document` (capturar o sistema visual).
- `critique.latest` é `null` → o projeto nunca passou por critique; para um projeto já configurado com uma superfície real, oferecer `/impeccable critique <surface>` é um padrão forte.
- `critique.latest` com `score` baixo ou `p0` / `p1` diferente de zero → `polish` (ele lê esse snapshot como seu backlog e o encerra quando estiver desatualizado ou resolvido).
- `git.changedFiles` apontando para uma única superfície → restrinja `audit` ou `polish` especificamente a esses arquivos, nomeando-os.
- `devServer.running` verdadeiro → `live` está disponível para iteração no navegador, e `generate` para execuções pontuais de variantes em um elemento nomeado; se for falso, não abra com nenhum dos dois. **`live`, `generate` e o `impeccable detect` incluído são exclusivos para web.** Se `setup.platform` for `ios`, `android` ou `adaptive`, não abra com nenhum deles; a sobreposição no navegador e o motor de regras de HTML não se aplicam a código de app nativo.
- Caso contrário, agrupe por intenção (construir algo novo / melhorar o que existe / iterar visualmente), adaptando à superfície atual e a `setup.platform`.

**Se `scan.targets` não estiver vazio e `setup.platform` não for `ios`/`android`/`adaptive`, execute `.kiro/skills/impeccable/scripts/impeccable detect --json <scan.targets joined by spaces>` uma vez** (o detector incluído rodando sobre arquivos locais: sem rede, sem npx; ele lê HTML/CSS, então pule-o em projetos nativos). `scan.via` informa o que eles são: `git-changes` (os arquivos de marcação/estilo na sua árvore com alterações, o conjunto mais relevante), `source-dir` (por exemplo, `src`, `app`), `html` ou `root`. Incorpore os achados às suas escolhas: muitos achados de qualidade / contraste → `audit` ou `polish`; uma família específica de desleixo → o comando correspondente (texto com gradiente ou eyebrows → `quieter` / `typeset`, paleta chapada ou cinza → `colorize`, e assim por diante). É um sinal real e atual, que vence o chute. Se o detect der erro ou se a árvore for grande e lenta, pule-o e recomende que o próprio usuário execute `audit`; nunca bloqueie a sugestão por causa dele.

Limite-se a 2-3 escolhas certeiras, com o comando exato a ser digitado. O menu continua sendo o recurso de reserva; a recomendação é o destaque.
