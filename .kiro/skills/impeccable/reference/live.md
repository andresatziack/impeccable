Modo live interativo de variantes: selecione elementos no navegador, escolha uma ação de design e receba variantes HTML+CSS geradas por IA, trocadas a quente via HMR do servidor de desenvolvimento.

## Pré-requisitos

Um servidor de desenvolvimento em execução com HMR (Vite, Next.js, Bun etc.) OU um arquivo HTML estático aberto no navegador. Se a porta padrão do servidor de desenvolvimento estiver ocupada, é muito provável que o app JÁ esteja rodando; teste a URL padrão antes de iniciar um segundo servidor.

A edição live exige um checkout local; injeção em sites de produção já implantados (incluindo HTTPS) não é suportada. Para inspecionar produção, use `.kiro/skills/impeccable/scripts/impeccable detect <url>` ou a extensão de navegador, não o helper live. Não desative a segurança do navegador nem enfraqueça o CSP de produção para habilitar o modo live.

## O contrato (leia uma vez)

Execute em ordem. Nenhuma etapa pulada, nenhuma etapa reordenada. Toda saída de ferramenta no modo live pode trazer um campo `_instructions`: ele é a próxima etapa oficial para aquela situação exata, com ids e caminhos reais substituídos; quando conflitar com o que você lembra deste documento, `_instructions` prevalece.

1. `impeccable live`: inicialização. Se a solicitação nomear ou implicar um arquivo, rota ou app dentro de um monorepo, deduza o caminho concreto e execute `.kiro/skills/impeccable/scripts/impeccable live --target <path>` em vez disso; depois execute o restante desta sessão live a partir do `projectRoot` retornado. A inicialização resolve a raiz do app a partir dos arquivos de configuração do servidor de desenvolvimento e a persiste em `.impeccable/live/roots.json`; todo helper se reancora a esse manifesto ao iniciar (um cwd errado não consegue bifurcar o estado da sessão), PRODUCT.md / DESIGN.md são descobertos subindo até a raiz do git, e argumentos relativos de helper como `--file` são resolvidos contra a raiz do app.
2. Abra a URL do app que serve `pageFile` (deduza a partir de `package.json`, da documentação, da saída do terminal ou de uma aba aberta). Nunca use `serverPort`; ele é o helper, não o app. **Cursor:** `browser_navigate` para essa URL antes de iniciar o polling; não pule. **Outros harnesses:** use a ferramenta de navegador disponível; se a URL for incerta, pergunte ao usuário uma vez.
3. Loop de polling com o timeout longo padrão (600000 ms). Execute `impeccable live-poll` de novo imediatamente após cada evento ou `--reply`; o Codex executa esse poll único em primeiro plano. Nunca passe um `--timeout=` curto. A **marca Impeccable** da barra global fica esmaecida com um ponto âmbar pulsante quando nada está fazendo polling em `/poll`; reinicie `impeccable live-poll` para reconectar.
4. Em `generate`: reutilize `event.scaffold` quando presente; leia a captura de tela se houver; carregue a referência da ação; entregue as variantes; `--reply done`; faça polling de novo. Gere nesta thread: você já tem os tokens e o layout do projeto. A pré-visualização do overlay É o canal de verificação; não tire capturas de tela, não renderize de novo nem faça QA das variantes entre generate e accept. Aplique por construção, enquanto escreve, os pisos de contraste, espaçamento e tipografia do craft-floor; a verificação completa roda uma vez no accept, sobre a variante escolhida.
5. Em `steer`: leia a mensagem e `pageUrl`; faça o trabalho; `--reply steer_done`; faça polling de novo. Sem confirmação de recebimento.
6. Em `accept` / `discard`: o script de poll executa `impeccable live-accept`, confirma a entrega e imprime `_completionAck`. Accepts/discards simples são terminais imediatamente; accepts com carbonize permanecem recuperáveis até que `impeccable live-complete --id EVENT_ID` seja executado. Termine essa limpeza antes de fazer polling de novo.
7. Se for interrompido, execute `impeccable live-status` ou `impeccable live-resume` antes de tentar adivinhar. O journal em `.impeccable/live/sessions/` é canônico e reproduz o trabalho não confirmado após um reinício do helper; o `live.js` injetado se reconecta quando a página é reaberta. Recorra ao loop de edição direta somente quando `impeccable live-resume` informar que não há sessão ativa, nunca porque as desconexões pareceram frequentes.
8. Em `exit`: execute a limpeza descrita no final.

Política por harness:
- **Claude Code**: execute o poll como uma **tarefa em segundo plano** (sem timeout curto); o harness notifica você ao concluir. Não bloqueie o shell.
- **Cursor**: poll **único** em um **terminal em segundo plano** com notificação em `"type":"(steer|generate|accept|discard|manual_edit_apply|variant_mount_failed|prefetch|exit)"`; trate, `--reply`, reinicie o poll. **Não** use `--stream` no Cursor (medido ~5 s de captação contra menos de um segundo no poll único).
- **Codex**: poll único padrão em uma **sessão exec em primeiro plano com yield**. Sem `&`, sem `--stream`, nunca deixe o Live sem um poll ativo em primeiro plano. Iniciar o poll não basta: ATENDA-o (continue lendo a sessão exec até ela retornar um evento). Nunca anuncie "aguardando o usuário" e fique ocioso; um poll com yield que ninguém lê é uma sessão morta, e o Go do usuário fica sem resposta.
- **Outros harnesses**: poll único em primeiro plano, a menos que você saiba que o stdout retorna de forma confiável quando um shell termina.

