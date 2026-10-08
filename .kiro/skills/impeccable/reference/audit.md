Execute verificações **técnicas** sistemáticas de qualidade e gere um relatório abrangente. Não corrija os problemas; documente-os para que outros comandos os resolvam.

Esta é uma auditoria no nível do código, não uma crítica de design. Verifique o que é mensurável e verificável na implementação.

**Somente web.** Plataformas nativas (`ios` / `android` / `adaptive`) são direcionadas para [audit.native.md](audit.native.md); se o projeto for nativo, mude para ele agora.

## Varredura diagnóstica

Execute verificações abrangentes em 5 dimensões. Dê a cada dimensão uma nota de 0 a 4 usando os critérios abaixo.

### 1. Acessibilidade (A11y)

**Verifique**:
- **Problemas de contraste**: Taxas de contraste de texto < 4.5:1 (ou 7:1 para AAA)
- **Sensibilidade a movimento**: `prefers-reduced-motion` precisa de uma alternativa intencional que preserve a mudança de estado e a hierarquia; aponte um corte global de `0.01ms` que destrói feedback útil, piscadas acima do limite e movimento que bloqueia o foco, a leitura ou a conclusão de tarefas
- **ARIA ausente**: Elementos interativos sem papéis, rótulos ou estados adequados
- **Navegação por teclado**: Indicadores de foco ausentes, ordem de tabulação ilógica, armadilhas de teclado
- **HTML semântico**: Hierarquia de títulos incorreta, landmarks ausentes, divs no lugar de botões
- **Texto alternativo**: Descrições de imagem ausentes ou ruins
- **Problemas em formulários**: Campos sem rótulos, mensagens de erro ruins, indicadores de obrigatoriedade ausentes

**Nota de 0 a 4**: 0=Inacessível (falha no WCAG A), 1=Lacunas graves (poucos rótulos ARIA, sem navegação por teclado), 2=Parcial (algum esforço de a11y, lacunas significativas), 3=Bom (WCAG AA atendido em grande parte, lacunas pequenas), 4=Excelente (WCAG AA totalmente atendido, próximo do AAA)

### 2. Desempenho

**Verifique**:
- **Layout thrashing**: Leitura/escrita de propriedades de layout em loops
- **Animações custosas**: Animação casual de propriedades de layout, efeitos de blur/filtro/sombra sem limite ou efeitos que perdem quadros visivelmente
- **Otimização ausente**: Imagens sem lazy loading, assets não otimizados
- **Uso excessivo de will-change**: `will-change` aplicado de forma ampla ou deixado ativo em repouso (é uma dica direcionada para animações sabidamente custosas, não um requisito básico)
- **Tamanho do bundle**: Imports desnecessários, dependências não utilizadas
- **Desempenho de renderização**: Re-renderizações desnecessárias, memoização ausente

**Nota de 0 a 4**: 0=Problemas graves (layout thrash, nada otimizado), 1=Problemas sérios (sem lazy loading, animações custosas), 2=Parcial (alguma otimização, lacunas restantes), 3=Bom (em grande parte otimizado, pequenas melhorias possíveis), 4=Excelente (rápido, enxuto, bem otimizado)

### 3. Temas

**Verifique**:
- **Cores fixas no código**: Cores que não usam tokens de design
- **Modo escuro quebrado**: Variantes de modo escuro ausentes, contraste ruim no tema escuro
- **Tokens inconsistentes**: Uso de tokens errados, mistura de tipos de token
- **Problemas na troca de tema**: Valores que não se atualizam na troca de tema

**Nota de 0 a 4**: 0=Sem temas (tudo fixo no código), 1=Tokens mínimos (quase tudo fixo no código), 2=Parcial (tokens existem, mas são usados de forma inconsistente), 3=Bom (tokens usados, poucos valores fixos), 4=Excelente (sistema de tokens completo, modo escuro funciona perfeitamente)

### 4. Design responsivo

