Designs que só funcionam com dados perfeitos não estão prontos para produção. Torne a interface robusta contra as entradas, os erros, os idiomas e as condições de rede que os usuários reais vão impor a ela.

## Avalie as necessidades de robustez

Identifique pontos fracos e casos extremos:

1. **Teste com entradas extremas**:
   - Texto muito longo (nomes, descrições, títulos)
   - Texto muito curto (vazio, um único caractere)
   - Caracteres especiais (emoji, texto RTL, acentos)
   - Números grandes (milhões, bilhões)
   - Muitos itens (mais de 1000 itens de lista, mais de 50 opções)
   - Sem dados (estados vazios)

2. **Teste cenários de erro**:
   - Falhas de rede (offline, lenta, timeout)
   - Erros de API (400, 401, 403, 404, 500)
   - Erros de validação
   - Erros de permissão
   - Limitação de taxa (rate limiting)
   - Operações concorrentes

3. **Teste a internacionalização**:
   - Traduções longas (o alemão costuma ser 30% mais longo que o inglês)
   - Idiomas RTL (árabe, hebraico)
   - Conjuntos de caracteres (chinês, japonês, coreano, emoji)
   - Formatos de data/hora
   - Formatos de número (1,000 vs 1.000)
   - Símbolos de moeda

**CRÍTICO**: designs que só funcionam com dados perfeitos não estão prontos para produção. Torne-os robustos contra a realidade.

## Dimensões de robustez

Melhore a resiliência de forma sistemática:

### Estouro e quebra de texto

**Tratamento de texto longo**:
```css
/* Single line with ellipsis */
.truncate {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

/* Multi-line with clamp */
.line-clamp {
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

/* Allow wrapping */
.wrap {
  word-wrap: break-word;
  overflow-wrap: break-word;
  hyphens: auto;
}
```

**Estouro em flex/grid**:
```css
/* Prevent flex items from overflowing */
.flex-item {
  min-width: 0; /* Allow shrinking below content size */
  overflow: hidden;
}

/* Prevent grid items from overflowing */
.grid-item {
  min-width: 0;
  min-height: 0;
}
```

**Tamanho de texto responsivo**:
- Use `clamp()` para tipografia fluida
- Defina tamanhos mínimos legíveis (16px no corpo de texto no mobile, o mesmo piso que as orientações de tipografia definem; 14px apenas para texto genuinamente secundário. O Safari do iOS força zoom em inputs focados com menos de 16px, o que quebra layouts de formulário)
- Teste o redimensionamento de texto (zoom de 200%)
- Garanta que os contêineres se expandam com o texto

### Internacionalização (i18n)

**Expansão de texto**:
- Reserve 30-40% de espaço extra para traduções
- Use flexbox/grid que se adapte ao conteúdo
- Teste com o idioma mais longo (geralmente o alemão)
- Evite larguras fixas em contêineres de texto

```jsx
// ❌ Bad: Assumes short English text
<button className="w-24">Submit</button>

// ✅ Good: Adapts to content
<button className="px-4 py-2">Submit</button>
```

**Suporte a RTL (da direita para a esquerda)**:
```css
/* Use logical properties */
margin-inline-start: 1rem; /* Not margin-left */
padding-inline: 1rem; /* Not padding-left/right */
border-inline-end: 1px solid; /* Not border-right */

/* Or use dir attribute */
[dir="rtl"] .arrow { transform: scaleX(-1); }
```

**Suporte a conjuntos de caracteres**:
- Use codificação UTF-8 em todo lugar
- Teste com caracteres chineses/japoneses/coreanos (CJK)
- Teste com emoji (eles podem ter de 2 a 4 bytes)
- Lide com diferentes sistemas de escrita (latino, cirílico, árabe etc.)

**Formatação de data/hora**:
```javascript
// ✅ Use Intl API for proper formatting
new Intl.DateTimeFormat('en-US').format(date); // 1/15/2024
new Intl.DateTimeFormat('de-DE').format(date); // 15.1.2024

new Intl.NumberFormat('en-US', { 
  style: 'currency', 
  currency: 'USD' 
}).format(1234.56); // $1,234.56
```

**Pluralização**:
```javascript
// ❌ Bad: Assumes English pluralization
`${count} item${count !== 1 ? 's' : ''}`

// ✅ Good: Use proper i18n library
t('items', { count }) // Handles complex plural rules
```

### Tratamento de erros

**Erros de rede**:
- Mostre mensagens de erro claras
- Ofereça um botão de tentar novamente
- Explique o que aconteceu
- Ofereça modo offline (se aplicável)
- Trate cenários de timeout

```jsx
// Error states with recovery
{error && (
  <ErrorMessage>
    <p>Failed to load data. {error.message}</p>
    <button onClick={retry}>Try again</button>
  </ErrorMessage>
)}
```

**Erros de validação de formulário**:
- Erros inline perto dos campos
- Mensagens claras e específicas
- Sugira correções
- Não bloqueie o envio sem necessidade
- Preserve a entrada do usuário em caso de erro

**Erros de API**:
- Trate cada código de status adequadamente
  - 400: mostre os erros de validação
  - 401: redirecione para o login
  - 403: mostre um erro de permissão
  - 404: mostre um estado de não encontrado
  - 429: mostre uma mensagem de limite de taxa
  - 500: mostre um erro genérico e ofereça suporte

**Degradação graciosa**:
- A funcionalidade essencial funciona sem JavaScript
- Imagens têm texto alternativo
- Aprimoramento progressivo
- Fallbacks para recursos não suportados

### Casos extremos e condições de limite

**Estados vazios**:
- Nenhum item na lista
- Nenhum resultado de busca
- Nenhuma notificação
- Nenhum dado para exibir
- Ofereça uma próxima ação clara

