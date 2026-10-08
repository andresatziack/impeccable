Execute verificações **técnicas** sistemáticas de qualidade em um app nativo (`ios` / `android` / `adaptive`) e gere um relatório abrangente. Não corrija os problemas; documente-os para que outros comandos os resolvam.

Esta é uma auditoria no nível do código, não uma crítica de design. Audite a partir do código-fonte (SwiftUI / UIKit / Compose / React Native / Flutter); nenhuma ferramenta de navegador nem `impeccable detect` se aplica. Dê as notas com base na(s) referência(s) da plataforma: [ios.md](ios.md) / [android.md](android.md), ambas para `adaptive`. Leia-as antes de dar as notas, se o Setup ainda não tiver feito isso. O esqueleto do relatório espelha o de [audit.md](audit.md); mantenha os dois sincronizados ao alterá-lo.

## Varredura diagnóstica

Execute verificações abrangentes em 5 dimensões. Dê a cada dimensão uma nota de 0 a 4 usando os critérios abaixo.

### 1. Acessibilidade (VoiceOver / TalkBack)

**Verifique**:
- **Rótulos ausentes**: elementos interativos sem rótulos de acessibilidade, traits/papéis ou anúncios de estado
- **Ordem de leitura e de foco**: percurso ilógico, controles inalcançáveis, foco perdido na navegação
- **Escala de texto**: tamanhos fixos em pontos que anulam o Dynamic Type (iOS) ou px no lugar de sp (Android); layouts que cortam ou se sobrepõem em tamanhos grandes
- **Alvos de toque**: abaixo de 44 pt (iOS) / 48 dp (Android), ou amontoados sem espaçamento
- **Reduce Motion ignorado**: parallax e deslizamentos grandes sem alternativa de crossfade
- **Contraste**: texto com contraste insuficiente em qualquer uma das aparências, clara ou escura

**Nota de 0 a 4**: 0=Inutilizável com leitor de tela, 1=Lacunas graves (controles sem rótulo, sem escala), 2=Parcial (rótulos existem, ordem ou escala quebram), 3=Bom (pequenas lacunas), 4=Excelente (rotulado, ordenado, escala bem, Reduce Motion respeitado)

### 2. Desempenho

**Verifique**:
- **Inicialização lenta**: trabalho pesado na abertura antes do primeiro quadro
- **Listas não virtualizadas**: conteúdo longo sem reciclagem de FlatList / LazyColumn / List
- **Travamentos na thread principal**: trabalho síncrono em caminhos de rolagem ou de gesto, quadros perdidos em 60/120 Hz
- **Renderização desperdiçada**: re-renderizações (React Native) ou recomposições (Compose) desnecessárias; memoização/keys ausentes
- **Tratamento de imagens**: imagens em tamanho total decodificadas para miniaturas, sem cache
- **Peso do app**: bundle JS ou binário inchado, dependências não utilizadas

**Nota de 0 a 4**: 0=Travando em todo lugar, 1=Problemas graves (listas não virtualizadas, abertura lenta), 2=Parcial, 3=Bom (pequenas melhorias possíveis), 4=Excelente (abertura rápida, rolagem fluida, enxuto)

### 3. Aparência e temas

**Verifique**:
- **Cores fixas no código**: hex bruto em vez de cores semânticas do sistema (iOS) / papéis de cor do Material (Android) / tokens de design
- **Aparência escura quebrada**: variantes escuras ausentes, contraste ruim no escuro, inversões apressadas
- **Dynamic Color** (Android 12+): sem esquema estático de fallback, ou ignorado onde caberia
- **Materiais fora da plataforma**: materiais visuais feitos à mão onde se esperam materiais do sistema ou elevação tonal

**Nota de 0 a 4**: 0=Tudo fixo no código, 1=Tokens mínimos, 2=Parcial (tokens existem, usados de forma inconsistente), 3=Bom (poucos valores fixos), 4=Excelente (semântico em tudo, ambas as aparências tratadas como prioridade)

### 4. Conformidade com a plataforma (CRÍTICO)

Dê a nota com base na(s) referência(s) de plataforma carregada(s), incluindo os testes de slop delas. **Verifique**:
- **Gestos do sistema quebrados**: voltar por deslize da borda desativado (iOS), Back preditivo sequestrado (Android)
- **Violações de insets**: conteúdo sob o notch, a Dynamic Island, o indicador de início, a barra de status ou o teclado
- **Navegação fora da plataforma**: navegação global personalizada, barras de abas sobrecarregadas, padrões de iOS no Android ou vice-versa
- **Controles com cara de web**: botões estilo HTML, toggles personalizados, affordances que dependem de hover
- **Desvio de ícones**: conjuntos de ícones misturados em vez de SF Symbols / Material Symbols
- **Desvio do sistema**: atalhos repetidos ou padrões decorativos que conflitam com o produto, a plataforma ou o design system estabelecido

**Nota de 0 a 4**: 0=Port da web (nada nativo), 1=Violações pesadas (3 a 4 tipos), 2=Algumas (1 a 2 perceptíveis), 3=Em grande parte conforme (problemas sutis), 4=Totalmente nativo (um usuário fluente confia em todas as telas)

### 5. Adaptabilidade