Política de entrega: entrega atômica em uma única edição em todos os casos; não mude um harness para publicação progressiva a menos que se saiba que o loop de poll dele não bloqueia nas chamadas extras.

Chat é sobrecarga. Sem recapitulação, sem saída em forma de tutorial, sem colar o conteúdo de PRODUCT / DESIGN. Gaste tokens em ferramentas e edições; em caso de falha, uma ou duas frases curtas.

## Loop de polling

```
LOOP:
  .kiro/skills/impeccable/scripts/impeccable live-poll   # default long timeout; no --timeout=
  Read JSON; dispatch on "type"

  "generate"  → Handle Generate; reply done; LOOP
  "steer"     → Handle Steer; reply steer_done; LOOP
  "accept"    → Handle Accept; complete carbonize cleanup if required; LOOP
  "discard"   → Handle Discard; LOOP
  "prefetch"  → Handle Prefetch; LOOP
  "manual_edit_apply" → Handle Manual Edit Apply; reply done|partial|error; LOOP
  "variant_mount_failed" → Fix the variant files; reply done --file <path>; LOOP
  "timeout"   → LOOP
  "exit"      → break → Cleanup
```

`variant_mount_failed` significa que o navegador não conseguiu renderizar o que você publicou (`variant`, módulo `url`, `error`). O usuário vê um cartão de erro persistente, não as variantes. Corrija os arquivos de variante e então `--reply EVENT_ID done --file <manifest or source path>`; o navegador tenta de novo por conta própria.

**Modo stream** (`--stream`, experimental, nunca no Cursor): um processo de longa duração, uma linha JSON por evento, `--reply` a partir de um comando separado. Somente para harnesses que leem stdout incremental de forma confiável.

## Início

```bash
.kiro/skills/impeccable/scripts/impeccable live
```

JSON de saída: `{ ok, serverPort, serverToken, pageFiles, roots, hasProduct, product, productPath, hasDesign, design, designPath, hasSurfaceBrief, surfaceBrief }`. `roots` é o manifesto de raízes resolvido; `projectRoot` espelha `roots.appRoot`. O briefing da superfície vem junto; não chame `impeccable surface-brief` separadamente no shell. Precedência para geração: **DESIGN.md prevalece nas decisões visuais; PRODUCT.md prevalece nas decisões duradouras de produto e voz; o briefing da superfície prevalece na estratégia desta superfície.** Quando DESIGN.md está ausente, a identidade **não** está ausente; extraia-a das variáveis CSS, dos estilos computados e dos componentes irmãos (Etapa 4, Fase A). Preservar a identidade é o padrão; afastar-se dela exige intenção explícita de redesign por parte do usuário.

`serverPort`/`serverToken` pertencem ao pequeno servidor HTTP do helper (`/live.js`, SSE, `/poll`), não ao seu servidor de desenvolvimento; a URL da página é qualquer origem que sirva uma entrada de `pageFiles`.

Se a saída for `{ ok: false, error: "config_missing" | "config_invalid", path }`, este projeto precisa de uma configuração única: leia [live-setup.md](live-setup.md) e siga-o. Se a saída trouxer um `configDrift` não nulo, diga ao usuário uma vez quais arquivos HTML não estão cobertos e sugira adicioná-los ou trocar `files` por um glob; nunca edite a configuração automaticamente.

## Comandos de recuperação

O journal somente de acréscimo em `.impeccable/live/sessions/` é o estado durável canônico (não é código-fonte do projeto). Quando o chat foi interrompido, o polling foi perdido, o helper reiniciou ou o navegador recarregou:

```bash
.kiro/skills/impeccable/scripts/impeccable live-status      # helper state, active sessions, queued events; works with the helper down
.kiro/skills/impeccable/scripts/impeccable live-resume --id SESSION_ID   # active snapshot, pending event, next safe action
.kiro/skills/impeccable/scripts/impeccable live-complete --id SESSION_ID # canonical manual final acknowledgement after verified cleanup
```

Regra de reinício do servidor: inicie `impeccable live-server` de novo e então faça polling; a inicialização recoloca na fila os eventos não confirmados, então nunca peça ao usuário para clicar em Go de novo, a menos que `impeccable live-resume` diga que não existe sessão ativa.

## Tratar `generate`

**Modo replace** (padrão): `{id, action, freeformPrompt?, count, pageUrl, element, screenshotPath?, comments?, strokes?}`.

**Modo insert** (`event.mode === "insert"`): `{id, mode: "insert", count, pageUrl, insert: { position, anchor }, placeholder: { width, height }, freeformPrompt?, screenshotPath?, comments?, strokes?}`. Sem `action`; exige um `freeformPrompt` não vazio **ou** anotações. `placeholder` é uma sugestão flexível de tamanho.

Velocidade importa; o usuário está olhando para o elemento selecionado. Reutilize os metadados do preflight, minimize as chamadas de descoberta.

### Ramo do modo insert