**Estados de carregamento**:
- Carregamento inicial
- Carregamento de paginação
- Atualização
- Mostre o que está carregando ("Carregando seus projetos...")
- Estimativas de tempo para operações longas

**Grandes conjuntos de dados**:
- Paginação ou virtual scrolling
- Recursos de busca/filtro
- Otimização de desempenho
- Não carregue todos os 10.000 itens de uma vez

**Operações concorrentes**:
- Evite envio duplicado (desative o botão durante o carregamento)
- Trate condições de corrida
- Atualizações otimistas com rollback
- Resolução de conflitos

**Gestos interrompidos** (sliders personalizados, superfícies de arrastar, faixas de controles roláveis):
- Um segundo dedo ou ponteiro toca no meio do arrasto: o primeiro arrasto mantém seu ponteiro ou termina de forma limpa, nunca salta para o novo
- O navegador cancela o gesto para rolar (`pointercancel`), a captura é perdida (`lostpointercapture`), o ponteiro é solto fora do controle ou a janela perde o foco (`blur`) no meio do arrasto: limpe o estado de arrasto e libere a captura
- Depois de cada um desses casos, o próximo toque ou arrasto funciona sem recarregar a página

**Estados de permissão**:
- Sem permissão para visualizar
- Sem permissão para editar
- Modo somente leitura
- Explicação clara do motivo

**Compatibilidade de navegadores**:
- Polyfills para recursos modernos
- Fallbacks para CSS não suportado
- Detecção de recursos (não detecção de navegador)
- Teste nos navegadores-alvo

### Validação e sanitização de entradas

**Validação no cliente**:
- Campos obrigatórios
- Validação de formato (e-mail, telefone, URL)
- Limites de tamanho
- Correspondência de padrões
- Regras de validação personalizadas

**Validação no servidor** (sempre):
- Nunca confie apenas no lado do cliente
- Valide e sanitize todas as entradas
- Proteja contra ataques de injeção
- Limitação de taxa

**Tratamento de restrições**:
```html
<!-- Set clear constraints -->
<input 
  type="text"
  maxlength="100"
  pattern="[A-Za-z0-9]+"
  required
  aria-describedby="username-hint"
/>
<small id="username-hint">
  Letters and numbers only, up to 100 characters
</small>
```

### Resiliência de acessibilidade

**Navegação por teclado**:
- Toda a funcionalidade acessível pelo teclado
- Ordem de tabulação lógica
- Gerenciamento de foco em modais
- Links de pular para conteúdos longos

**Suporte a leitores de tela**:
- Rótulos ARIA adequados
- Anuncie mudanças dinâmicas (live regions)
- Texto alternativo descritivo
- HTML semântico

**Modo de alto contraste**:
- Teste no modo de alto contraste do Windows
- Não dependa apenas da cor
- Forneça pistas visuais alternativas

### Resiliência de desempenho

**Conexões lentas**:
- Carregamento progressivo de imagens
- Skeleton screens
- Atualizações otimistas da UI
- Suporte offline (service workers)

**Vazamentos de memória**:
- Remova event listeners
- Cancele assinaturas
- Limpe timers/intervals
- Aborte requisições pendentes ao desmontar

**Throttling e debouncing**:
```javascript
// Debounce search input
const debouncedSearch = debounce(handleSearch, 300);

// Throttle scroll handler
const throttledScroll = throttle(handleScroll, 100);
```

## Estratégias de teste

**Testes manuais**:
- Teste com dados extremos (muito longos, muito curtos, vazios)
- Teste em idiomas diferentes
- Teste offline
- Teste com conexão lenta (limite para 3G)
- Teste com leitor de tela
- Teste a navegação só por teclado
- Teste em navegadores antigos

**Testes automatizados**:
- Testes unitários para casos extremos
- Testes de integração para cenários de erro
- Testes E2E para caminhos críticos
- Uma regressão comportamental para cada correção de gesto confirmada, quando o executor de testes do projeto conseguir controlar a entrada
- Testes de regressão visual
- Testes de acessibilidade (axe, WAVE)

**IMPORTANTE**: robustez é esperar o inesperado. Usuários reais farão coisas que você nunca imaginou.

**NUNCA**:
- Presuma entrada perfeita (valide tudo)
- Ignore a internacionalização (projete para o mundo)
- Deixe mensagens de erro genéricas ("Ocorreu um erro")
- Esqueça os cenários offline
- Confie apenas na validação no cliente
- Use larguras fixas para texto
- Presuma texto com o comprimento do inglês
- Bloqueie a interface inteira quando um componente falhar

## Verifique a robustez

Teste a fundo com casos extremos:

- **Texto longo**: experimente nomes com mais de 100 caracteres
- **Emoji**: use emoji em todos os campos de texto
- **RTL**: teste com árabe ou hebraico
- **CJK**: teste com chinês/japonês/coreano
- **Problemas de rede**: desligue a internet, limite a conexão
- **Grandes conjuntos de dados**: teste com mais de 1000 itens
- **Ações concorrentes**: clique em enviar 10 vezes rapidamente
- **Gestos interrompidos**: adicione um segundo dedo no meio do arrasto, role por cima do controle, solte fora dele, troque de janela no meio do arrasto; depois arraste de novo
- **Erros**: force erros de API, teste todos os estados de erro
- **Vazio**: remova todos os dados, teste os estados vazios

Para gestos, diga o que produziu a evidência (viewport emulada, toque sintetizado, qual engine, dispositivo físico) e nomeie o que ficou sem teste.

Quando os casos extremos estiverem cobertos, passe para `/impeccable polish` para a passada final.
