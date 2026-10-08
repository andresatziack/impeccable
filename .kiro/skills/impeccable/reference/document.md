Gere um arquivo `DESIGN.md` na raiz do projeto que capture o design system visual atual, para que agentes de IA que geram novas telas permaneçam fiéis à marca.

O DESIGN.md segue a [especificação oficial do formato DESIGN.md](https://raw.githubusercontent.com/google-labs-code/design.md/main/docs/spec.md): um frontmatter YAML opcional contendo tokens de design legíveis por máquina, seguido de até oito seções markdown em ordem fixa. **Os tokens são normativos; a prosa fornece o contexto de como aplicá-los.** Seções podem ser omitidas quando não forem relevantes, mas as presentes permanecem na ordem especificada. Use os títulos canônicos abaixo para que o arquivo continue portável entre ferramentas que entendem DESIGN.md.

## O frontmatter: esquema de tokens

O frontmatter YAML é a camada legível por máquina. É o que o linter do Stitch valida e a partir do que o painel live renderiza os blocos. Mantenha-o enxuto; cada entrada deve corresponder a um token que o projeto realmente usa.

```yaml
---
name: <project title>
description: <one-line tagline>
colors:
  primary: "#b8422e"
  neutral-bg: "#faf7f2"
  # ...one entry per extracted color; key = descriptive slug
typography:
  display:
    fontFamily: "Cormorant Garamond, Georgia, serif"
    fontSize: "clamp(2.5rem, 7vw, 4.5rem)"
    fontWeight: 300
    lineHeight: 1
    letterSpacing: "normal"
  body:
    # ...
rounded:
  sm: "4px"
  md: "8px"
spacing:
  sm: "8px"
  md: "16px"
components:
  button-primary:
    backgroundColor: "{colors.primary}"
    textColor: "{colors.neutral-bg}"
    rounded: "{rounded.sm}"
    padding: "16px 48px"
  button-primary-hover:
    backgroundColor: "{colors.primary-deep}"
---
```

Regras que importam:

- **Referências a tokens** usam `{path.to.token}` (ex.: `{colors.primary}`, `{rounded.md}`). Componentes podem referenciar primitivos; primitivos não podem referenciar uns aos outros.
- **Cores aceitam qualquer string de cor CSS válida.** Hex é o padrão recomendado por portabilidade, mas preserve um valor `rgb()`, `hsl()`, `oklch()`, de gama ampla ou de cor mista já existente quando ele for a fonte normativa do projeto. Nunca divida a fonte da verdade sem um motivo explícito.
- **Subtokens de componente** são limitados a 8 propriedades: `backgroundColor`, `textColor`, `typography`, `rounded`, `padding`, `size`, `height`, `width`. Sombras, movimento, anéis de foco, backdrop-filter: nada disso cabe. Leve-os no sidecar (Passo 4b).
- **As chaves de escala são abertas.** Use os nomes que o projeto já usa (`oxblood-deep`, `surface-container-low`). Não renomeie para os padrões do Material.
- **Variantes são convenção de nomenclatura, não esquema.** `button-primary` / `button-primary-hover` / `button-primary-active` como chaves irmãs.

## O corpo markdown: oito seções (ordem canônica)

1. `## Overview`
2. `## Colors`
3. `## Typography`
4. `## Layout`
5. `## Elevation & Depth`
6. `## Shapes`
7. `## Components`
8. `## Do's and Don'ts`

Omita seções irrelevantes em vez de preenchê-las com regras inventadas. Coloque o layout responsivo em Layout, a profundidade em Elevation & Depth, o raio e a linguagem de formas em Shapes, e o comportamento de cada componente em Components. Seções desconhecidas são preservadas pelo formato, mas novas orientações visuais devem usar a estrutura canônica sempre que couberem nela.

## Quando executar

- O new-work encontrou um sistema visual existente coerente, mas nenhum `DESIGN.md`.
- A primeira implementação de um novo mundo visual está concluída e suas decisões provisórias precisam ser consolidadas.
- Um `DESIGN.md` existente está desatualizado (o design se afastou dele).
- Antes de um grande redesign, para capturar o estado atual como referência.

Se um `DESIGN.md` já existir, **não o sobrescreva silenciosamente**. Mostre primeiro o arquivo existente ao usuário. Pergunte diretamente ao usuário para esclarecer o que você não consegue inferir. A escolha é entre atualizar, sobrescrever ou mesclar.

## Dois caminhos

- **Modo scan** (padrão): o projeto tem tokens de design, componentes ou saída renderizada. Extraia e depois confirme a linguagem descritiva. Use quando houver código para analisar.
- **Modo seed**: o projeto está em pré-implementação. Garanta que o PRODUCT.md exista e então reutilize a oficina de mundo visual do new-work e escreva a semente direcional do DESIGN.md. Execute de novo no modo scan quando houver código.

Decida examinando primeiro (Passo 1 do modo scan). Se o exame não encontrar tokens, arquivos de componentes nem site renderizado, ofereça o modo seed; não troque silenciosamente. `/impeccable document --seed` solicita a oficina de mundo visual do new-work, mas não autoriza substituir código coerente: quando existe um sistema atual, ofereça o modo scan ou encaminhe um pedido explícito de substituição de identidade pelo new-work.

## Modo scan (abordagem C: extrair automaticamente e depois confirmar a linguagem descritiva)

### Passo 1: Encontre os ativos de design

Pesquise a base de código nesta ordem de prioridade:

1. **Propriedades customizadas de CSS**: faça grep por declarações `--color-`, `--font-`, `--spacing-`, `--radius-`, `--shadow-`, `--ease-`, `--duration-` em arquivos CSS (normalmente `src/styles/`, `public/css/`, `app/globals.css` etc.). Registre nome, valor e o arquivo em que está definido.
2. **Configuração do Tailwind**: se `tailwind.config.{js,ts,mjs}` existir, leia o bloco `theme.extend` em busca de colors, fontFamily, spacing, borderRadius, boxShadow.
3. **Arquivos de tema CSS-in-JS**: styled-components, emotion, vanilla-extract, stitches; procure `theme.ts`, `tokens.ts` ou equivalente.
4. **Arquivos de tokens de design**: `tokens.json`, `design-tokens.json`, saída do Style Dictionary, formato do W3C Design Tokens Community Group.
5. **Biblioteca de componentes**: examine os principais componentes de botão, card, input, navegação e diálogo. Anote suas APIs de variantes e estilos padrão.
6. **Folha de estilos global**: o arquivo CSS raiz normalmente tem a tipografia base e as atribuições de cor.
7. **Saída renderizada visível**: se houver ferramentas de automação de navegador disponíveis, carregue o site ao vivo e amostre os estilos computados de elementos-chave (body, h1, a, button, .card). Isso captura valores que os tokens deixam escapar.

### Passo 2: Extraia automaticamente o que pode ser extraído automaticamente

Monte um rascunho estruturado a partir dos tokens descobertos. Para cada classe de token:

- **Cores**: agrupe em Primary / Secondary / Tertiary / Neutral (os papéis derivados do Material que o Stitch usa). Se o projeto tiver apenas um destaque, expresse-o como Primary + Neutral; omita Secondary e Tertiary em vez de inventá-los.
- **Tipografia**: mapeie os tamanhos e pesos observados para a hierarquia do Material (display / headline / title / body / label). Anote as pilhas de font-family e a razão da escala.
- **Elevação**: catalogue o vocabulário de sombras. Se o projeto for plano e usar camadas tonais no lugar, essa é uma resposta válida; declare-a explicitamente.
- **Componentes**: para cada componente comum (botão, card, input, chip, item de lista, tooltip, nav), extraia forma (raio), atribuição de cor, tratamento de hover/foco e padding interno.
- **Layout + espaçamento**: extraia grid, container, breakpoints, ritmo e comportamento de densidade para Layout.
- **Formas**: extraia raio, cantos, bordas, recortes e comportamentos de forma recorrentes para Shapes.

### Passo 2b: Prepare o frontmatter

A partir dos tokens extraídos automaticamente, rascunhe agora o frontmatter YAML (você o escreverá no topo do DESIGN.md no Passo 4). Esta é a camada legível por máquina: o que o painel live e o linter do Stitch consomem.

- **Cores**: uma entrada por cor extraída. Chave = slug descritivo (`oxblood-deep`, `editorial-magenta`, não `blue-800`). Valor = o formato que o projeto trata como canônico (OKLCH ou hex; veja as regras de frontmatter acima). Não divida a fonte da verdade: um formato no frontmatter, e não redefina o mesmo token na prosa com outro valor.
- **Tipografia**: uma entrada por papel (`display`, `headline`, `title`, `body`, `label`). Typography é um objeto; inclua apenas as propriedades reais para o projeto (`fontFamily`, `fontSize`, `fontWeight`, `lineHeight`, `letterSpacing`, `fontFeature`, `fontVariation`).
- **Rounded / Spacing**: os passos de escala que o projeto realmente usa, com as chaves no nome de escala que o projeto usa (`sm` / `md` / `lg`, ou `surface-sm`, ou passos numéricos).
- **Componentes**: uma entrada por variante (`button-primary`, `button-primary-hover`, `button-ghost`). Referencie primitivos via `{colors.X}`, `{rounded.Y}`. Se uma variante precisar de uma propriedade que o conjunto de 8 propriedades do Stitch não cobre (sombra, anel de foco, backdrop-filter), leve o snippet completo no sidecar.

Pule tudo o que o projeto não tem. Chaves de escala vazias ou tokens fabricados poluem a especificação.

### Passo 3: Peça ao usuário a linguagem qualitativa

Os itens a seguir exigem contribuição criativa que não pode ser extraída automaticamente. Pergunte-os em duas rodadas estruturadas de no máximo três perguntas cada (ou o limite menor do harness), aguardando entre as rodadas:

- **Creative North Star**: uma única metáfora nomeada para o sistema inteiro ("The Editorial Sanctuary", "The Golden State Curator", "The Lab Notebook"). Ofereça 2-3 opções que honrem a personalidade de marca do PRODUCT.md.
- **Voz do Overview**: adjetivos de clima, filosofia estética em 2-3 frases e qualquer antirreferência visual confirmada.
- **Caráter das cores** (para as cores extraídas automaticamente): nomes descritivos ("Deep Muted Teal-Navy", não "blue-800"). Sugira 2-3 opções por cor-chave com base em matiz/saturação.
- **Filosofia de elevação**: plana/em camadas/elevada. Se existirem sombras, o papel delas é ambiente ou estrutural?
- **Filosofia de componentes**: a sensação de botões, cards e inputs em uma frase ("táteis e confiantes" vs. "refinados e contidos").

Leve uma linha do PRODUCT.md apenas quando ela for um compromisso de marca duradouro que de fato restringe o sistema visual. Estratégia de página e conceitos de superfície não pertencem aqui.

### Passo 4: Escreva o DESIGN.md

O arquivo começa com o frontmatter YAML preparado no Passo 2b (esquema documentado no topo desta referência) e depois o corpo markdown usando a estrutura canônica abaixo.

```markdown
---
name: [Project Title]
description: [one-line tagline]
colors:
  # ... staged frontmatter from Step 2b
---

# Design System: [Project Title]

## Overview

**Creative North Star: "[Named metaphor in quotes]"**

[2-3 paragraph holistic description: personality, density, and aesthetic philosophy. Start from the North Star and work outward. State only confirmed visual rejections. End with a short **Key Characteristics:** bullet list.]

## Colors

[Describe the palette character in one sentence.]

### Primary
- **[Descriptive Name]** (#HEX / oklch(...)): [Where and why this color is used. Be specific about context, not just role.]

### Secondary (optional; omit if the project has only one accent)
- **[Descriptive Name]** (#HEX): [Role.]

### Tertiary (optional)
- **[Descriptive Name]** (#HEX): [Role.]

### Neutral
- **[Descriptive Name]** (#HEX): [Text / background / border / divider role.]
- [...]

### Named Rules (optional, powerful)
**The [Rule Name] Rule.** [Short, forceful prohibition or doctrine, e.g. "The One Voice Rule. The primary accent is used on ≤10% of any given screen. Its rarity is the point."]

## Typography

**Display Font:** [Family] (with [fallback])
**Body Font:** [Family] (with [fallback])
**Label/Mono Font:** [Family, if distinct]

**Character:** [1-2 sentence personality description of the pairing.]

### Hierarchy
- **Display** ([weight], [size/clamp], [line-height]): [Purpose; where it appears.]
- **Headline** ([weight], [size], [line-height]): [Purpose.]
- **Title** ([weight], [size], [line-height]): [Purpose.]
- **Body** ([weight], [size], [line-height]): [Purpose. Include max line length like 65–75ch if relevant.]
- **Label** ([weight], [size], [letter-spacing], [case if uppercase]): [Purpose.]

### Named Rules (optional)
**The [Rule Name] Rule.** [Short doctrine about type use.]

## Layout

[Describe the grid or spatial model, container behavior, density, responsive changes, and the spacing rhythm. Include exact values only when observed.]

## Elevation & Depth

[One paragraph: does this system use shadows, tonal layering, or a hybrid? If "no shadows", say so explicitly and describe how depth is conveyed instead.]

### Shadow Vocabulary (if applicable)
- **[Role name]** (`box-shadow: [exact value]`): [When to use it.]
- [...]

### Named Rules (optional)
**The [Rule Name] Rule.** [e.g. "The Flat-By-Default Rule. Surfaces are flat at rest. Shadows appear only as a response to state (hover, elevation, focus)."]

## Shapes

[Describe the form language: corner/radius strategy, borders, clipping, and any recurring silhouette or geometry.]

## Components

For each component, lead with a short character line, then specify shape, color assignment, states, and any distinctive behavior.

### Buttons
- **Shape:** [radius described, exact value in parens]
- **Primary:** [color assignment + padding, in semantic + exact terms]
- **Hover / Focus:** [transitions, treatments]
- **Secondary / Ghost / Tertiary (if applicable):** [brief description]

### Chips (if used)
- **Style:** [background, text color, border treatment]
- **State:** [selected / unselected, filter / action variants]

### Cards / Containers
- **Corner Style:** [radius]
- **Background:** [colors used]
- **Shadow Strategy:** [reference Elevation section]
- **Border:** [if any]
- **Internal Padding:** [scale]

### Inputs / Fields
- **Style:** [stroke, background, radius]
- **Focus:** [treatment, e.g. glow, border shift, etc.]
- **Error / Disabled:** [if applicable]

### Navigation
- **Style, typography, default/hover/active states, mobile treatment.**

### [Signature Component] (optional; if the project has a distinctive custom component worth documenting)
[Description.]

## Do's and Don'ts

Concrete visual guardrails grounded in the incumbent implementation or the user's chosen world. Lead each with "Do" or "Don't" and include exact values only when established. Do not turn a task-specific concept or surface strategy into a system-wide prohibition.

### Do:
- **Do** [specific prescription with exact values / named rule].
- **Do** [...]

### Don't:
- **Don't** [specific prohibition confirmed by the incumbent system or the user].
- **Don't** [...]
- **Don't** [...]
```

### Passo 4b: Escreva o sidecar .impeccable/design.json (somente extensões)

O frontmatter é dono dos primitivos de token (colors, typography, rounded, spacing, components). O sidecar em `.impeccable/design.json` carrega **o que o esquema do Stitch não comporta**: rampas tonais por cor, tokens de sombra/elevação, tokens de movimento, breakpoints, snippets completos de HTML/CSS dos componentes (o painel os renderiza em um shadow DOM) e a narrativa (north star, regras, do's/don'ts). Ele estende o frontmatter, não o duplica.

Regenere o sidecar sempre que regenerar o `DESIGN.md` da raiz. Se o usuário pedir apenas para atualizar o sidecar (ex.: a partir do aviso de desatualização do painel live), preserve o `DESIGN.md` e escreva somente `.impeccable/design.json`.

#### Esquema

```json
{
  "schemaVersion": 2,
  "generatedAt": "ISO-8601 string",
  "title": "Design System: [Project Title]",
  "extensions": {
    "colorMeta": {
      "primary":        { "role": "primary",  "displayName": "Editorial Magenta", "canonical": "oklch(60% 0.25 350)", "tonalRamp": ["...", "...", "..."] },
      "cool-paper": { "role": "neutral",  "displayName": "Cool Paper",    "canonical": "oklch(96% 0.005 230)", "tonalRamp": ["...", "...", "..."] }
    },
    "typographyMeta": {
      "display": { "displayName": "Display", "purpose": "Hero headlines only." }
    },
    "shadows": [
      { "name": "ambient-low", "value": "0 4px 24px rgba(0,0,0,0.12)", "purpose": "Diffuse hover glow under accent elements." }
    ],
    "motion": [
      { "name": "ease-standard", "value": "cubic-bezier(0.4, 0, 0.2, 1)", "purpose": "Default easing for state transitions." }
    ],
    "breakpoints": [
      { "name": "sm", "value": "640px" }
    ]
  },
  "components": [
    {
      "name": "Primary Button",
      "kind": "button | input | nav | chip | card | custom",
      "refersTo": "button-primary",
      "description": "One-line what and when.",
      "html": "<button class=\"ds-btn-primary\">SAVE CHANGES</button>",
      "css": ".ds-btn-primary { background: #191c1d; color: #fff; padding: 16px 48px; letter-spacing: 0.05em; text-transform: uppercase; font-weight: 500; border: none; border-radius: 0; transition: background 0.2s, transform 0.2s; } .ds-btn-primary:hover { background: oklch(60% 0.25 350); transform: translateY(-2px); }"
    }
  ],
  "narrative": {
    "northStar": "The Editorial Sanctuary",
    "overview": "2-3 paragraphs of the philosophy, pulled from DESIGN.md Overview section.",
    "keyCharacteristics": ["...", "..."],
    "rules": [{ "name": "The One Voice Rule", "body": "...", "section": "colors|typography|elevation" }],
    "dos":   ["Do use ..."],
    "donts": ["Don't use ..."]
  }
}
```

**O que mudou em relação ao schemaVersion 1.** O sidecar antigo carregava arrays de primitivos de token (`tokens.colors[]`, `tokens.typography[]` etc.). Esses valores agora vivem no frontmatter. O sidecar carrega apenas os metadados que não cabem no frontmatter (rampas tonais, OKLCH canônico quando o hex é uma aproximação, nomes de exibição, indicações de papel), indexados pelo nome do token no frontmatter (`colorMeta.<token-name>`, `typographyMeta.<token-name>`). Os componentes continuam carregando HTML/CSS completos porque o conjunto de 8 propriedades do Stitch não os comporta.

#### Regras de tradução de componentes

Os campos `html` e `css` devem ser **snippets autocontidos, prontos para encaixar**, que renderizam corretamente quando injetados em um shadow DOM. O painel os aplica diretamente: sem pós-processamento, sem runtime de framework.

1. **Expansão do Tailwind.** Se a fonte usa Tailwind (className="bg-primary text-white rounded-lg px-6 py-3"), expanda cada utilitário em propriedades CSS literais na string `css`. **Não** referencie classes do Tailwind; **não** presuma que um bundle CSS do Tailwind esteja carregado. Cada componente é autocontido.
2. **Resolução de tokens.** Se o projeto expõe tokens como propriedades customizadas de CSS em `:root` (ex.: `--color-primary`, `--radius-md`), referencie-os via `var(--color-primary)`; eles são herdados através do shadow DOM e permanecem vinculados ao vivo. Se os tokens vivem apenas em objetos de tema JS (styled-components, CSS-in-JS), resolva-os em valores literais no momento da geração.
3. **Ícones.** Inline como SVG. Não referencie pacotes Lucide/Heroicons, fontes de ícones nem `<img src="...">`. Um ícone típico tem 16-24px; copie os dados de path do SVG diretamente.
4. **Estados.** Inclua regras `:hover`, `:focus-visible` e (se fizer sentido) `:active` inline. Um snapshot estático só com o padrão faz o painel parecer morto. Regras de hover + foco no CSS fazem-no parecer vivo.
5. **Excesso de reset.** Extraia apenas o CSS *distintivo* do componente (background, cor, padding, border-radius, tipografia, transição). Pule resets universais (`box-sizing: border-box`, `line-height: inherit`, `-webkit-font-smoothing`). O painel já tem uma tela neutra; não reenvie resets.
6. **Nomes de classe com escopo.** Prefixe toda classe com `ds-` (ex.: `ds-btn-primary`, `ds-input-search`) para que o CSS do componente não colida com o CSS de outros componentes no mesmo shadow DOM.

#### O que incluir

Mire em um conjunto enxuto de **5-10 componentes** que melhor representem o sistema visual:

- **Primitivos canônicos (inclua sempre, se o projeto os tiver):** botão (cada variante como uma entrada de componente separada), input/campo de texto, navegação, chip/tag, card.
- **Componentes de assinatura (inclua se forem distintivos):** os padrões customizados recorrentes que de fato definem o sistema implementado.
- **Pule o resto.** Componentes utilitários, blocos de formulário, layouts de wrapper: não vale a pena documentá-los, a menos que sejam visualmente distintivos.

Se o projeto **ainda não tem biblioteca de componentes** (landing page simples, projeto novo), sintetize primitivos canônicos a partir dos tokens usando padrões de boas práticas coerentes com as regras do DESIGN.md. Todo `.impeccable/design.json` tem *algo* para renderizar, mesmo no dia zero.

#### Rampas tonais

Para cada token de cor, gere um array `tonalRamp` de 8 passos: do escuro ao claro, mesmo matiz e croma, com luminosidade escalonada de ~15% a ~95%. O painel renderiza isso como uma faixa sob a amostra de cor. Se o projeto já define uma escala tonal (família `surface-container-low` do Material, estilo Tailwind `blue-50..blue-900`), use esses valores. Caso contrário, sintetize em OKLCH.

#### Mapeamento da narrativa

Extraia diretamente do DESIGN.md que você acabou de escrever:

- `narrative.northStar` → a linha `**Creative North Star: "..."**` do Overview
- `narrative.overview` → os parágrafos de filosofia do Overview
- `narrative.keyCharacteristics` → a lista com marcadores `**Key Characteristics:**`
- `narrative.rules` → toda `**The [Name] Rule.** [body]` em todas as seções, marcada com `section`
- `narrative.dos` / `narrative.donts` → as listas com marcadores de Do's and Don'ts, literalmente

Não reescreva. O painel mostra isso como contexto secundário recolhível; a mesma voz que está no Markdown é mantida.

### Passo 5: Confirme e refine

1. Mostre ao usuário o DESIGN.md completo que você escreveu. Destaque brevemente as escolhas criativas não óbvias (nomes descritivos de cores, linguagem de atmosfera, regras nomeadas).
2. Mencione que `.impeccable/design.json` também foi escrito ao lado; o painel live agora renderizará os primitivos reais de botão/input/nav deste projeto em vez de aproximações genéricas.
3. Ofereça refinar qualquer seção: "Quer que eu revise uma seção, acrescente padrões de componentes que deixei passar ou ajuste a linguagem de atmosfera?"

A sua própria escrita é a fonte mais recente; os comandos seguintes nesta sessão não precisam recarregar.

## Modo seed

Para projetos que ainda não têm sistema visual a extrair. Produz um esqueleto de mundo visual escolhido pelo usuário, não uma especificação de tokens fabricada.

### Passo 1: Passe pela oficina do new-work

O PRODUCT.md é pré-requisito. Se estiver faltando, carregue o [init.md](init.md) e conclua primeiro a entrevista de produto dele. Não crie uma identidade visual sem um contexto de produto duradouro.

Se o PRODUCT.md existir, carregue o [new-work.md](new-work.md) e resolva a autoridade visual. O modo seed exige uma primeira superfície concreta: use o alvo que o usuário nomeou ou pergunte o que ele quer fazer primeiro. Execute o fluxo **Create or replace the visual world** do new-work e depois **Commit the world**, para que o mundo visual e sua primeira expressão sejam escolhidos juntos. Pare após a semente direcional do DESIGN.md e o briefing da superfície; não implemente. Um usuário simulado estruturado conta como o usuário e precisa receber a mesma escolha.

Se o new-work já concluiu a oficina nesta sessão, use diretamente a direção escolhida. Não pergunte de novo.

### Passo 2: Escreva o DESIGN.md semente

Use a ordem canônica de seções do modo scan. Preencha com a direção selecionada na oficina e deixe os fatos de implementação não resolvidos como placeholders honestos. A semente compromete um mundo visual e suas invariantes; ela não finge que tokens de implementação já existem.

Inicie o arquivo com:

```markdown
<!-- SEED: established with the user before implementation; re-run /impeccable document once there's code to capture the actual tokens and components. -->
```

Orientações por seção no modo seed:

- **Overview**: a tese de design escolhida, o comportamento de layout, o caráter dos materiais, a postura em relação a imagens, a gramática de movimento e a assinatura reutilizável. Mantenha a expressão da primeira superfície selecionada no briefing dela; não promova a composição dela ao mundo visual global.
- **Colors**: a estratégia de paleta e os papéis selecionados. Inclua valores apenas quando o usuário, um ativo existente ou a exploração do new-work os tiver estabelecido; caso contrário, marque-os como `[to be resolved during implementation]`.
- **Typography**: o caráter tipográfico e a relação entre papéis selecionados. Inclua nomes de fontes apenas quando estabelecidos; caso contrário, marque o par como `[to be resolved during implementation]`.
- **Layout**: a gramática espacial e o comportamento responsivo selecionados, sem fingir que medidas exatas já estão definidas.
- **Elevation & Depth**: o comportamento de material e profundidade selecionado, declarado como invariante em vez de inferido de um preset genérico.
- **Shapes**: a linguagem de formas e cantos selecionada.
- **Components**: omita por completo; ainda não existem componentes.
- **Do's and Don'ts**: registre as salvaguardas duradouras confirmadas durante a escolha do mundo visual, não recusas locais da tarefa.

O modo seed escreve um frontmatter mínimo apenas com `name` e `description`; ainda sem colors, typography, rounded, spacing ou components. Os tokens reais chegam na próxima execução no modo scan. Pule o sidecar `.impeccable/design.json` no modo seed pelo mesmo motivo: não há nada para renderizar.

### Passo 3: Confirme

1. Mostre o DESIGN.md semente. Destaque que é uma semente (o marcador é o compromisso literal).
2. Diga ao usuário: "Execute `/impeccable document` de novo quando tiver algum código. Essa passada extrairá os tokens reais e gerará o sidecar."

A sua própria escrita é a fonte mais recente; não é preciso recarregar.

## Diretrizes de estilo

- **Frontmatter primeiro, prosa depois.** Os tokens vão no frontmatter YAML; a prosa os contextualiza. Não redefina o valor de um token em dois lugares; o frontmatter é normativo.
- **Leve apenas restrições de produto duradouras.** Um logo vinculante, um ativo de identidade, uma necessidade de acessibilidade ou um compromisso de marca do PRODUCT.md pode restringir o DESIGN.md. A estratégia de superfície fica no briefing da superfície.
- **Siga a especificação.** Use as oito seções canônicas em ordem e omita as irrelevantes. Coloque as orientações de movimento junto ao mundo visual ou componente que elas afetam, em vez de criar um grupo de tokens que o esquema não suporta.
- **Descritivo > técnico**: "Bordas suavemente curvas (raio de 8px)" > "rounded-lg". Inclua o valor técnico entre parênteses e comece pela descrição.
- **Funcional > decorativo**: para cada token, explique ONDE e POR QUE ele é usado, não apenas O QUE ele é.
- **Valores exatos entre parênteses**: códigos hex, valores em px/rem, pesos de fonte; sempre o número entre parênteses ao lado da descrição.
- **Use Named Rules**: `**The [Name] Rule.** [short doctrine]`. Elas são memoráveis, citáveis e muito mais marcantes para consumidores de IA do que listas com marcadores. As próprias saídas do Stitch as usam bastante ("The No-Line Rule", "The Ghost Border Fallback"). Mire em 1-3 por seção.
- **Seja decisivo onde as evidências são decisivas.** Use linguagem firme para invariantes reais e linguagem mais suave para orientações provisórias.
- **Use testes de auditoria concretos apenas quando eles se basearem no sistema observado ou em uma decisão confirmada do usuário.** Um teste de uma frase vale mais que um parágrafo de princípios.
- **Referencie o PRODUCT.md seletivamente.** A verdade do produto explica por que o mundo visual se encaixa; ela não fornece, por padrão, composição de página nem uma lista de proibições visuais.
- **Agrupe as cores por papel**, não por ordem de hex nem de matiz. Primary / Secondary / Tertiary / Neutral é a ordem da especificação.

## Armadilhas

- Não cole nomes de classes CSS crus. Traduza para linguagem descritiva.
- Não extraia todos os tokens. Pare no que é realmente reutilizado; valores avulsos poluem o sistema.
- Não invente componentes que não existem. Se o projeto só tem botões e cards, documente só esses.
- Não sobrescreva um DESIGN.md existente sem perguntar.
- Não duplique conteúdo do PRODUCT.md. O DESIGN.md é estritamente visual.
- Não substitua seções canônicas por quase-sinônimos. Coloque layout e comportamento responsivo em `Layout`; coloque o movimento junto ao mundo visual ou componente afetado.
- Não renomeie seções, nem um pouco. "Colors", não "Color Palette & Roles". "Typography", não "Typography Rules". O parsing das ferramentas depende dos títulos exatos.
- Não duplique valores de token entre frontmatter e prosa. Se uma cor está em `colors.primary` como hex, a prosa pode nomeá-la e descrever seu papel, mas não deve reafirmar um hex diferente. O frontmatter é normativo.
- Não invente grupos de tokens no frontmatter fora do esquema do Stitch (nada de `motion:`, `breakpoints:`, `shadows:` no nível superior). O esquema Zod do Stitch só aceita `colors`, `typography`, `rounded`, `spacing`, `components`. Todo o resto pertence às `extensions` do sidecar.
