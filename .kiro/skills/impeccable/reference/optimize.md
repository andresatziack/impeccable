Desempenho é um recurso. Identifique o gargalo real DESTA interface, corrija-o e depois meça. Não otimize o que não está lento.

## Avaliar problemas de desempenho

Entenda o desempenho atual e identifique os problemas:

1. **Meça o estado atual**:
   - **Core Web Vitals**: pontuações de LCP, INP e CLS
   - **Tempo de carregamento**: Tempo até a interatividade, first contentful paint
   - **Tamanho do bundle**: Tamanhos de JavaScript, CSS e imagens
   - **Desempenho em tempo de execução**: Taxa de quadros, uso de memória, uso de CPU
   - **Rede**: Quantidade de requisições, tamanhos de payload, waterfall

2. **Identifique os gargalos**:
   - O que está lento? (Carregamento inicial? Interações? Animações?)
   - O que está causando isso? (Imagens grandes? JavaScript custoso? Layout thrashing?)
   - Quão grave é? (Perceptível? Irritante? Bloqueante?)
   - Quem é afetado? (Todos os usuários? Só mobile? Conexões lentas?)

**CRÍTICO**: Meça antes e depois. Otimização prematura desperdiça tempo. Otimize o que realmente importa.

## Estratégia de otimização

Crie um plano de melhoria sistemático:

### Desempenho de carregamento

**Otimize as imagens**:
- Use formatos modernos (WebP, AVIF)
- Dimensionamento adequado (não carregue uma imagem de 3000px para exibir em 300px)
- Lazy loading para imagens abaixo da dobra
- Imagens responsivas (`srcset`, elemento `picture`)
- Comprima as imagens (qualidade de 80-85% costuma ser imperceptível)
- Use CDN para uma entrega mais rápida

```html
<img 
  src="hero.webp"
  srcset="hero-400.webp 400w, hero-800.webp 800w, hero-1200.webp 1200w"
  sizes="(max-width: 400px) 400px, (max-width: 800px) 800px, 1200px"
  loading="lazy"
  alt="Hero image"
/>
```

**Reduza o bundle de JavaScript**:
- Code splitting (por rota, por componente)
- Tree shaking (remova código não utilizado)
- Remova dependências não utilizadas
- Faça lazy loading de código não crítico
- Use imports dinâmicos para componentes grandes

```javascript
// Lazy load heavy component
const HeavyChart = lazy(() => import('./HeavyChart'));
```

**Otimize o CSS**:
- Remova CSS não utilizado
- CSS crítico inline, o restante assíncrono
- Minimize os arquivos CSS
- Use CSS containment para regiões independentes

**Otimize as fontes**:
- Use `font-display: swap` ou `optional`
- Faça subset das fontes (apenas os caracteres de que você precisa)
- Faça preload das fontes críticas
- Use fontes do sistema quando apropriado
- Limite os pesos de fonte carregados

```css
@font-face {
  font-family: 'CustomFont';
  src: url('/fonts/custom.woff2') format('woff2');
  font-display: swap; /* Show fallback immediately */
  unicode-range: U+0020-007F; /* Basic Latin only */
}
```

**Otimize a estratégia de carregamento**:
- Recursos críticos primeiro (async/defer para os não críticos)
- Faça preload dos assets críticos
- Faça prefetch das próximas páginas prováveis
- Service worker para offline/cache
- HTTP/2 ou HTTP/3 para multiplexação

### Desempenho de renderização

**Evite layout thrashing**:
```javascript
// ❌ Bad: Alternating reads and writes (causes reflows)
elements.forEach(el => {
  const height = el.offsetHeight; // Read (forces layout)
  el.style.height = height * 2; // Write
});

// ✅ Good: Batch reads, then batch writes
const heights = elements.map(el => el.offsetHeight); // All reads
elements.forEach((el, i) => {
  el.style.height = heights[i] * 2; // All writes
});
```

**Otimize a renderização**:
- Use a propriedade CSS `contain` para regiões independentes
- Minimize a profundidade do DOM (mais plano é mais rápido)
- Reduza o tamanho do DOM (menos elementos)
- Use `content-visibility: auto` para listas longas
- Rolagem virtual para listas muito longas (react-window, TanStack Virtual)

**Reduza paint e composição**:
- Use `transform` e `opacity` para movimento confiável, mas permita blur, filtros, máscaras, clip paths, sombras e mudanças de cor quando criarem um refinamento significativo
- Evite animar casualmente propriedades que determinam o layout (`width`, `height`, `top`, `left`, margens)
- Use `will-change` com moderação, para operações sabidamente custosas
- Limite as áreas de paint custosas para efeitos de blur/filtro/sombra (menor e isolado é mais rápido)