**Verifique**:
- **Layouts de celular esticados**: tablet/iPad renderizando uma UI de celular ampliada em vez de usar size classes / window size classes
- **Quebra de orientação**: paisagem cortando, ignorada ou bloqueada sem motivo
- **Tratamento de teclado/IME**: campos escondidos atrás do teclado, sem ajuste de inset
- **Multitarefa**: Split View do iPad / multi-janela do Android quebrando o layout
- **Dobráveis**: layouts que ignoram a dobradiça ao mudar de postura (Android)

**Nota de 0 a 4**: 0=Apenas um tamanho de tela, 1=Quebras graves (paisagem ou tablet quebrados), 2=Parcial, 3=Bom (pequenos casos extremos), 4=Excelente (adapta-se a tamanhos, orientações e janelas)

## Gerar o relatório

### Pontuação de saúde da auditoria

| # | Dimensão | Nota | Achado principal |
|---|-----------|-------|-------------|
| 1 | Acessibilidade | ? | [problema mais crítico ou "--"] |
| 2 | Desempenho | ? | |
| 3 | Aparência e temas | ? | |
| 4 | Conformidade com a plataforma | ? | |
| 5 | Adaptabilidade | ? | |
| **Total** | | **??/20** | **[Faixa de classificação]** |

**Faixas de classificação**: 18-20 Excelente (pequenos refinamentos), 14-17 Bom (trate as dimensões fracas), 10-13 Aceitável (trabalho significativo necessário), 6-9 Ruim (reformulação ampla), 0-5 Crítico (problemas fundamentais)

### Veredito de conformidade com a plataforma
**Comece por aqui.** Aprovado/reprovado: isto parece um app nativo ou um site portado? Liste as violações específicas. Seja brutalmente honesto.

### Resumo executivo
- Pontuação de saúde da auditoria: **??/20** ([faixa de classificação])
- Total de problemas encontrados (contagem por severidade: P0/P1/P2/P3)
- Os 3 a 5 problemas mais críticos
- Próximos passos recomendados

### Achados detalhados por severidade

Marque cada problema com uma **severidade de P0 a P3**:
- **P0 Bloqueante**: Impede a conclusão da tarefa. Corrija imediatamente
- **P1 Grave**: Dificuldade significativa ou violação das diretrizes da plataforma. Corrija antes do lançamento
- **P2 Menor**: Incômodo, existe solução alternativa. Corrija na próxima passada
- **P3 Polish**: Bom de corrigir, sem impacto real no usuário. Corrija se houver tempo

Para cada problema, documente:
- **[P?] Nome do problema**
- **Localização**: Tela, arquivo, linha
- **Categoria**: Acessibilidade / Desempenho / Temas / Conformidade / Adaptabilidade
- **Impacto**: Como afeta os usuários
- **Diretriz**: A regra do HIG / Material que é violada (se aplicável)
- **Recomendação**: Como corrigir
- **Comando sugerido**: Qual comando usar (prefira: /impeccable adapt, /impeccable animate, /impeccable audit, /impeccable bolder, /impeccable clarify, /impeccable colorize, /impeccable critique, /impeccable delight, /impeccable distill, /impeccable document, /impeccable harden, /impeccable layout, /impeccable onboard, /impeccable optimize, /impeccable overdrive, /impeccable polish, /impeccable quieter, /impeccable shape, /impeccable typeset)

### Padrões e problemas sistêmicos

Identifique problemas recorrentes que indiquem lacunas sistêmicas, e não erros pontuais:
- "Cores fixas no código aparecem em mais de 15 telas; deveriam usar cores semânticas"
- "Alvos de toque consistentemente abaixo de 44 pt em toda a barra de abas e nas linhas das listas"

### Achados positivos

Registre o que está funcionando bem: boas práticas a manter e replicar.

## Ações recomendadas

Liste os comandos recomendados em ordem de prioridade (P0 primeiro, depois P1, depois P2):

1. **[P?] `/command-name`**: Descrição breve (contexto específico dos achados da auditoria)
2. **[P?] `/command-name`**: Descrição breve (contexto específico)

**Regras**: Recomende apenas comandos dentre: /impeccable adapt, /impeccable animate, /impeccable audit, /impeccable bolder, /impeccable clarify, /impeccable colorize, /impeccable critique, /impeccable delight, /impeccable distill, /impeccable document, /impeccable harden, /impeccable layout, /impeccable onboard, /impeccable optimize, /impeccable overdrive, /impeccable polish, /impeccable quieter, /impeccable shape, /impeccable typeset. Associe os achados ao comando mais adequado. Termine com `/impeccable polish` como etapa final se alguma correção tiver sido recomendada.

Depois de apresentar o resumo, diga ao usuário:

> Você pode me pedir para executá-los um de cada vez, todos de uma vez ou na ordem que preferir.
>
> Rode `/impeccable audit` novamente após as correções para ver sua pontuação melhorar.

**IMPORTANTE**: Seja minucioso, mas acionável. Problemas P3 demais geram ruído. Foque no que realmente importa.

**NUNCA**:
- Relate problemas sem explicar o impacto (por que isso importa?)
- Dê recomendações genéricas (seja específico e acionável)
- Omita os achados positivos (celebre o que funciona)
- Esqueça de priorizar (nem tudo pode ser P0)
- Relate falsos positivos sem verificação
