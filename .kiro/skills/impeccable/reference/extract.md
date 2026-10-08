# Fluxo do extract

Identifique padrões, componentes e tokens de design reutilizáveis e, em seguida, extraia-os e consolide-os no design system para reutilização sistemática.

## Passo 1: Descubra o design system

Encontre o design system, a biblioteca de componentes ou o diretório de UI compartilhada. Entenda sua estrutura: organização dos componentes, convenções de nomenclatura, estrutura dos tokens de design, convenções de import/export.

**CRÍTICO**: Se não existir um design system, não crie um ainda. Pergunte diretamente ao usuário para esclarecer o que você não consegue inferir. Entenda primeiro a localização e a estrutura preferidas.

## Passo 2: Identifique padrões

Procure oportunidades de extração na área-alvo:

- **Componentes repetidos**: padrões de UI semelhantes usados 3 ou mais vezes (botões, cards, inputs)
- **Valores fixos no código**: cores, espaçamentos, tipografia e sombras que deveriam ser tokens
- **Variações inconsistentes**: múltiplas implementações do mesmo conceito
- **Padrões de composição**: padrões de layout ou de interação que se repetem (linhas de formulário, grupos de barra de ferramentas, estados vazios)
- **Estilos tipográficos**: combinações repetidas de font-size + peso + line-height
- **Padrões de animação**: combinações repetidas de easing, duração ou keyframes

Avalie o valor: só extraia coisas usadas 3 ou mais vezes com a mesma intenção. Abstração prematura é pior do que duplicação.

## Passo 3: Planeje a extração

Crie um plano sistemático:

- **Componentes a extrair**: quais elementos de UI se tornam componentes reutilizáveis?
- **Tokens a criar**: quais valores fixos no código se tornam tokens de design?
- **Variantes a suportar**: de quais variações cada componente precisa?
- **Convenções de nomenclatura**: nomes de componentes, de tokens e de props que correspondam aos padrões existentes
- **Caminho de migração**: como refatorar os usos existentes para consumir as novas versões compartilhadas

**IMPORTANTE**: Design systems crescem de forma incremental. Extraia o que é claramente reutilizável agora, não tudo o que um dia talvez possa ser reutilizável.

## Passo 4: Extraia e enriqueça

Construa versões melhoradas e reutilizáveis:

- **Componentes**: API de props clara com padrões sensatos, variantes adequadas para diferentes casos de uso, acessibilidade embutida (ARIA, navegação por teclado, gerenciamento de foco), documentação e exemplos de uso
- **Tokens de design**: nomenclatura clara (primitivos vs. semânticos), hierarquia e organização adequadas, documentação de quando usar cada token
- **Padrões**: quando usar este padrão, exemplos de código, variações e combinações

## Passo 5: Migre

Substitua os usos existentes pelas novas versões compartilhadas:

- **Encontre todas as instâncias**: procure os padrões que você extraiu
- **Substitua de forma sistemática**: atualize cada uso para consumir a versão compartilhada
- **Teste a fundo**: garanta paridade visual e funcional
- **Apague código morto**: remova as implementações antigas

## Passo 6: Documente

Atualize a documentação do design system:

- Adicione os novos componentes à biblioteca de componentes
- Documente o uso e os valores dos tokens
- Adicione exemplos e diretrizes
- Atualize qualquer Storybook ou catálogo de componentes

**NUNCA**:
- Extraia implementações pontuais e específicas de contexto sem generalizá-las
- Crie componentes tão genéricos que se tornem inúteis
- Extraia sem considerar as convenções existentes do design system
- Pule tipos TypeScript adequados ou a documentação das props
- Crie tokens para cada valor individual (tokens devem ter significado semântico)
- Extraia coisas que diferem em intenção (dois botões que parecem semelhantes, mas servem a propósitos diferentes, devem continuar separados)