1. Leia a captura de tela se houver (somente anotações).
2. Se `event.scaffold` estiver presente, use-o e **não** execute o helper de novo. Caso contrário:

```bash
.kiro/skills/impeccable/scripts/impeccable live-insert --id EVENT_ID --count EVENT_COUNT --position after \
  --element-id "ANCHOR_ID" --classes "class1,class2" --tag "section" --text "ANCHOR_TEXT"
```

`--position` ← `event.insert.position`; as flags de âncora mapeiam exatamente como as do wrap. O scaffold **não** tem `data-impeccable-variant="original"`; as variantes são HTML+CSS totalmente novos em `insertLine`. Em alvos de pré-visualização de código-fonte, o scaffold traz `sourceWritten: false` com `wrapperBlock` e `replaceEndLine < replaceStartLine` (uma inserção): encaixe as variantes em `wrapperBlock` no marcador e insira em `replaceStartLine` em UMA edição, exatamente como a seção de wrap descreve. Decida o modo do visitante a partir da superfície e carregue [craft-floor.md](craft-floor.md) antes de escrever marcação totalmente nova. Alvos Svelte seguem o mesmo fluxo de componentes do wrap abaixo (`mode: "insert"` no manifesto): cada variante é um componente real de raiz única em `componentDir`, sem atributos `data-impeccable-*`; nunca edite a rota durante a geração; o accept encaixa mecanicamente a marcação escolhida em `sourceFile`. Para alvos que não são Svelte, accept/discard remove o wrapper; a âncora fica intacta.

### Modo replace (padrão)

### 1. Leia a captura de tela (se houver)

`event.screenshotPath` é enviado **somente quando o usuário fez anotações antes do Go**; é um PNG do elemento com as anotações incorporadas. Leia-o antes de planejar. Quando ausente, não peça um nem tire você mesmo uma captura da página: sem anotações, uma captura de tela ancora você no design existente e contraria o briefing de três direções distintas; trabalhe a partir de `element.outerHTML`, dos estilos computados e do prompt.

Semântica das anotações: o `{x, y}` de um comentário é local ao elemento e vincula o texto ao filho sob aquele ponto (um comentário perto do título é sobre o título). Comentários e traços são independentes, a menos que estejam claramente pareados. Traços são lidos pela forma: laço fechado = "esta coisa" (ênfase, não uma região de recorte); seta = direção ou movimento; cruz/barra = excluir; rabisco = ênfase ou exclusão, conforme o contexto. Se a intenção de um traço for genuinamente ambígua e mudar o briefing, faça uma pergunta curta antes de gerar; caso contrário, declare sua interpretação em uma frase.

### 2. Envolva o elemento

Quando `event.scaffold` está presente, o helper já encontrou o código-fonte e calculou o wrapper; trate-o como a saída bem-sucedida e pule o comando. `event.scaffoldAttempted` com `scaffoldError` significa que o preflight não conseguiu terminar; use o comando abaixo.

**Em alvos de pré-visualização de código-fonte, `event.scaffold` traz `sourceWritten: false`.** O helper NÃO escreveu o wrapper; ele entrega a você `scaffold.wrapperBlock` mais o intervalo no código-fonte do elemento escolhido (`replaceStartLine`, `replaceEndLine`, indexados a partir de 1). Escreva o wrapper **e** todas as variantes em UMA edição: encaixe suas variantes em `wrapperBlock` no marcador "Variants: insert below this line" e então substitua as linhas `[replaceStartLine, replaceEndLine]` pelo resultado. Uma escrita separada do scaffold recarrega o framework antes de a escrita das variantes chegar e deixa o navegador travado em 0/N. (`replaceEndLine < replaceStartLine` significa modo insert: insira, não remova nada.) O caminho `svelte-component` nunca define `sourceWritten`.

```bash
.kiro/skills/impeccable/scripts/impeccable live-wrap --id EVENT_ID --count EVENT_COUNT --element-id "ELEMENT_ID" --classes "class1,class2" --tag "div" --text "TEXT_SNIPPET"
```

Mapeamento de flags (mantenha separadas, nunca as junte em `--query`): `--element-id` ← `event.element.id`; `--classes` ← classes unidas por vírgulas; `--tag` ← tagName; `--text` ← primeiros ~80 caracteres de textContent, **em toda chamada**: ele desambigua componentes irmãos repetidos; sem ele, o wrap cai na primeira correspondência. Se `event.pageUrl` implicar o arquivo, passe `--file PATH`. Se `--text` ainda corresponder a vários candidatos, o wrap termina com `{ error: "element_ambiguous", candidates, fallback: "agent-driven" }`: escolha o intervalo certo a partir do contexto da página e escreva o wrapper manualmente conforme o fluxo de fallback.

Saída de sucesso: `{ file, insertLine, commentSyntax, styleMode, styleTag, cssSelectorPrefixExamples, cssAuthoring }` (mais os campos de `sourceWritten: false` acima em alvos de pré-visualização de código-fonte). Executado diretamente, sem scaffold de preflight, ele mesmo escreve o wrapper e você encaixa as variantes em `insertLine`. `styleMode` controla como o CSS de pré-visualização deve ser escrito. Trate-o como um modo de capacidade detectado, não como um palpite sobre o framework: `scoped` significa regras `@scope ([data-impeccable-variant="N"])`; `astro-global-prefixed` significa prefixos explícitos `[data-impeccable-variant="N"]` com o `styleTag` exato retornado. Use `cssAuthoring` como fonte da verdade para o arquivo atual (styleTag, estratégia de seletores, requisitos, padrões proibidos); não aplique nenhuma exceção específica de framework a menos que ele mande.

