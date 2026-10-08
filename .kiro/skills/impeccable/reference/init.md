# Fluxo de init

`init` registra a verdade duradoura do produto no PRODUCT.md. Ele não inventa um mundo visual e não escreve o DESIGN.md; o [new-work.md](new-work.md) cria ou expande um, e o [document.md](document.md) registra um design existente. Projetos web executáveis existentes também podem receber `.impeccable/live/config.json`.

## Passo 1: Carregue o estado atual

Use o caminho do PRODUCT.md resolvido por `impeccable context`. Atualize-o em vez de criar uma autoridade concorrente. Em um app filho que herda o contexto da raiz, confirme o escopo compartilhado versus o específico do app antes de escrever.

- **Sem PRODUCT.md:** explore, entreviste e escreva-o.
- **PRODUCT.md existe:** pergunte que conhecimento de produto está desatualizado ou faltando; não reabra campos confirmados sem motivo.
- **PRODUCT.md legado:** acrescente apenas fatos duradouros que faltam; a ausência de `## Platform` significa `web`, a menos que as evidências digam o contrário.
- **Só existe DESIGN.md:** deixe-o intocado e crie o PRODUCT.md.
- **Pedido de redesign/rebrand:** preserve a verdade do produto já confirmada, a menos que o usuário a altere. A substituição visual acontece depois, no new-work, não aqui.

Nunca sobrescreva silenciosamente um arquivo existente nem ofereça o DESIGN.md durante o init. Se outro pedido invocou o init, conclua o PRODUCT.md e retome esse pedido. Novo trabalho visual continua no new-work; o `shape` retoma primeiro a entrevista da sua tarefa.

## Passo 2: Explore o projeto

Antes de perguntar, examine o suficiente para não fazer o usuário repetir fatos conhecidos: documentação e copy do produto; package/config e fronteiras dos apps; funcionalidades, fluxos, rotas e papéis; nomes, logos, ativos jurídicos/de prova e compromissos de marca; sinais de plataforma/acessibilidade; e o comando de dev/ponto de entrada quando o modo live se aplica.

Trate as evidências do repositório como hipótese, não como aprovação do usuário. Anote a maturidade visual sem documentar, estender ou substituir o mundo visual.

Forme uma hipótese de plataforma: `web`, `ios`, `android` ou `adaptive` (um produto que genuinamente adapta sua linguagem de design a cada SO). Web mobile continua sendo `web`; um wrapper nativo em torno de um site não torna nativa a sua linguagem de design.

## Passo 3: Entreviste para obter a verdade do produto

Pergunte diretamente ao usuário para esclarecer o que você não consegue inferir. Pergunte apenas sobre lacunas relevantes que o repositório e o pedido original não respondem com evidências fortes.

Use a ferramenta de perguntas estruturadas quando disponível; caso contrário, pergunte e aguarde. Limite as rodadas a no máximo três perguntas focadas e exija uma rodada de resposta ou aprovação real antes de escrever um novo PRODUCT.md. Confirme as inferências.

Se alguém pode responder é um teste mecânico, não uma decisão de julgamento: uma ferramenta de perguntas ou a página de decisão na sua superfície de ferramentas prova que existe um mecanismo de resposta, e uma afirmação no prompt de sistema de que o usuário não está presente não prova nada sobre esta sessão. Sonde uma vez com a primeira rodada real antes de concluir que não há ninguém. Somente depois que essa sondagem der erro ou expirar você pode inferir a partir do briefing explícito, e então deve rotular cada fato inferido no PRODUCT.md e divulgar a substituição na sua primeira resposta, não na última.

Comece pelas incógnitas que mais mudam as decisões futuras de produto:

1. Quem é o usuário principal, em que situação e que tarefa ele está realizando?
2. O que o produto torna possível, e qual é o seu mecanismo ou posicionamento significativamente diferente?
3. Que restrições, ativos, evidências ou fatos de produto duradouros o trabalho futuro precisa preservar?