**Verifique**:
- **Larguras fixas**: Larguras fixas no código que quebram no mobile
- **Alvos de toque**: Elementos interativos < 44x44px
- **Interação por toque quebrada**: Sliders personalizados, superfícies de arrastar e faixas de controles roláveis cujo gesto principal falha com toque, que engolem a rolagem da página ou perdem o arrasto para ela, ou que ficam travados após um gesto interrompido. Sinais no código: handlers só de mouse, ausência de `touch-action` em uma superfície de arrastar baseada em pointer events, estado de arrasto que nada limpa no cancelamento, perda de captura ou blur. Exercite o gesto quando uma ferramenta de navegador conseguir sintetizar toque (uma viewport renderizada prova o layout, não o gesto) e depois diga o que produziu a evidência (viewport emulada, toque sintetizado, qual engine, dispositivo físico) e o que ficou sem teste
- **Rolagem horizontal**: Conteúdo transbordando em viewports estreitas
- **Escala de texto**: Layouts que quebram quando o tamanho do texto aumenta
- **Breakpoints ausentes**: Sem variantes para mobile/tablet

**Nota de 0 a 4**: 0=Somente desktop (quebra no mobile), 1=Problemas graves (alguns breakpoints, muitas falhas), 2=Parcial (funciona no mobile, com arestas), 3=Bom (responsivo, pequenos problemas de alvo de toque ou overflow), 4=Excelente (fluido, todas as viewports, alvos de toque adequados, gestos funcionam com toque)

### 5. Integridade da implementação (CRÍTICO)

Rode o detector incluído e verifique cada achado no contexto. Procure atalhos de implementação repetidos, desvios do design system, conteúdo enganoso ou decorativo e estrutura intercambiável com um produto não relacionado. Mantenha os achados determinísticos separados do julgamento visual e aponte os falsos positivos.

**Nota de 0 a 4**: 0=desvio sistêmico, 1=falhas graves repetidas, 2=vários problemas verificados, 3=pequenos problemas isolados, 4=coerente e intencional

## Gerar o relatório

### Pontuação de saúde da auditoria

| # | Dimensão | Nota | Achado principal |
|---|-----------|-------|-------------|
| 1 | Acessibilidade | ? | [problema de a11y mais crítico ou "--"] |
| 2 | Desempenho | ? | |
| 3 | Temas | ? | |
| 4 | Design responsivo | ? | |
| 5 | Integridade da implementação | ? | |
| **Total** | | **??/20** | **[Faixa de classificação]** |

**Faixas de classificação**: 18-20 Excelente (pequenos refinamentos), 14-17 Bom (trate as dimensões fracas), 10-13 Aceitável (trabalho significativo necessário), 6-9 Ruim (reformulação ampla), 0-5 Crítico (problemas fundamentais)

### Veredito de integridade da implementação
**Comece por aqui.** Aprovado/reprovado: a implementação expressa um sistema coerente e específico do produto? Cite evidências verificadas e achados do detector.

### Resumo executivo
- Pontuação de saúde da auditoria: **??/20** ([faixa de classificação])
- Total de problemas encontrados (contagem por severidade: P0/P1/P2/P3)
- Os 3 a 5 problemas mais críticos
- Próximos passos recomendados

### Achados detalhados por severidade

Marque cada problema com uma **severidade de P0 a P3**:
- **P0 Bloqueante**: Impede a conclusão da tarefa. Corrija imediatamente
- **P1 Grave**: Dificuldade significativa ou violação do WCAG AA. Corrija antes do lançamento
- **P2 Menor**: Incômodo, existe solução alternativa. Corrija na próxima passada
- **P3 Polish**: Bom de corrigir, sem impacto real no usuário. Corrija se houver tempo

Para cada problema, documente:
- **[P?] Nome do problema**
- **Localização**: Componente, arquivo, linha
- **Categoria**: Acessibilidade / Desempenho / Temas / Responsivo / Integridade da implementação
- **Impacto**: Como afeta os usuários
- **WCAG/Padrão**: Qual padrão é violado (se aplicável)
- **Recomendação**: Como corrigir
- **Comando sugerido**: Qual comando usar (prefira: /impeccable adapt, /impeccable animate, /impeccable audit, /impeccable bolder, /impeccable clarify, /impeccable colorize, /impeccable critique, /impeccable delight, /impeccable distill, /impeccable document, /impeccable harden, /impeccable layout, /impeccable onboard, /impeccable optimize, /impeccable overdrive, /impeccable polish, /impeccable quieter, /impeccable shape, /impeccable typeset)

### Padrões e problemas sistêmicos

Identifique problemas recorrentes que indiquem lacunas sistêmicas, e não erros pontuais:
- "Cores fixas no código aparecem em mais de 15 componentes; deveriam usar tokens de design"
- "Alvos de toque consistentemente pequenos demais (<44px) em toda a experiência mobile"

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