### Desempenho de animação

**Aceleração por GPU**:
```css
/* ✅ GPU-accelerated (fast) */
.animated {
  transform: translateX(100px);
  opacity: 0.5;
}

/* ❌ CPU-bound (slow) */
.animated {
  left: 100px;
  width: 300px;
}
```

**60fps fluidos**:
- Mire em 16ms por quadro (60fps)
- Use `requestAnimationFrame` para animações em JS
- Aplique debounce/throttle aos handlers de rolagem
- Use animações CSS quando possível
- Evite JavaScript de longa duração durante animações

**Intersection Observer**:
```javascript
// Efficiently detect when elements enter viewport
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      // Element is visible, lazy load or animate
    }
  });
});
```

### Otimização de React/frameworks

**Específico de React**:
- Use `memo()` para componentes custosos
- `useMemo()` e `useCallback()` para cálculos custosos
- Virtualize listas longas
- Faça code splitting das rotas
- Evite criar funções inline no render
- Use o React DevTools Profiler

**Independente de framework**:
- Minimize as re-renderizações
- Aplique debounce a operações custosas
- Memoize valores calculados
- Faça lazy loading de rotas e componentes

### Otimização de rede

**Reduza as requisições**:
- Combine arquivos pequenos
- Use sprites SVG para ícones
- Coloque inline os assets críticos pequenos
- Remova scripts de terceiros não utilizados

**Otimize as APIs**:
- Use paginação (não carregue tudo)
- GraphQL para solicitar apenas os campos necessários
- Compressão de resposta (gzip, brotli)
- Cabeçalhos de cache HTTP
- CDN para assets estáticos

**Otimize para conexões lentas**:
- Carregamento adaptativo com base na conexão (navigator.connection)
- Atualizações otimistas de UI
- Priorização de requisições
- Aprimoramento progressivo

## Otimização dos Core Web Vitals

### Largest Contentful Paint (LCP < 2.5s)
- Otimize as imagens da seção hero
- Coloque inline o CSS crítico
- Faça preload dos recursos principais
- Use CDN
- Renderização no servidor

### Interaction to Next Paint (INP < 200ms)
- Divida tarefas longas
- Adie o JavaScript não crítico
- Use web workers para processamento pesado
- Reduza o tempo de execução de JavaScript

### Cumulative Layout Shift (CLS < 0.1)
- Defina dimensões em imagens e vídeos
- Não injete conteúdo acima do conteúdo existente
- Use a propriedade CSS `aspect-ratio`
- Reserve espaço para anúncios/embeds
- Evite animações que causem deslocamentos de layout

```css
/* Reserve space for image */
.image-container {
  aspect-ratio: 16 / 9;
}
```

## Monitoramento de desempenho

**Ferramentas a usar**:
- Chrome DevTools (Lighthouse, painel Performance)
- WebPageTest
- Core Web Vitals (Chrome UX Report)
- Analisadores de bundle (webpack-bundle-analyzer)
- Monitoramento de desempenho (Sentry, DataDog, New Relic)

**Métricas principais**:
- LCP, INP, CLS (Core Web Vitals; o INP substituiu o FID em março de 2024)
- Time to Interactive (TTI)
- First Contentful Paint (FCP)
- Total Blocking Time (TBT)
- Tamanho do bundle
- Quantidade de requisições

**IMPORTANTE**: Meça em dispositivos reais com condições de rede reais. O Chrome no desktop com conexão rápida não é representativo.

**NUNCA**:
- Otimize sem medir (otimização prematura)
- Sacrifique a acessibilidade pelo desempenho
- Quebre funcionalidades ao otimizar
- Use `will-change` em todo lugar (cria novas camadas, consome memória)
- Faça lazy loading de conteúdo acima da dobra
- Faça micro-otimizações ignorando os problemas principais (otimize primeiro o maior gargalo)
- Esqueça o desempenho em mobile (dispositivos muitas vezes mais lentos, conexões mais lentas)

## Verificar as melhorias

Teste se as otimizações funcionaram:

- **Métricas de antes/depois**: Compare as pontuações do Lighthouse
- **Monitoramento de usuários reais**: Acompanhe as melhorias para usuários reais
- **Dispositivos diferentes**: Teste em Android de entrada, não apenas no iPhone topo de linha
- **Conexões lentas**: Limite para 3G e teste a experiência
- **Sem regressões**: Garanta que as funcionalidades continuam funcionando
- **Percepção do usuário**: Ele *parece* mais rápido?

Quando os números percebidos pelo usuário melhorarem, passe para `/impeccable polish` para a etapa final.