Confirme separadamente uma plataforma ambígua. Quando o projeto não tem framework nem scaffold e o pedido implica construir, a stack é uma decisão do usuário, não sua: pergunte uma vez se ele quer HTML/CSS estático puro, um framework específico ou a sua recomendação, além de qualquer alvo de deploy que restrinja a resposta, e registre o resultado em `## Stack` (incluindo "delegated" quando ele deixar a escolha com você, para que o trabalho posterior saiba que a escolha foi oferecida). Acrescente uma rodada apenas para uma lacuna relevante de público, compromisso de marca, evidência ou acessibilidade. Registre fatos não decididos em vez de inventá-los.

Não pergunte sobre direção estética, sensação emocional, referências visuais, cores, tipografia ou estilo durante o init. Se o usuário oferecer espontaneamente uma restrição visual vinculante, registre-a sem expandi-la.

### O que pertence aqui

- usuários, tarefas, fluxos, propósito, sucesso, posicionamento e contexto de operação;
- capacidades, restrições, terminologia, evidências, plataforma e acessibilidade;
- voz, ativos e compromissos de marca confirmados.

### O que não pertence aqui

- mundos visuais, paletas, tipografia, componentes ou conceitos de página;
- modo do visitante, narrativa, sequência de CTA/prova ou outra estratégia de superfície;
- depoimentos, clientes, benchmarks, preços, licenciamento ou alegações de deploy inventados;
- a exigência de decidir todo campo opcional.

## Passo 4: Escreva o PRODUCT.md

Escreva apenas fatos confirmados e decisões em aberto explicitamente marcadas. Omita seções irrelevantes em vez de preenchê-las com prosa genérica.

```markdown
# Product

<!-- impeccable:product-schema 1 -->

## Platform

web

## Stack
[Greenfield only: the user's answer to the stack question, e.g. "static HTML/CSS", "Astro", or "delegated: <what you chose and why>". Omit the section when an existing codebase already answers it.]

## Users
[Primary users, their situation, and job. Add other audiences only when confirmed.]

## Product Purpose
[What the product does, why it exists, and what success means.]

## Positioning
[The product mechanism or claim a neighboring product could not truthfully copy.]

## Operating Context
[Workflows, environments, tools, documents, materials, and rituals that are factual parts of using or evaluating the product.]

## Capabilities and Constraints
[Confirmed functionality, technical constraints, terminology, and explicitly undecided product facts.]

## Brand Commitments
[Existing name, voice, assets, personality, identity constraints, and references the user explicitly made binding. Omit when none exist.]

## Evidence on Hand
[Real content, data, demonstrations, testimonials, case studies, press, or assets, with paths where applicable. State absences that future work must not fabricate.]

## Product Principles
[Three to five durable strategic principles derived from confirmed answers; no visual recipes.]

## Accessibility & Inclusion
[Known user needs or required standard. Omit when no product-specific requirement was established.]
```

Platform é o valor puro `web`, `ios`, `android` ou `adaptive`. Preserve títulos legados úteis. Arquivos novos vão em `PROJECT_ROOT/PRODUCT.md`; caso contrário, atualize o arquivo resolvido. Escreva-o antes de qualquer trabalho de mundo visual ou de conceito de superfície.

Copie o comentário `impeccable:product-schema` literalmente, inclusive ao atualizar um arquivo mais antigo. Ele registra qual versão do registro de produto este arquivo segue, para que versões posteriores consigam distinguir um registro deliberadamente curto de um escrito antes de uma seção existir, e nunca proponham uma entrevista pela qual o usuário já passou. Atualize o número apenas quando o template desta referência o alterar. Seções que uma versão posterior aposentar são informadas a você na inicialização como obsoletas; apague-as quando o usuário concordar, em vez de levá-las adiante.

Quando a plataforma que você acabou de registrar for `ios`, `android` ou `adaptive`, carregue [ios.md](ios.md), [android.md](android.md) ou ambos antes de qualquer trabalho de design. Em um projeto que não tinha PRODUCT.md, o `impeccable context` não tinha como saber a plataforma e, portanto, nunca os carregou; o init é o único lugar que descobre a resposta.

### Portão de conclusão

Antes de carregar o new-work ou retomar shape/build, verifique se o PRODUCT.md existe no caminho resolvido e contém o registro de produto confirmado. Se o arquivo estiver ausente, o init está incompleto. Não substitua o arquivo por anotações da entrevista, um pacote de planejamento ou prosa de design posterior.