Para alvos Svelte/SvelteKit, `impeccable live-wrap` retorna `previewMode: "svelte-component"` com `file` apontando para um `node_modules/.impeccable-live/<id>/manifest.json` temporário, `componentDir` contendo os componentes de variante e `sourceFile` a rota real. O scaffold é baseado em AST: blocos de fluxo de controle (`{#each}`, `{#if}`) sobrevivem intactos e uma coleção livre de each atravessa o contrato como UMA prop estruturada (kind `collection`). O payload inclui `componentStubMarkup` (a marcação com props substituídas já escrita em cada stub), então não leia de volta o manifesto nem os stubs. EDITE `v1.svelte`, `v2.svelte`, ... no próprio lugar; nunca os exclua e recrie; mantenha o fluxo de controle do stub e os nomes de props de `propContract`; nunca achate um loop em itens literais. O `<style>` do stub chega pré-preenchido com as regras do código-fonte que atualmente estilizam a seleção; reestilize ou exclua-as livremente. No accept, toda regra pré-preenchida que sua variante não declarar de novo é REMOVIDA do código-fonte (a pré-visualização nunca a aplicou, então o usuário aprovou um design sem ela). Use seletores de classe semânticos, sem `@scope`, sem `data-impeccable-*`. Responda com `--file` definido como o caminho do manifesto; o navegador monta os componentes compilados para que o HMR do Svelte não redefina o estado da página. O accept mescla de volta o componente escolhido mecanicamente (marcação restaurada para expressões da rota, CSS reconciliado, parâmetros incorporados, indentação preservada); neste caminho você não tem limpeza pós-accept. Quando a seleção contém construções que uma pré-visualização desacoplada não suporta (tags de componente, `bind:`/`use:`, blocos await, scripts inline, atributos spread), o wrap retorna o wrapper normal de pré-visualização de código-fonte com `previewFallback: { from: "svelte-component", reason }`; apenas siga o formato retornado.

**Parâmetros em caminhos de pré-visualização de componentes vão em um arquivo lateral, nunca como atributo** (o Svelte interpreta `{` em valores de atributo como expressão). Declare-os em `componentDir/params.json`, indexados pelo número da variante, usando o esquema da seção 7:

```json
{ "1": [ {"id":"density","kind":"steps","default":"snug","label":"Density","options":[
    {"value":"airy","label":"Airy"},{"value":"snug","label":"Snug"} ]} ] }
```

Escreva o `<style>` do componente contra `var(--p-<id>, default)` para `range`/`toggle` e `[data-p-<id>="…"]` para `steps`, envolvido em `:global(...)` para que os valores dos controles em tempo de execução na raiz montada alcancem suas regras.

**Erros de fallback.** O wrap se recusa a escrever em arquivos que não são código-fonte (gerados, não rastreados): aceitar em um deles é perda silenciosa de dados. Três formatos, todos com `fallback: "agent-driven"` (veja **Tratar fallback**): `file_is_generated` (seu `--file` aponta para um arquivo gerado), `element_not_in_source` com `generatedMatch` (o elemento só existe no gerado), `element_not_found` (provavelmente injetado em tempo de execução).

### 3. Carregue a referência da ação

`event.action` é `impeccable` (livre): trabalhe a partir das regras de design do SKILL.md mais [craft-floor.md](craft-floor.md); decida o modo do visitante a partir da superfície; não carregue a referência de um subcomando. Livre não é passe para pular parâmetros: siga o orçamento e o viés do modo livre da seção 7. Qualquer outra ação (`bolder`, `quieter`, `distill`, `polish`, `typeset`, `colorize`, `layout`, `adapt`, `animate`, `delight`, `overdrive`): leia `reference/<action>.md` antes de planejar; os parâmetros MUST dela se somam ao orçamento da seção 7.

### 4. Planeje três variantes: identidade primeiro, depois modo, depois eixos

O live roda sobre uma superfície existente; a marca já foi escolhida. O trabalho é variar **dentro da identidade**, não escolher entre identidades. A pior falha são três variantes fora da marca que o usuário não consegue aceitar. Quatro fases, em ordem.

#### Fase A: Extraia a identidade (não pode ser pulada)

Fontes em ordem de prioridade: os campos de sistema visual do DESIGN.md; propriedades customizadas de CSS (tokens de fato); estilos computados no elemento escolhido e no pai; a retórica visual dos componentes irmãos. Escreva UMA frase registrando o que está de fato na tela: cor dominante da superfície e cor de destaque (valores reais, não "quente"), o par de fontes carregado, a topologia do layout (empilhado / lado a lado / grade / assimétrico / sobreposto), o tratamento da superfície (cantos, bordas, sombras, densidade de decoração) e o tom de voz lido a partir da copy. Seja específico; pule um eixo em vez de inventar; não nomeie uma família estética (isso é conclusão, não dado). Essa frase é a **trava de identidade**: toda variante precisa parecer a mesma marca lado a lado. A ausência do DESIGN.md nunca é desculpa.

