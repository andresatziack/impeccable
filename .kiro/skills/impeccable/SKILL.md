---
name: impeccable
description: Use quando o usuário quiser projetar, redesenhar, dar forma, criticar, auditar, polir, clarificar, destilar, robustecer, otimizar, adaptar, animar, colorir, extrair ou aprimorar uma interface frontend. Abrange sites, landing pages, dashboards, UI de produto, app shells, componentes, formulários, configurações, onboarding e estados vazios. Cobre revisão de UX, hierarquia visual, arquitetura de informação, carga cognitiva, acessibilidade, desempenho, responsividade, temas, antipadrões, tipografia, fontes, espaçamento, layout, alinhamento, cor, movimento, microinterações, copy de UX, estados de erro, casos extremos, i18n e design systems ou tokens reutilizáveis. Também para designs sem graça que devem ficar mais ousados ou encantadores, designs barulhentos que pedem mais discrição, iteração ao vivo de UI no navegador ou efeitos visuais ambiciosos e tecnicamente extraordinários. Em inglês — design, UI, UX, frontend, landing page, dashboard, redesign, accessibility. Não serve para tarefas só de backend ou sem UI.
version: 4.5.0
license: Apache 2.0
---

Esta skill dá a você as ferramentas e a permissão para criar um design que mereça ser chamado de artesania fora da curva: se antes o seu trabalho de design seria seguro, tímido e comedido, agora você aborda cada tarefa de design como um diretor de design premiado, com compreensão impecável do que torna um trabalho de design excepcional: código de nível de produção, criatividade máxima, um ponto de vista claro, entendimento profundo das necessidades do cliente e dos usuários e um acabamento excepcional.

Princípios centrais:
- Vá com tudo. Sem rodeios, sem atalhos. A entrega deve estar completa (exceto os assets que o usuário precisa fornecer).
- Sonhe grande e com ousadia. Trabalho distinto, bonito, excepcional e altamente inspirador.
- Verifique em passadas limitadas, não em loop, e o teto vale para o ciclo inteiro: capturas de tela, varreduras de defeitos, microedições e rebuilds, tudo junto. Construa por completo, inspecione uma vez com uma rodada em lote (desktop e mobile juntos na web; as classes de dispositivo entregues em uma plataforma nativa), corrija tudo o que ela mostrar em um único lote, confirme com no máximo mais uma rodada e pare de polir. Um autoQA sem fim queima o dinheiro do usuário fazendo pior o que as etapas de finalização fazem melhor.

## Configuração

1. Rode `<skill-base-dir>/scripts/impeccable context` uma vez por sessão, onde `<skill-base-dir>` é o diretório que contém este SKILL.md (a pasta da skill, não a raiz de um plugin dois níveis acima); mantenha o cwd no projeto do usuário. Esse diretório base resolve todo comando `.kiro/skills/impeccable/scripts/impeccable <verb>` desta skill e de suas referências, e `.kiro/skills/impeccable/scripts` é o fallback apenas quando o runtime não informa nenhum diretório base. Em um shell Windows sem `sh`, chame `.kiro/skills/impeccable/scripts/impeccable.cmd` em vez disso. O launcher (inicializador) executa um binário autocontido que acompanha o launcher ou é baixado uma única vez na primeira execução; não é necessário Node nem outro runtime. Passe um arquivo-fonte ou rota nomeado como `--target <path>`. Ele carrega PRODUCT.md, DESIGN.md, o briefing da superfície correspondente e as orientações de plataforma nativa quando aplicável; siga as diretivas dele e não o execute novamente.
2. Carregue o playbook da solicitação: a referência da tabela de comandos para um subcomando explícito/implícito, ou [reference/new-work.md](reference/new-work.md) para uma nova superfície ou um mundo visual substituto. Inspecione a verdade visual do alvo e do design atual antes de editar. Quando o app não puder rodar, comece pelos goldens de regressão visual ou fixtures de captura de tela commitados; verifique o alvo e a atualidade em relação aos tokens, CSS, componentes ou assets atuais, resolva conflitos e compare as capturas de tema/variante.
3. Depois de resolver a análise e a direção, leia [reference/craft-floor.md](reference/craft-floor.md) imediatamente antes de qualquer edição de UI, inclusive pequenos refinamentos. Ele traz o padrão mínimo de qualidade, as proibições absolutas e os reflexos que nenhum detector capta. Não o carregue para trabalho apenas de planejamento.

**Launcher indisponível:** em caso de recusa ou falha, envie uma mensagem separada **antes da próxima chamada de ferramenta**: “O carregamento de contexto não rodou; vou ler diretamente o contexto existente do projeto.” Em seguida, leia PRODUCT.md e DESIGN.md existentes sem inventar o contexto que falta, siga os passos 2–3 aplicáveis e continue usando as ferramentas permitidas. Isso vale para planejamento e edição; a falha do launcher, por si só, não bloqueia nenhum dos dois.

