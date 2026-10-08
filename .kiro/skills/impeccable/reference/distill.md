Reduza um design à sua essência. Remova tudo o que não justifica seu lugar: elementos redundantes, informações repetidas, ruído decorativo, complexidade cosmética.


---

## Avalie o estado atual

Analise o que faz o design parecer complexo ou poluído:

1. **Identifique as fontes de complexidade**:
   - **Elementos demais**: botões competindo entre si, informações redundantes, poluição visual
   - **Variação excessiva**: cores, fontes, tamanhos e estilos demais, sem propósito
   - **Sobrecarga de informação**: tudo visível ao mesmo tempo, sem divulgação progressiva
   - **Ruído visual**: bordas, sombras, fundos e decorações desnecessários
   - **Hierarquia confusa**: não fica claro o que mais importa
   - **Inchaço de funcionalidades**: opções, ações ou caminhos a seguir em excesso

2. **Encontre a essência**:
   - Qual é o objetivo principal do usuário? (Deve haver UM só)
   - O que é realmente necessário e o que é apenas desejável?
   - O que pode ser removido, ocultado ou combinado?
   - Quais são os 20% que entregam 80% do valor?

Se algum desses pontos não estiver claro a partir do código, não chute. Pergunte diretamente ao usuário para esclarecer o que você não consegue inferir.

**CRÍTICO**: simplicidade não é remover funcionalidades. É remover obstáculos entre os usuários e seus objetivos. Cada elemento deve justificar sua existência.

## Planeje a simplificação

Crie uma estratégia de edição implacável:

- **Propósito central**: qual é a ÚNICA coisa que isto deve realizar?
- **Elementos essenciais**: o que é de fato necessário para cumprir esse propósito?
- **Divulgação progressiva**: o que pode ficar oculto até ser necessário?
- **Oportunidades de consolidação**: o que pode ser combinado ou integrado?

**IMPORTANTE**: simplificar é difícil. Exige dizer não a boas ideias para abrir espaço a uma ótima execução. Seja implacável.

## Simplifique o design

Remova a complexidade de forma sistemática nestas dimensões:

### Arquitetura da informação
- **Reduza o escopo**: remova ações secundárias, funcionalidades opcionais, informações redundantes
- **Divulgação progressiva**: oculte a complexidade atrás de pontos de entrada claros (acordeões, modais, fluxos passo a passo)
- **Combine ações relacionadas**: junte botões semelhantes, consolide formulários, agrupe conteúdo relacionado
- **Hierarquia clara**: UMA ação principal, poucas ações secundárias, todo o resto terciário ou oculto
- **Remova redundâncias**: se já foi dito em outro lugar, não repita aqui

### Simplificação visual
- **Reduza a paleta de cores**: use 1 ou 2 cores mais neutros, não 5 a 7 cores
- **Limite a tipografia**: uma família de fontes, no máximo 3 ou 4 tamanhos, 2 ou 3 pesos
- **Remova decorações**: elimine bordas, sombras e fundos que não servem à hierarquia nem à função
- **Achate a estrutura**: reduza o aninhamento, remova contêineres desnecessários; nunca aninhe cards dentro de cards
- **Remova cards desnecessários**: cards não são necessários para o layout básico; use espaçamento e alinhamento no lugar
- **Espaçamento consistente**: use uma única escala de espaçamento, remova lacunas arbitrárias

### Simplificação do layout
- **Fluxo linear**: substitua grids complexos por um fluxo vertical simples sempre que possível
- **Remova barras laterais**: traga o conteúdo secundário para o fluxo ou oculte-o
- **Largura total**: use o espaço disponível com generosidade em vez de layouts complexos de várias colunas
- **Alinhamento consistente**: escolha à esquerda ou centralizado e mantenha
- **Espaço em branco generoso**: deixe o conteúdo respirar, não aperte tudo

### Simplificação da interação
- **Reduza as escolhas**: menos botões, menos opções, um caminho mais claro a seguir (o paradoxo da escolha é real)
- **Padrões inteligentes**: torne automáticas as escolhas comuns, pergunte só quando necessário
- **Ações inline**: substitua fluxos em modal por edição inline sempre que possível
- **Remova etapas**: o fluxo pode perder uma etapa?
- **Próxima ação clara**: UMA próxima ação óbvia, não cinco competindo entre si

### Simplificação do conteúdo
- **Copy mais curta**: corte cada frase pela metade e depois faça isso de novo
- **Voz ativa**: "Salvar alterações", não "As alterações serão salvas"
- **Remova o jargão**: linguagem simples sempre vence
- **Estrutura escaneável**: parágrafos curtos, tópicos, títulos claros
- **Somente informação essencial**: remova enrolação de marketing, juridiquês, ressalvas
- **Remova copy redundante**: nada de títulos que repetem a introdução, nada de explicações repetidas; diga uma vez só

### Simplificação do código
- **Remova código não utilizado**: CSS morto, componentes sem uso, arquivos órfãos
- **Achate as árvores de componentes**: reduza a profundidade de aninhamento
- **Consolide estilos**: junte estilos semelhantes, use utilitários de forma consistente
- **Reduza variantes**: esse componente precisa de 12 variações, ou 3 cobrem 90% dos casos?

**NUNCA**:
- Remova funcionalidades necessárias (simplicidade ≠ ausência de funcionalidades)
- Sacrifique a acessibilidade em nome da simplicidade (rótulos claros e ARIA continuam obrigatórios)
- Deixe as coisas tão simples que fiquem obscuras (mistério ≠ minimalismo)
- Remova informações de que os usuários precisam para tomar decisões
- Elimine a hierarquia por completo (algumas coisas devem se destacar)
- Simplifique demais domínios complexos (ajuste a complexidade à complexidade real da tarefa)

## Verifique a simplificação

Garanta que a simplificação melhore a usabilidade:

- **Conclusão de tarefas mais rápida**: os usuários conseguem atingir seus objetivos mais depressa?
- **Carga cognitiva reduzida**: ficou mais fácil entender o que fazer?
- **Continua completo**: todas as funcionalidades necessárias ainda estão acessíveis?
- **Hierarquia mais clara**: é óbvio o que mais importa?
- **Melhor desempenho**: o design mais simples carrega mais rápido?

## Documente a complexidade removida

Se você removeu funcionalidades ou opções:
- Documente por que elas foram removidas
- Avalie se precisam de pontos de acesso alternativos
- Anote qualquer feedback de usuários a ser monitorado

Quando os cortes parecerem certos, passe para `/impeccable polish` para a etapa final. Como disse Antoine de Saint-Exupéry: "A perfeição é alcançada não quando não há mais nada a acrescentar, mas quando não há mais nada a retirar."
