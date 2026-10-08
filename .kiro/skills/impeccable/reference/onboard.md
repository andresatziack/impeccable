> **Contexto adicional necessário**: o "momento aha" que você quer que os usuários alcancem e o nível de experiência dos usuários.

Leve os usuários ao primeiro valor o mais rápido possível. A função do onboarding não é ensinar o produto. A função dele é levar as pessoas ao momento que prova que o produto vale o tempo delas.

## Avaliar as necessidades de onboarding

Entenda o que os usuários precisam aprender e por quê:

1. **Identifique o desafio**:
   - O que os usuários estão tentando realizar?
   - O que é confuso ou pouco claro na experiência atual?
   - Onde os usuários travam ou desistem?
   - Qual é o "momento aha" que queremos que os usuários alcancem?

2. **Entenda os usuários**:
   - Qual é o nível de experiência deles? (Iniciantes, usuários avançados, misto?)
   - Qual é a motivação deles? (Animados e explorando? Obrigados pelo trabalho?)
   - Quanto tempo eles vão dedicar? (5 minutos? 30 minutos?)
   - Que alternativas eles conhecem? (Vindos de um concorrente? Novos na categoria?)

3. **Defina sucesso**:
   - Qual é o mínimo que os usuários precisam aprender para ter sucesso?
   - Qual é a ação principal que queremos que eles realizem? (Primeiro projeto? Primeiro convite?)
   - Como sabemos que o onboarding funcionou? (Taxa de conclusão? Tempo até o valor?)

**CRÍTICO**: O onboarding deve levar os usuários ao valor o mais rápido possível, não ensinar tudo o que for possível.

## Princípios de onboarding

Siga estes princípios centrais:

### Mostre, não conte
- Demonstre com exemplos funcionais, não apenas descrições
- Ofereça funcionalidade real no onboarding, não um modo tutorial separado
- Use revelação progressiva, ensine uma coisa de cada vez

### Torne-o opcional (quando possível)
- Deixe usuários experientes pularem o onboarding
- Não bloqueie o acesso ao produto
- Ofereça opções como "Pular" ou "Vou explorar por conta própria"

### Tempo até o valor
- Leve os usuários ao "momento aha" deles o quanto antes
- Apresente primeiro os conceitos mais importantes
- Ensine os 20% que entregam 80% do valor
- Guarde recursos avançados para descoberta contextual

### Contexto em vez de cerimônia
- Ensine recursos quando os usuários precisarem deles, não de antemão
- Estados vazios são oportunidades de onboarding
- Tooltips e dicas no ponto de uso

### Respeite a inteligência do usuário
- Não seja condescendente nem explique demais
- Seja conciso e claro
- Presuma que os usuários conseguem entender padrões comuns

## Projetar experiências de onboarding

Crie o onboarding adequado ao contexto:

### Onboarding inicial do produto

**Tela de boas-vindas**:
- Proposta de valor clara (o que é este produto?)
- O que os usuários vão aprender/realizar
- Estimativa de tempo (honesta quanto ao compromisso)
- Opção de pular (para usuários experientes)

**Configuração da conta**:
- Mínimo de informações obrigatórias (colete mais depois)
- Explique por que você está pedindo cada informação
- Padrões inteligentes sempre que possível
- Login social quando apropriado

**Introdução aos conceitos centrais**:
- Apresente de 1 a 3 conceitos centrais (não tudo)
- Use linguagem simples e exemplos
- Interativo quando possível (fazer, não apenas ler)
- Indicação de progresso (etapa 1 de 3)

**Primeiro sucesso**:
- Guie os usuários para realizarem algo real
- Exemplos ou templates pré-preenchidos
- Comemore a conclusão (mas sem exagero)
- Próximos passos claros

### Descoberta e adoção de recursos

**Estados vazios**:
Em vez de espaço em branco, mostre:
- O que vai aparecer aqui (descrição + captura de tela/ilustração)
- Por que isso é valioso
- CTA clara para criar o primeiro item
- Opção de exemplo ou template

Exemplo:
```
No projects yet
Projects help you organize your work and collaborate with your team.
[Create your first project] or [Start from template]
```

**Tooltips contextuais**:
- Aparecem no momento relevante (na primeira vez que o usuário vê o recurso)
- Apontam diretamente para o elemento de UI relevante
- Explicação breve + benefício
- Dispensáveis (com a opção "Não mostrar novamente")
- Link opcional "Saiba mais"

**Anúncios de recursos**:
- Destaque recursos novos quando forem lançados
- Mostre o que há de novo e por que importa
- Deixe os usuários experimentarem imediatamente
- Dispensáveis

**Onboarding progressivo**:
- Ensine recursos quando os usuários os encontrarem
- Selos ou indicadores em recursos novos/não usados
- Libere a complexidade gradualmente (não mostre todas as opções de imediato)

