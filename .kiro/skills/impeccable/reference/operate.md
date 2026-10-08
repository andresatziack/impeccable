# Profundidade do modo Operate (e notas sobre Read)

Quando o design SERVE ao produto: UIs de apps, dashboards administrativos, painéis de configurações, tabelas de dados, ferramentas, superfícies autenticadas, qualquer coisa em que o usuário esteja executando uma tarefa. O essencial está nos modos do SKILL.md e em [craft-floor.md](craft-floor.md); este arquivo é a profundidade estendida, escrito para superfícies **Operate** (operar). Superfícies **Read** (ler) (documentação, guias, textos longos) seguem o modo Read do SKILL.md mais as regras de tipografia e consistência deste arquivo; nelas, a medida da prosa e a navegação importam mais do que a densidade de componentes.

## O teste de desleixo do produto

Aqui, a familiaridade muitas vezes é um recurso. O teste é se um usuário fluente na categoria consegue confiar na interface imediatamente ou precisa parar a cada componente sutilmente fora do lugar.

O modo de falha de uma UI de produto não é a falta de personalidade, é a estranheza sem propósito: botões decorados demais, controles de formulário incompatíveis entre si, movimento gratuito, fontes de display onde deveriam estar rótulos, affordances inventadas para tarefas padrão. O padrão é a familiaridade conquistada. A ferramenta deve desaparecer dentro da tarefa.

## Tipografia

- **Uma família costuma bastar.** UIs de produto não precisam de pareamento display/corpo. Uma sans bem ajustada dá conta de títulos, botões, rótulos, corpo de texto e dados.
- **Escala fixa em rem, não fluida.** Títulos dimensionados com clamp não servem a UIs de produto. Os usuários visualizam com DPI consistente, e um h1 fluido que encolhe numa barra lateral fica pior, não melhor.
- **Razão de escala mais apertada.** 1,125–1,2 entre os degraus é o típico. Há mais elementos tipográficos aqui do que em superfícies de marca; um contraste exagerado cria ruído.
- **O comprimento de linha ainda vale para a prosa** (65–75ch). Dados e UI compacta podem ser mais densos; tabelas com 120ch+ não têm problema.

## Cor

O produto usa Restrained (contido) por padrão. Uma única superfície pode merecer Committed (comprometido) (um dashboard em que uma cor de categoria sustenta um relatório, um fluxo de onboarding com uma tela de boas-vindas encharcada de cor), mas Restrained é o piso.

- Vocabulário semântico rico em estados: hover, focus, active, disabled, selected, loading, error, warning, success, info. Padronize-os.
- A cor de destaque é usada apenas para ações primárias, seleção atual e indicadores de estado, não para decoração.
- Uma segunda camada neutra para barras laterais, barras de ferramentas e painéis (levemente mais fria ou mais quente do que a superfície de conteúdo).

## Layout

- O comportamento responsivo é estrutural (recolher a barra lateral, tabela responsiva, colunas guiadas por breakpoints), não tipografia fluida.

## Componentes

Todo componente interativo tem: default, hover, focus, active, disabled, loading, error. Não entregue com metade deles.

- Estados skeleton para carregamento, não spinners no meio do conteúdo.
- Estados vazios que ensinam a interface, não "nada por aqui".
- Affordances consistentes em toda a superfície. O mesmo formato de botão. O mesmo vocabulário de controles de formulário. O mesmo estilo de ícone.
- Sobreposições escapam do seu contêiner. Um dropdown posicionado de forma absoluta dentro de um ancestral com `overflow: hidden` ou `overflow: auto` é cortado; recorra a `<dialog>`, à API de popover, a `position: fixed` ou a um portal.

## Movimento

- 150–250 ms na maioria das transições. Os usuários estão em fluxo; não os faça esperar por uma coreografia.
- O movimento transmite estado, não decoração. Mudança de estado, feedback, carregamento, revelação: nada além disso.
- Nada de sequências orquestradas de carregamento de página. O produto carrega dentro de uma tarefa; os usuários não querem assistir ao carregamento.

## Restrições de produto

- Movimento decorativo que não transmite estado.
- Vocabulário de componentes inconsistente entre telas. Se o botão "salvar" tem aparência diferente em dois lugares, um deles está errado.
- Fontes de display em rótulos, botões e dados da UI.
- Reinventar affordances padrão por estilo (barras de rolagem personalizadas, controles de formulário esquisitos, modais fora do padrão).
- Cor pesada ou destaques em saturação total em estados inativos.
- Modal como primeira ideia. Modais geralmente são preguiça. Esgote primeiro as alternativas inline / progressivas.

## Permissões de produto

O produto pode se dar ao luxo de coisas que superfícies de marca não podem.

- Fontes do sistema e sans familiares como padrão.
- Padrões de navegação convencionais: barra superior + navegação lateral, breadcrumbs, abas, paletas de comandos.
- Densidade. Tabelas com muitas linhas, painéis com muitos rótulos, informação densa quando os usuários precisam dela.
- Consistência acima de surpresa. O mesmo vocabulário visual de tela em tela é uma virtude; o encantamento fica reservado para momentos, não para páginas.