## Como projetar

- **O briefing vence.** Respeite estéticas, épocas, materiais, fontes e paletas fixados, mesmo quando conflitarem com um aviso de padrão saturado. Redirecionar um briefing claro para o seu gosto é fracasso.
- **Refinamento preserva; redesign substitui.** O refinamento mantém a identidade, o comportamento, a copy do design atual e tudo o que está fora do escopo. Pergunte antes de substituir copy factual ou acrescentar afirmações. O redesign mantém a verdade do produto, o conteúdo, a função, as affordances nativas e as restrições, mas trata o visual antigo como evidência e antirreferência; escolha um mundo substituto em new-work e substitua o DESIGN.md. Nunca fique no meio-termo polindo o visual descartado.
- **Símbolos carregados ficam fora da decoração.** O mundo de um tema não autoriza emblemas ligados a militarismo, supremacismo ou movimentos de ódio como motivos, insígnias ou ornamentos, como os raios da bandeira do Sol Nascente, a bandeira de batalha confederada ou as insígnias da era nazista e suas variantes estilizadas; recorra às formas neutras desse mundo. Conteúdo que documenta tal símbolo como fato permanece como está.
- **Autoridade visual é evidência, não um nome de arquivo.** A ausência de DESIGN.md, por si só, não torna um projeto greenfield; new-work decide se preserva, expande ou substitui o mundo existente.

## Modos

O modo nomeia como é o sucesso do visitante nesta superfície.

- **Persuade (persuadir):** o visitante decide e age; o design é o produto. Landing pages, marketing, campanhas, preços. Conquiste atenção e ação. Entregue imagens reais quando o briefing precisar; siga o mundo definido, não o hábito da categoria.
- **Operate (operar):** o visitante conclui uma tarefa. UI de app, dashboards, editores, admin, configurações, ferramentas. Escaneabilidade, consistência, expectativas nativas e o cenário real de uso superam a expressão. A marca vive nos detalhes precisos.
- **Read (ler):** o visitante entende algo. Documentação, artigos, guias, ajuda, changelogs. Estruture para a compreensão e, depois, faça a experiência de leitura valer a permanência.
- **Experience (experienciar):** o visitante está dentro da própria obra. Portfólios, galerias, vitrines. Deixe o artefato conduzir desde a primeira viewport; a interface recua.

Escolha o modo a partir da superfície solicitada, não do produto, e persista-o apenas no briefing dessa superfície. A landing page de uma ferramenta continua sendo Persuade; a documentação de uma maison de moda continua sendo Read; um índice de documentação é Read, não Persuade. Veja [new-work.md](reference/new-work.md) para novas superfícies e [operate.md](reference/operate.md) para orientações mais aprofundadas de Operate/Read.

## Comandos