### Tours guiados e walkthroughs

**Quando usar**:
- Interfaces complexas com muitos recursos
- Mudanças significativas em um produto existente
- Ferramentas específicas de um setor que exigem conhecimento de domínio

**Como projetar**:
- Destaque elementos específicos da UI (escureça o resto da página)
- Mantenha as etapas curtas (no máximo 3 a 7 etapas por tour)
- Permita que os usuários avancem pelo tour livremente
- Inclua a opção "Pular tour"
- Torne-o reproduzível novamente (menu de ajuda)

**Boas práticas**:
- Interativo em vez de passivo (deixe os usuários clicarem em botões reais)
- Foque no fluxo de trabalho, não nos recursos ("Crie um projeto", não "Este é o botão de projeto")
- Forneça dados de exemplo para que as ações funcionem

### Tutoriais interativos

**Quando usar**:
- Os usuários precisam de prática mão na massa
- Os conceitos são complexos ou desconhecidos
- Muita coisa em jogo (melhor praticar em um ambiente seguro)

**Como projetar**:
- Ambiente sandbox com dados de exemplo
- Objetivos claros ("Crie um gráfico mostrando as vendas por região")
- Orientação passo a passo
- Validação (confirme que fizeram certo)
- Momento de formatura (você está pronto!)

### Documentação e ajuda

**Ajuda dentro do produto**:
- Links de ajuda contextual por toda a interface
- Referência de atalhos de teclado
- Central de ajuda pesquisável
- Tutoriais em vídeo para fluxos de trabalho complexos

**Padrões de ajuda**:
- Ícone `?` perto de recursos complexos
- Links "Saiba mais" em tooltips
- Dicas de atalhos de teclado (`⌘K` mostrado na caixa de busca)

## Design de estados vazios

Todo estado vazio precisa de:

### O que vai estar aqui
"Seus projetos recentes vão aparecer aqui"

### Por que isso importa
"Projetos ajudam você a organizar seu trabalho e colaborar com sua equipe"

### Como começar
[Criar projeto] ou [Importar de um template]

### Interesse visual
Ilustração ou ícone (não apenas texto em uma página em branco)

### Ajuda contextual
"Precisa de ajuda para começar? [Assista ao tutorial de 2 min]"

**Tipos de estado vazio**:
- **Primeiro uso**: Nunca usou este recurso (enfatize o valor, ofereça um template)
- **Limpo pelo usuário**: Excluiu tudo intencionalmente (abordagem leve, fácil de recriar)
- **Sem resultados**: A busca ou o filtro não retornou nada (sugira outra consulta, limpe os filtros)
- **Sem permissões**: Não consegue acessar (explique por quê e como obter acesso)
- **Estado de erro**: Falha ao carregar (explique o que aconteceu, opção de tentar novamente)

## Padrões de implementação

### Abordagens técnicas:

**Bibliotecas de tooltip**: Tippy.js, Popper.js
**Bibliotecas de tour**: Intro.js, Shepherd.js, React Joyride
**Padrões de modal**: Focus trap, backdrop, ESC para fechar
**Acompanhamento de progresso**: LocalStorage para estados de "visto"
**Analytics**: Acompanhe a conclusão e os pontos de abandono

**Padrões de armazenamento**:
```javascript
// Track which onboarding steps user has seen
localStorage.setItem('onboarding-completed', 'true');
localStorage.setItem('feature-tooltip-seen-reports', 'true');
```

**IMPORTANTE**: Não mostre o mesmo onboarding duas vezes (é irritante). Acompanhe a conclusão e respeite as dispensas.

**NUNCA**:
- Force os usuários a passar por um onboarding longo antes de poderem usar o produto
- Seja condescendente com os usuários com explicações óbvias
- Mostre o mesmo tooltip repetidamente (respeite as dispensas)
- Bloqueie toda a UI durante o tour (deixe os usuários explorarem)
- Crie um modo tutorial separado, desconectado do produto real
- Sobrecarregue com informações de antemão (revelação progressiva!)
- Esconda o "Pular" ou dificulte encontrá-lo
- Esqueça os usuários que retornam (não mostre o onboarding inicial novamente)

## Verificar a qualidade do onboarding

Teste com usuários reais:

- **Tempo até a conclusão**: Os usuários conseguem concluir o onboarding rapidamente?
- **Compreensão**: Os usuários entendem depois de concluir?
- **Ação**: Os usuários dão o próximo passo desejado?
- **Taxa de pulo**: Muitos usuários estão pulando? (Talvez esteja longo demais ou sem valor)
- **Taxa de conclusão**: Os usuários estão concluindo? (Se for baixa, simplifique)
- **Tempo até o valor**: Quanto tempo até os usuários obterem o primeiro valor?

Quando os usuários chegarem rápido ao momento aha e não desistirem, passe para `/impeccable polish` para a etapa final.