#### Fase B: Escolha o modo (padrão vs ruptura)

O modo **padrão** preserva a identidade e varia a expressão dentro dela; certo para ~90% das sessões. O modo **ruptura** rejeita a identidade; acione SOMENTE diante de um pedido explícito do usuário na solicitação ou no prompt atual ("refaça o design disto", "reconstrua do zero", "algo completamente diferente"); uma crítica antiga ou uma nota velha não é autorização. Na dúvida, padrão: o padrão errado custa "três variantes dentro da marca com sensação parecida" (recuperável); a ruptura errada custa três variantes fora da marca (irrecuperável).

#### Fase C: Planeje três variantes

**Modo padrão.** Cada variante se compromete com um **eixo principal** diferente, preservando a frase de identidade. Os seis eixos: 1 **Hierarquia** (qual elemento comanda o olhar), 2 **Topologia do layout** (empilhado / lado a lado / grade / assimétrico / sobreposto), 3 **Sistema tipográfico** (lógica de pareamento, razão de escala, caixa/peso, *dentro das fontes disponíveis*), 4 **Estratégia de cor** (qual papel da paleta existente sustenta a superfície: Contida / Comprometida / Paleta completa / Encharcada; somente tokens existentes), 5 **Densidade** (mínima / confortável / densa), 6 **Decomposição estrutural** (mesclar, dividir, revelação progressiva). Três variantes, três eixos DIFERENTES: a mesma marca sob três ângulos. Fontes novas, matizes novos ou sinais de uma nova família estética pertencem somente ao modo ruptura.

**Modo ruptura.** Cada variante se ancora em uma direção estética diferente derivada da marca, nunca de um catálogo fixo: leia as palavras de Personalidade da Marca do PRODUCT.md; derive experiências físicas, espaciais ou materiais que as encarnem; a partir delas, derive três direções genuinamente diferentes entre si E da superfície atual; rejeite escolhas automáticas cuja justificativa serviria para um produto vizinho. Cada direção precisa ser uma frase concreta que nomeie uma referência do mundo real ("um sistema de etiquetas de exposição de museu", não "limpo e minimalista").

**Em ambos os modos, nomeie os 2 ou 3 controles de parâmetro de cada variante durante o planejamento** (orçamento da seção 7). Parâmetros fazem parte do design; decidir "o que é ajustável" durante o planejamento é melhor do que adaptar depois.

#### Fase D: Teste de olhos semicerrados

**Padrão:** compare cada variante com a trava da Fase A; desvio de paleta, de voz tipográfica ou de retórica significa que ela entrou na ruptura por acidente: refaça. Depois confirme três eixos principais diferentes; três variantes de "densidade mais apertada" é falha. **Ruptura:** duas passadas, família antes da frase. Passada de família (inegociável): rotule cada variante com uma família concreta escolhida por você; rótulos compartilhados ou intercambiáveis significam refazer. Passada de frase: três descrições de uma linha lado a lado; duas que rimam significam refazer. Quando o eixo principal é cor ou tema, o trio não pode compartilhar tema + matiz dominante: três mundos de cor, não três tons.

**Invocações específicas de ação** precisam variar ao longo da dimensão da ação:

- `bolder`: amplifique uma dimensão diferente por variante (escala / saturação / mudança estrutural).
- `quieter`: recue em uma dimensão diferente (cor / ornamento / espaçamento).
- `distill`: remova uma classe diferente de excesso (ruído visual / conteúdo redundante / estrutura aninhada).
- `polish`: um eixo de refinamento diferente (ritmo / hierarquia / microdetalhes).
- `typeset`: pareamento diferente E razão de escala diferente em cada uma.
- `colorize`: família de matiz diferente em cada uma; varie croma e estratégia de contraste.
- `layout`: arranjo estrutural diferente, não ajustes de espaçamento.
- `adapt`: contexto-alvo diferente por variante (mobile-first / tablet / desktop / impressão ou poucos dados).
- `animate`: vocabulário de movimento diferente (escalonamento em cascata / revelação por recorte / escala e foco / morph / parallax).
- `delight`: sabor diferente de personalidade (microinteração / surpresa tipográfica / detalhe ilustrado / sonoro ou háptico / easter egg).
- `overdrive`: convenção diferente quebrada (escala / estrutura / movimento / modelo de entrada / transições de estado); pule a etapa de "propor e perguntar" dele, o live não é interativo.

### 5. Aplique o prompt livre (se houver)

`event.freeformPrompt` é o teto do usuário para a direção: todas as variantes o respeitam enquanto exploram interpretações diferentes dentro do modo da Fase B. Modo padrão: o prompt estreita os eixos, não a identidade ("mais confiante" → uma variante amplifica a hierarquia, uma assume a cor de destaque, uma aperta a densidade). Modo ruptura: o prompt estreita as faixas, não as famílias ("primeira página de jornal" → broadsheet vs tabloide vs publicação setorial; depois faça a passada de família). Quando o prompt conflitar com um compromisso de marca vinculante ou uma invariante do DESIGN.md, preserve a invariante, a menos que o usuário a revogue explicitamente.