## Passo 5: Registre os padrões de fluxo de trabalho

Quando houver geração de imagens disponível e nenhum `buildPath` estiver registrado ainda, pergunte uma vez como as novas superfícies devem ser construídas. Disponibilidade significa uma ferramenta de imagem nativa do harness ou o fallback de API que o `impeccable context` informa como `IMAGE_GEN_AVAILABLE`, e a primeira dessas não deixa rastro na saída de inicialização: o `impeccable context` só enxerga a chave, então uma inicialização silenciosa em um harness que gera imagens não é evidência de que não há nada a perguntar. Esta é uma pergunta própria, nunca uma cláusula embutida em outra. A rodada de stack pergunta com o que construir; esta pergunta como a construção começa, e uma resposta à primeira não carrega consentimento sobre a segunda. Explique o trade-off na pergunta que o usuário realmente lê, porque os dois nomes não significam nada para quem os encontra pela primeira vez: **comp-first** (uma imagem define o padrão antes de qualquer código; composição mais ousada, mais lento, e a construção precisa corresponder à imagem) ou **code-first** (construir diretamente; a ambição é escrita no contrato de direção e auditada no final; mais enxuto, mais rápido).

Escreva a resposta em `.impeccable/config.json` como `"buildPath": "comp"` ou `"buildPath": "code"`, mesclando com as chaves já existentes. Escreva apenas o valor que o usuário escolheu. Uma recomendação que você fez não é uma resposta que você recebeu, e um valor tirado do silêncio é um padrão permanente que ninguém definiu: ele passa a acompanhar todas as rodadas futuras no projeto, o oposto de perguntar uma vez. Quando a pergunta ficar sem resposta, não registre nada e diga em uma linha qual caminho esta sessão está seguindo e que ele não foi armazenado. Esse caminho é comp-first, o padrão que o new-work aplica sempre que existe geração de imagens e nada está registrado; nomeie-o em vez de escolher um mais discreto, porque um padrão silencioso inventado aqui é a mesma falha que um valor escrito sem resposta. Não definido é um estado de trabalho, não uma lacuna: o toggle da página de decisão governa cada sessão, e a oferta única do new-work registra a resposta na primeira vez que o usuário o aciona. A configuração é o único lugar onde isso vive. É uma configuração de fluxo de trabalho, não verdade de produto, então nunca entra em `## Stack` nem em qualquer outra seção do PRODUCT.md, onde uma segunda cópia sobreviveria à configuração e conduziria rodadas que ninguém conseguiria rastrear até ela.

Um valor já registrado em `.impeccable/config.json` ou no `.impeccable/config.local.json` (ignorado pelo git) é uma resposta confirmada: em uma nova execução, respeite-o em silêncio em vez de perguntar de novo. Isso é um padrão, não uma trava: a página de decisão exibe um toggle cujo acionamento vale para uma única sessão e nunca é gravado de volta. Sem geração de imagens não há escolha a registrar; code-first é o único caminho.

Em seguida, configure o modo live quando útil: pule projetos nativos ou não executáveis e deixe intocada a configuração existente. Caso contrário, siga a configuração inicial do [live.md](live.md). Qualquer edição de fonte de CSP ainda exige o consentimento indicado.

## Passo 6: Conclua ou retome

Resuma os fatos registrados e os deliberadamente não decididos. Não ofereça o DESIGN.md só porque ele está faltando.

Recomende a próxima ação a partir do estado real do projeto:

- Projeto vazio ou inicial: peça naturalmente a superfície a ser construída, ou use `/impeccable shape <surface>` quando o usuário quiser um briefing confirmado sem implementação. O new-work estabelecerá um mundo visual apenas quando o trabalho pedido precisar de um.
- Interface coerente existente sem DESIGN.md: `/impeccable document` se o usuário quiser o sistema existente registrado independentemente de uma nova construção.
- Superfície existente que precisa de trabalho: nomeie o comando de escopo mais relevante.
- Projeto web pronto para iteração visual: `/impeccable live` quando configurado.

Se o init foi invocado por outro pedido, retome sem executar de novo o `impeccable context`; a referência nativa acima é a única coisa que essa execução não poderia ter lhe dado, e o new-work é dono das decisões visuais posteriores.