| Command | Categoria | Descrição | Referência |
|---|---|---|---|
| `craft [feature]` | Construir | Alias obsoleto para uma solicitação comum de new-work | [reference/craft.md](reference/craft.md) |
| `shape [feature]` | Construir | Planejar UX/UI antes de escrever código | [reference/shape.md](reference/shape.md) |
| `init` | Construir | Registrar o contexto duradouro do produto em PRODUCT.md | [reference/init.md](reference/init.md) |
| `document` | Construir | Gerar DESIGN.md a partir do código existente do projeto | [reference/document.md](reference/document.md) |
| `extract [target]` | Construir | Extrair tokens e componentes reutilizáveis para o design system | [reference/extract.md](reference/extract.md) |
| `critique [target]` | Avaliar | Revisão de design de UX com pontuação heurística | [reference/critique.md](reference/critique.md) |
| `audit [target]` | Avaliar | Verificações de qualidade técnica (a11y, desempenho, responsivo) | [reference/audit.md](reference/audit.md) · nativo: [reference/audit.native.md](reference/audit.native.md) |
| `polish [target]` | Refinar | Passada final de qualidade antes de entregar | [reference/polish.md](reference/polish.md) |
| `bolder [target]` | Refinar | Amplificar designs seguros ou sem graça | [reference/bolder.md](reference/bolder.md) |
| `quieter [target]` | Refinar | Suavizar designs agressivos ou superestimulantes | [reference/quieter.md](reference/quieter.md) |
| `distill [target]` | Refinar | Reduzir à essência, remover complexidade | [reference/distill.md](reference/distill.md) |
| `harden [target]` | Refinar | Pronto para produção: erros, i18n, casos extremos | [reference/harden.md](reference/harden.md) |
| `onboard [target]` | Refinar | Projetar fluxos de primeira execução, estados vazios, ativação | [reference/onboard.md](reference/onboard.md) |
| `animate [target]` | Aprimorar | Adicionar animações e movimento com propósito | [reference/animate.md](reference/animate.md) |
| `colorize [target]` | Aprimorar | Adicionar cor estratégica a UIs monocromáticas | [reference/colorize.md](reference/colorize.md) |
| `typeset [target]` | Aprimorar | Melhorar a hierarquia tipográfica e as fontes | [reference/typeset.md](reference/typeset.md) |
| `layout [target]` | Aprimorar | Corrigir espaçamento, ritmo e hierarquia visual | [reference/layout.md](reference/layout.md) |
| `delight [target]` | Aprimorar | Adicionar personalidade e toques memoráveis | [reference/delight.md](reference/delight.md) |
| `overdrive [target]` | Aprimorar | Ir além dos limites convencionais | [reference/overdrive.md](reference/overdrive.md) |
| `clarify [target]` | Corrigir | Melhorar a copy de UX, rótulos e mensagens de erro | [reference/clarify.md](reference/clarify.md) |
| `adapt [target]` | Corrigir | Adaptar para diferentes dispositivos e tamanhos de tela | [reference/adapt.md](reference/adapt.md) · nativo: [reference/adapt.native.md](reference/adapt.native.md) |
| `optimize [target]` | Corrigir | Diagnosticar e corrigir o desempenho da UI | [reference/optimize.md](reference/optimize.md) |
| `live` | Iterar | Modo de variantes visuais: selecione elementos no navegador e itere sobre alternativas | [reference/live.md](reference/live.md) |
| `generate [n] [action] [element]` | Iterar | Variantes, versões ou alternativas de um elemento nomeado para escolher no navegador ao vivo; sem seleção manual | [reference/generate.md](reference/generate.md) |

Roteamento:

- **Sem argumento:** leia [routing.md](reference/routing.md) e apresente o menu sensível ao contexto dele; nunca execute um comando automaticamente.
- **Solicitação explícita ou claramente implícita para executar um comando:** carregue a referência dele (a variante nativa em plataformas nativas) e siga-a. Pergunte uma vez se dois comandos se encaixarem.
- **Pergunta sobre fluxo de trabalho ou escolha de comando:** leia [Perguntas de fluxo de trabalho](reference/routing.md#workflow-questions).
- **Caso contrário:** trate a solicitação como trabalho geral de design. Sem PRODUCT.md, uma nova superfície ou um mundo substituto passa por init e depois por new-work; um refinamento pontual de código existente prossegue sobre a implementação atual conforme `impeccable context` orientar, oferecendo init depois em vez de ficar bloqueado por ele.
- `teach` é alias de `init`. `craft` é um alias obsoleto para new-work comum e não acrescenta nada. `shape` cuida da descoberta da tarefa e só entra em new-work para decisões de mundo visual e de conceito da superfície.

Depois que init gravar o PRODUCT.md, retome sem rodar `impeccable context` de novo; o próprio init carrega a referência da plataforma nativa quando a plataforma registrada é `ios`, `android` ou `adaptive`.

**Pin / Unpin:** `.kiro/skills/impeccable/scripts/impeccable pin <pin|unpin> <command>` cria ou remove um atalho `/<command>` independente. Relate o resultado do script de forma concisa; em caso de erro, repasse o stderr literalmente.

**Hooks:** `/impeccable hooks <on|off|status|ignore-rule|ignore-file|ignore-value|reset>` gerencia o hook do detector de design deste projeto (executa o detector automaticamente após edições em arquivos de UI e apresenta os achados). Carregue [reference/hooks.md](reference/hooks.md) quando o usuário o invocar com qualquer argumento.

**Doctor:** `/impeccable doctor` relata e corrige divergências entre os artefatos do Impeccable deste projeto (PRODUCT.md, DESIGN.md e seu arquivo auxiliar, configuração, briefings de superfície, o hook) e o que esta versão lê. Carregue [reference/doctor.md](reference/doctor.md) quando o usuário o invocar ou quando perguntar o que está desatualizado, obsoleto ou precisa ser atualizado. Uma diretiva `CONTEXT_STALE` na saída da Configuração é o subconjunto barato do mesmo relatório; aja sobre ela ali, conforme as próprias instruções dela, em vez de rodar doctor sem que peçam.

**Nunca corrija divergências como efeito colateral de uma tarefa de design.** Um achado `CONTEXT_STALE` é relatado, não tratado, a menos que o usuário peça. A única exceção é um achado marcado como `auto`, que a próxima gravação nesse arquivo executa de qualquer forma.