### 6. Entregue as variantes

Substituição HTML completa do elemento original por variante, não um patch só de CSS. Coloque o CSS de pré-visualização junto, como uma tag `<style>` dentro do wrapper. **Padrão atômico:** CSS + todas as variantes + manifestos de parâmetros em uma edição em `insertLine`.

```html
<!-- Variants: insert below this line -->
<style data-impeccable-css="SESSION_ID">
  /* rules matching cssAuthoring.rulePattern */
</style>
<div data-impeccable-variant="1">
  <!-- variant 1: full element replacement (single top-level element) -->
</div>
<div data-impeccable-variant="2" style="display: none">
  <!-- variant 2 -->
</div>
<div data-impeccable-variant="3" style="display: none">
  <!-- variant 3 -->
</div>
```

Substitua a tag de abertura de style por `cssAuthoring.styleTag` quando a ferramenta retornar uma diferente. **Cada div de variante contém exatamente um elemento de nível superior**, com a mesma tag do original; irmãos soltos quebram o rastreamento do contorno e o accept. Primeira variante visível, todas as outras `display: none`. O MutationObserver do navegador aceita chegada atômica ou progressiva; aceitar uma variante que já chegou isola o worker, então publicações posteriores são rejeitadas.

Para `styleMode: "scoped"`, escreva toda regra `:scope` com um combinador de descendente: o limite do `@scope` é a div wrapper da variante, não o seu elemento, então um `:scope { ... }` puro estiliza uma casca `display: contents`. Sempre desça um nível (`:scope > .card`, `:scope .hero-title`). O CSS do agente de teste falso no [modelo de agente do repositório](https://github.com/pbakaus/impeccable/blob/8dac6ae7e020c43ab10ce9b41939f6fd42627b96/tests/live-e2e/agent.mjs) é um modelo fiel.

**Alvos JSX / TSX:** envolva o conteúdo de `<style>` em um template literal (chaves de CSS seriam interpretadas como JSX), use `className=` / `style={{…}}`, mantenha os atributos `data-impeccable-*` como strings simples:

```tsx
<style data-impeccable-css="SESSION_ID">{`
  @scope ([data-impeccable-variant="1"]) { ... }
`}</style>
<div data-impeccable-variant="2" style={{ display: 'none' }}>
  {/* variant 2 */}
</div>
```

O script de wrap fornece um wrapper JSX de raiz única com os comentários marcadores dentro; solte o bloco no marcador e o código-fonte continua sendo TSX válido.

### 7. Parâmetros (dimensionados pela composição, 0-4 por variante)

Cada variante pode expor controles **grosseiros**; o navegador acopla um controle por parâmetro com custo zero de regeneração (os controles acionam uma variável CSS ou um atributo de dados contra o qual seu CSS com escopo foi escrito). Conecte um eixo assim que o usuário puder plausivelmente murmurar "um pouco mais apertado" ou "um toque a mais de destaque" sem querer uma regeneração; micromargens e ajustes pontuais não são parâmetros. Viés do modo livre: foi você quem escolheu os eixos, então exponha-os; uma seção hero com 0 parâmetros é quase sempre um erro, e 1 é pouco, a menos que o design seja um ponto fixo genuíno.

O orçamento escala com o peso VISUAL do elemento (conte filhos visuais, não profundidade do DOM):

- **Folha / minúsculo** (botão, ícone, título isolado): **0 parâmetros.**
- **Composição pequena** (cartão simples, input com rótulo, ≤ ~5 filhos visuais): **0-1**.
- **Composição média** (seção, grupo de navegação, 6-15 filhos): **alvo 2**; 1 se for simples.
- **Composição grande** (seção hero, região inteira, 16+ filhos ou subseções): **alvo 2-3, até 4** quando eixos independentes estiverem todos escritos em CSS.

**Limite rígido: quatro** por variante. Para subcomandos nomeados, os parâmetros MUST da referência da ação são inegociáveis quando expressáveis; respeite o limite, sem controles duplicados.

**Declare** no caminho HTML/JSX como atributo do wrapper (caminhos de pré-visualização de componentes usam `componentDir/params.json`, mesmo esquema, indexado pelo número da variante; veja a seção de wrap):

```html
<div data-impeccable-variant="1" data-impeccable-params='[
  {"id":"color-amount","kind":"range","min":0,"max":1,"step":0.05,"default":0.5,"label":"Color amount"},
  {"id":"serif","kind":"toggle","default":false,"label":"Serif display"}
]'>
```

Três tipos: `range` (controle deslizante; aciona `--p-<id>`; escreva `var(--p-color-amount, 0.5)`; campos min/max/step/default/label), `steps` (botões de opção segmentados; aciona `data-p-<id>`; escreva `:scope[data-p-density="airy"] .grid { ... }`; campos options/default/label), `toggle` (aciona tanto `--p-<id>: 0|1` quanto a presença do atributo; campos default/label). A redefinição ao trocar de variante é uma limitação conhecida: cada variante começa nos padrões que declarou.

**No accept**, o navegador envia os valores atuais e `impeccable live-accept` os grava como um comentário irmão: `<!-- impeccable-param-values SESSION_ID: {"color-amount":0.7} -->`. A limpeza do carbonize os incorpora: mantenha somente o ramo de `steps`/`toggle` correspondente, descarte os outros, reduza `:scope[data-p-…]` a regras semânticas; substitua os literais de `range` ou atualize o padrão da variável.

### 8. Sinalize a conclusão

```bash
.kiro/skills/impeccable/scripts/impeccable live-poll --reply EVENT_ID done --file RELATIVE_PATH
```

`RELATIVE_PATH` é relativo à raiz do projeto; o navegador busca o código-fonte diretamente se o servidor de desenvolvimento não tiver HMR. Depois faça polling de novo imediatamente.

### Abortar uma sessão em andamento

Se o wrap ou a geração falhar depois que o navegador passou para GENERATING, avise o **navegador** para que a barra dele seja redefinida: `.kiro/skills/impeccable/scripts/impeccable live-poll --reply EVENT_ID error "Short reason"`. Nunca use `live-accept --discard` para isso (é um mero modificador de arquivos, o navegador nunca o vê, a barra fica presa nos pontinhos); `--discard` serve apenas para a limpeza no código-fonte de um discard que o próprio navegador iniciou.

## Tratar fallback

Quando o wrap retorna `fallback: "agent-driven"`, você mesmo escolhe o arquivo-fonte; o objetivo não muda: três variantes de pré-visualização agora, e a aceita persistida onde o próximo build não consiga apagá-la.

1. **Descubra onde o elemento realmente vive** a partir do payload de erro: `element_not_in_source` + `generatedMatch` significa que o HTML servido é gerado, então encontre o template ou partial do gerador; `element_not_found` significa injetado em tempo de execução, então encontre o componente que renderiza ou a fonte de dados; `file_is_generated` se resolve da mesma forma. Uma mudança puramente visual pode pertencer a uma folha de estilo compartilhada, e não a um template.
2. **Pré-visualize no arquivo servido**: escreva manualmente o mesmo scaffold de wrapper que `impeccable live-wrap` produz (`<!-- impeccable-variants-start ID --><div data-impeccable-variants="ID" data-impeccable-variant-count="3" style="display: contents">…</div><!-- end -->`) no arquivo que o navegador de fato carregou, insira suas divs de variante, `--reply EVENT_ID done --file <served file>`. Essa edição é temporária; tudo bem se uma regeneração a apagar.
3. **No accept, escreva no código-fonte verdadeiro** (o accept recusa arquivos gerados, então `_acceptResult.handled` geralmente é `false` aqui): mudança estrutural → código-fonte do template/componente; apenas visual → a folha de estilo certa; conteúdo renderizado a partir de dados → a fonte de dados ou a lógica de renderização. Depois remova o wrapper temporário do arquivo servido.
4. **No discard**, apenas remova o wrapper temporário.

## Tratar `accept`

Evento: `{id, variantId, _acceptResult, _completionAck}`. O script de poll já executou `impeccable live-accept` de forma determinística e confirmou a entrega; o DOM do navegador já está atualizado.

- O evento de accept inclui `pageUrl`; o script de poll precisa repassá-lo para `impeccable live-accept --page-url PAGE_URL`, para que a limpeza no momento do accept só remova as edições de copy preparadas para a página atual.
- `_completionAck.ok !== true`: ainda não faça polling. Execute `impeccable live-status` / `impeccable live-resume`, termine a limpeza manualmente se necessário e então `impeccable live-complete --id EVENT_ID`.
- `handled: true, carbonize: false`: nada a fazer; faça polling de novo.
- `handled: true, carbonize: true`: limpeza obrigatória abaixo; `_acceptResult.todo`, `_completionAck.requiresComplete` e o banner no stderr apontam todos para ela.
- `handled: false, mode: "fallback"`: a sessão viveu em um arquivo gerado; você já escreveu no código-fonte verdadeiro na Etapa 3 do fallback; limpe o wrapper temporário e faça polling.
- `handled: false, mode: "error"`: **não edite o arquivo à mão.** `source_locked`: execute de novo o mesmo comando `impeccable live-accept` (idempotente) até o publicador liberar. `accept_receipt_conflict`: a sessão já foi resolvida como `priorOperation`; execute `impeccable live-status` e avise o usuário. Qualquer outra coisa: relate brevemente, executando `impeccable live-status` primeiro.
- `handled: false` sem `mode`: limpeza manual: leia o arquivo, encontre os marcadores, edite.

### Obrigatório após o accept (carbonize)

`carbonize: true` significa que a variante aceita foi costurada no código-fonte com marcadores do helper e CSS inline (para que o navegador renderize sem lacuna). Essa costura é temporária; reescreva-a em forma permanente antes de qualquer outra coisa, ou regras `@scope` mortas, divs wrapper e comentários marcadores se acumulam entre sessões. Cinco etapas, de forma síncrona, antes do próximo poll:

1. **Localize o bloco carbonize** em `_acceptResult.file`: delimitado por `<!-- impeccable-carbonize-start/end SESSION_ID -->` com um elemento `<style data-impeccable-css>`; leia primeiro o comentário `<!-- impeccable-param-values -->` quando presente, ele orienta as etapas 3 e 4.
2. **Mova as regras CSS** para a folha de estilo real do projeto (a que já cuida da estilização do elemento ao redor).
3. **Incorpore os valores de parâmetros enquanto reescreve os seletores**: redirecione `@scope ([data-impeccable-variant="N"])` para classes semânticas reais; mantenha somente o ramo `:scope[data-p-<id>="VALUE"]` correspondente ao valor escolhido; substitua os literais de `var(--p-<id>)` ou atualize o padrão da variável.
4. **Desembrulhe o conteúdo aceito**: exclua a div interna da variante (e, em JSX, a div externa `data-impeccable-carbonize`); remova `data-impeccable-params` e todos os atributos `data-p-*`.
5. **Exclua** o bloco `<style>` inline, o comentário de valores de parâmetros, os dois marcadores carbonize e quaisquer regras `@scope` das variantes não aceitas.

Depois execute `impeccable live-complete --id SESSION_ID` e verifique `phase: "completed"` antes de fazer polling de novo. O comando é uma barreira, não uma formalidade: ele recusa com `error: "source_dirty"` mais os achados enquanto restar qualquer sobra do modo live; corrija e execute de novo (`--force` somente para falsos positivos).

## Tratar `discard`

Evento: `{id, _acceptResult, _completionAck}`. O script de poll já restaurou o original e confirmou `discarded`. Nada a fazer, a menos que `_completionAck.ok !== true`; nesse caso, `impeccable live-complete --id EVENT_ID --discarded` e faça polling de novo.

## Tratar `steer`

Evento: `{id, message, pageUrl}`: direção no nível da página vinda do controle Steer da barra global (digitada ou falada), sem contexto de elemento, sem alternância de variantes. Leia `message`, inspecione a página ou os arquivos conforme necessário, faça edições ou responda em prosa. Responda `.kiro/skills/impeccable/scripts/impeccable live-poll --reply EVENT_ID steer_done ["Optional short toast"]` ou, em caso de falha, `--reply EVENT_ID error "Short reason"`, e então faça polling imediatamente. Sem resposta separada de recebimento; a barra Steer é desbloqueada em `steer_done` ou `error`.

## Tratar `prefetch`

Evento: `{pageUrl}`: disparado uma vez por rota na primeira seleção; o usuário provavelmente está prestes a dar Go em uma página que você não leu. Resolva a rota para o arquivo dela (a raiz `/` geralmente é o `pageFile` da inicialização; sites de várias páginas costumam mapear `/foo` para `public/foo/index.html`; SPAs mapeiam tudo para uma única entrada), leia-o e faça polling de novo. Sem `--reply`. Se não conseguir resolvê-la com confiança, pule e faça polling.

## Tratar `manual_edit_apply`

Evento: `{id, pageUrl, batch: {entries}, evidencePath?, chunk?, repair?, deadlineMs}`.

O usuário já clicou em Apply. Não pergunte o que fazer, não descarte nem redirecione para o Go. A thread live principal mantém o loop de poll em primeiro plano e envia o `/poll --reply --data` final.

Quando houver subagentes nativos disponíveis, delegue as edições de código-fonte para `impeccable_manual_edit_applier` / `impeccable-manual-edit-applier`. Passe cwd, caminho dos scripts, id do evento, URL da página, chunk/deadline, `batch`, `evidencePath` e o esquema canônico de resultado JSON. O subagente não deve fazer polling nem responder. Se não houver, aplique inline com o mesmo contrato.

Se `repair` estiver presente, o Apply anterior alterou o código-fonte, mas a validação final falhou. Corrija o código-fonte atual e retorne o mesmo resultado JSON canônico; não reverta os arquivos você mesmo. O navegador perguntará ao usuário antes de qualquer reversão.

Depois que as edições de código-fonte terminarem, responda exatamente uma vez com `.kiro/skills/impeccable/scripts/impeccable live-poll --reply EVENT_ID done --data '{"status":"done","appliedEntryIds":["8hexid"],"failed":[],"files":["src/page.html"],"notes":[]}'`. Use `status:"partial"` ou `status:"error"` com `failed[]` quando nem toda entrada tiver sido aplicada. Depois faça polling de novo. Nunca responda sem o id do evento; `--reply done --file ...` é inválido para o Apply manual.

## Saída

O usuário encerra o modo live dizendo isso no chat, fechando a aba (o SSE cai; o poll retorna `exit` após 8 s) ou pelo botão de saída do navegador. Em `exit`, encerre qualquer poll em segundo plano ainda em execução e então faça a limpeza.

## Limpeza

```bash
.kiro/skills/impeccable/scripts/impeccable live-server stop
```

Para o helper e executa `impeccable live-inject --remove` para retirar o script injetado (use `stop --keep-inject` para mantê-lo e reiniciar rapidamente; `.impeccable/live/config.json` persiste como configuração do projeto). Depois procure e remova quaisquer wrappers `impeccable-variants-start` e blocos `impeccable-carbonize-start` remanescentes.

## Configuração inicial

Somente quando `impeccable live` informar `config_missing` / `config_invalid`, ou quando `configDrift` precisar de explicação, ou quando a configuração não tiver `cspChecked`: leia [live-setup.md](live-setup.md). Ele cuida do esquema de configuração, da tabela `files` por framework, dos adaptadores de injeção, da correção de desvios e do fluxo de detecção de CSP e consentimento.
