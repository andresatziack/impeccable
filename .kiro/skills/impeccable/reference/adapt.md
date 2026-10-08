> **Contexto adicional necessário**: plataformas/dispositivos-alvo e contextos de uso.

Adapte um design existente para um contexto diferente: outro tamanho de tela, dispositivo, plataforma ou caso de uso. A armadilha é tratar a adaptação como mudança de escala. O trabalho é repensar a experiência para o novo contexto.

**Apenas web** (incluindo web mobile). Plataformas nativas (`ios` / `android` / `adaptive`) seguem para [adapt.native.md](adapt.native.md); se o projeto for nativo, mude para ele agora.

---

## Avalie o desafio de adaptação

Entenda o que precisa ser adaptado e por quê:

1. **Identifique o contexto de origem**:
   - Para o que foi projetado originalmente? (Web desktop? App mobile?)
   - Que suposições foram feitas? (Tela grande? Entrada por mouse? Conexão rápida?)
   - O que funciona bem no contexto atual?

2. **Entenda o contexto-alvo**:
   - **Dispositivo**: mobile, tablet, desktop, TV, relógio, impressão?
   - **Método de entrada**: toque, mouse, teclado, voz, gamepad?
   - **Restrições de tela**: tamanho, resolução, orientação?
   - **Conexão**: wi-fi rápido, 3G lento, offline?
   - **Contexto de uso**: em movimento ou na mesa, olhada rápida ou leitura concentrada?
   - **Expectativas do usuário**: o que os usuários esperam nesta plataforma?

3. **Identifique os desafios de adaptação**:
   - O que não vai caber? (Conteúdo, navegação, funcionalidades)
   - O que não vai funcionar? (Estados de hover no toque, alvos de toque minúsculos)
   - O que é inadequado? (Padrões de desktop no mobile, padrões de mobile no desktop)

**CRÍTICO**: adaptar é repensar a experiência para o novo contexto, não escalar pixels.

## Planeje a estratégia de adaptação

Crie uma estratégia adequada ao contexto:

### Adaptação para mobile (desktop → mobile)

**Estratégia de layout**:
- Uma coluna em vez de várias colunas
- Empilhamento vertical em vez de lado a lado
- Componentes de largura total em vez de larguras fixas
- Navegação inferior em vez de navegação superior/lateral

**Estratégia de interação**:
- Alvos de toque de no mínimo 44x44px (sem depender de hover)
- Gestos de deslizar onde fizer sentido (listas, carrosséis)
- Bottom sheets em vez de dropdowns
- Design pensado primeiro para o polegar (controles ao alcance do polegar)
- Áreas de toque maiores e com mais espaçamento

**Estratégia de conteúdo**:
- Divulgação progressiva (não mostre tudo de uma vez)
- Priorize o conteúdo principal (conteúdo secundário em abas/acordeões)
- Textos mais curtos (mais concisos)
- Texto maior (mínimo de 16px)

**Estratégia de navegação**:
- Menu hambúrguer ou navegação inferior
- Reduza a complexidade da navegação
- Cabeçalhos fixos para dar contexto
- Botão de voltar no fluxo de navegação

### Adaptação para tablet (abordagem híbrida)

**Estratégia de layout**:
- Layouts de duas colunas (nem uma nem três colunas)
- Painéis laterais para conteúdo secundário
- Visualizações mestre-detalhe (lista + detalhe)
- Adaptação conforme a orientação (retrato vs. paisagem)

**Estratégia de interação**:
- Suporte tanto a toque quanto a ponteiro
- Alvos de toque de 44x44px, mas permitindo layouts mais densos que no celular
- Gavetas de navegação lateral
- Formulários de várias colunas onde fizer sentido

### Adaptação para desktop (mobile → desktop)

**Estratégia de layout**:
- Layouts de várias colunas (aproveite o espaço horizontal)
- Navegação lateral sempre visível
- Vários painéis de informação simultaneamente
- Larguras fixas com restrições de max-width (não estique até 4K)

**Estratégia de interação**:
- Estados de hover para informações adicionais
- Atalhos de teclado
- Menus de contexto no clique direito
- Arrastar e soltar onde ajudar
- Seleção múltipla com Shift/Cmd

**Estratégia de conteúdo**:
- Mostre mais informações de imediato (menos divulgação progressiva)
- Tabelas de dados com muitas colunas
- Visualizações mais ricas
- Descrições mais detalhadas

### Adaptação para impressão (tela → impressão)

**Estratégia de layout**:
- Quebras de página em pontos lógicos
- Remova navegação, rodapé e elementos interativos
- Preto e branco (ou cor limitada)
- Margens adequadas para encadernação

**Estratégia de conteúdo**:
- Expanda o conteúdo abreviado (mostre URLs completas, seções ocultas)
- Adicione números de página, cabeçalhos e rodapés
- Inclua metadados (data de impressão, título da página)
- Converta gráficos em versões adequadas para impressão

### Adaptação para e-mail (web → e-mail)

**Estratégia de layout**:
- Largura estreita (máximo de 600px)
- Somente uma coluna
- CSS inline (sem folhas de estilo externas)
- Layouts baseados em tabelas (para compatibilidade com clientes de e-mail)

**Estratégia de interação**:
- CTAs grandes e óbvios (botões, não links de texto)
- Sem estados de hover (não são confiáveis)
- Deep links para o web app em interações complexas

## Implemente as adaptações

Aplique as mudanças de forma sistemática:

### Breakpoints responsivos

Escolha breakpoints adequados:
- Mobile: 320px-767px
- Tablet: 768px-1023px
- Desktop: 1024px+
- Ou breakpoints guiados pelo conteúdo (onde o design quebra)

### Técnicas de adaptação de layout

- **CSS Grid/Flexbox**: refluxo automático dos layouts
- **Container Queries**: adapte com base no contêiner, não na viewport
- **`clamp()`**: dimensionamento fluido entre mínimo e máximo
- **Media queries**: estilos diferentes para contextos diferentes
- **Propriedades de display**: mostre/oculte elementos conforme o contexto

### Adaptação para toque

- Aumente o tamanho dos alvos de toque (mínimo de 44x44px)
- Adicione mais espaçamento entre elementos interativos
- Remova interações que dependem de hover
- Adicione feedback de toque (ripples, destaques)
- Considere as zonas do polegar (é mais fácil alcançar a parte de baixo do que a de cima)

### Adaptação de conteúdo

- Use `display: none` com moderação (o conteúdo ainda é baixado)
- Aprimoramento progressivo (conteúdo essencial primeiro, aprimoramentos em telas maiores)
- Lazy loading para conteúdo fora da tela
- Imagens responsivas (`srcset`, elemento `picture`)

### Adaptação de navegação

- Transforme navegação complexa em hambúrguer/gaveta no mobile
- Barra de navegação inferior para apps mobile
- Navegação lateral persistente no desktop
- Breadcrumbs em telas menores para dar contexto

**IMPORTANTE**: teste em dispositivos reais. A emulação de dispositivos no DevTools ajuda, mas não é perfeita.

**NUNCA**:
- Esconda funcionalidades essenciais no mobile (se importa, faça funcionar)
- Presuma que desktop = dispositivo potente (considere acessibilidade, máquinas mais antigas)
- Use arquiteturas de informação diferentes entre contextos (confunde)
- Quebre as expectativas do usuário para a plataforma (usuários de mobile esperam padrões de mobile)
- Esqueça a orientação paisagem no mobile/tablet
- Use breakpoints genéricos às cegas (use breakpoints guiados pelo conteúdo)
- Ignore o toque no desktop (muitos dispositivos desktop têm tela sensível ao toque)

## Verifique as adaptações

Teste a fundo em todos os contextos:

- **Dispositivos reais**: teste em celulares, tablets e desktops de verdade
- **Orientações diferentes**: retrato e paisagem
- **Navegadores diferentes**: Safari, Chrome, Firefox, Edge
- **Sistemas operacionais diferentes**: iOS, Android, Windows, macOS
- **Métodos de entrada diferentes**: toque, mouse, teclado
- **Casos extremos**: telas muito pequenas (320px), telas muito grandes (4K)
- **Conexões lentas**: teste com a rede limitada

**Controles personalizados** (sliders, superfícies de arrastar, faixas de controles roláveis): um slider de antes/depois pode passar em todas as verificações de largura acima e ainda assim se recusar a arrastar no iOS, então exercite cada um que estiver no escopo na mesma rodada em lote das verificações acima:

- **Gesto principal**: toque no controle e confirme que ele responde como projetado; depois arraste-o com o método de entrada-alvo; o arrasto precisa ser concluído, não apenas começar
- **Rolagem por cima dele**: um deslizar ao longo do eixo de rolagem da página por cima do controle rola a página ou o contêiner sem ativá-lo; um arrasto que começa no controle, ao longo do eixo dele, move o controle, não a página. Nenhuma das duas falhas gera erro, então teste ambas
- **Evidência**: diga o que produziu a evidência: uma viewport emulada, entrada de toque sintetizada por uma ferramenta de navegador, qual engine a executou (Chromium não é Safari) ou um dispositivo físico. Capturas de tela e viewports redimensionadas verificam o layout, nunca um gesto. Nomeie o que ficou sem teste e siga em frente; hardware inacessível é uma lacuna relatada, não um bloqueio

Quando a adaptação parecer nativa em cada contexto, passe para `/impeccable polish` para a passada final.

---

## Material de referência

As seções abaixo eram antes `responsive-design.md` e agora ficam inline, para que o fluxo de adapt tenha sua referência aprofundada de responsividade em um só lugar.

### Design responsivo

#### Mobile-first: escreva do jeito certo

Comece com estilos base para mobile e use queries `min-width` para adicionar complexidade em camadas. Desktop-first (`max-width`) faz o mobile carregar estilos desnecessários primeiro.

#### Breakpoints: guiados pelo conteúdo

Não persiga tamanhos de dispositivos; deixe o conteúdo dizer onde quebrar. Comece estreito, estique até o design quebrar e adicione um breakpoint ali. Três breakpoints costumam bastar (640, 768, 1024px). Use `clamp()` para valores fluidos sem breakpoints.

#### Detecte o método de entrada, não só o tamanho da tela

**O tamanho da tela não diz qual é o método de entrada.** Um notebook com tela sensível ao toque, um tablet com teclado. Use as queries de pointer e hover:

```css
/* Fine pointer (mouse, trackpad) */
@media (pointer: fine) {
  .button { padding: 8px 16px; }
}

/* Coarse pointer (touch, stylus) */
@media (pointer: coarse) {
  .button { padding: 12px 20px; }  /* Larger touch target */
}

/* Device supports hover */
@media (hover: hover) {
  .card:hover { transform: translateY(-2px); }
}

/* Device doesn't support hover (touch) */
@media (hover: none) {
  .card { /* No hover state - use active instead */ }
}
```

**Crítico**: não dependa de hover para funcionalidades. Usuários de toque não conseguem passar o mouse por cima.

#### Áreas seguras: lide com o notch

Celulares modernos têm notches, cantos arredondados e indicadores de início. Use `env()`:

```css
body {
  padding-top: env(safe-area-inset-top);
  padding-bottom: env(safe-area-inset-bottom);
  padding-left: env(safe-area-inset-left);
  padding-right: env(safe-area-inset-right);
}

/* With fallback */
.footer {
  padding-bottom: max(1rem, env(safe-area-inset-bottom));
}
```

**Ative o viewport-fit** na sua meta tag:
```html
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
```

#### Imagens responsivas: faça do jeito certo

##### srcset com descritores de largura

```html
<img
  src="hero-800.jpg"
  srcset="
    hero-400.jpg 400w,
    hero-800.jpg 800w,
    hero-1200.jpg 1200w
  "
  sizes="(max-width: 768px) 100vw, 50vw"
  alt="Hero image"
>
```

**Como funciona**:
- `srcset` lista as imagens disponíveis com suas larguras reais (descritores `w`)
- `sizes` informa ao navegador com que largura a imagem será exibida
- O navegador escolhe o melhor arquivo com base na largura da viewport E na densidade de pixels do dispositivo

##### Elemento picture para direção de arte

Quando você precisa de recortes/composições diferentes (não apenas resoluções):

```html
<picture>
  <source media="(min-width: 768px)" srcset="wide.jpg">
  <source media="(max-width: 767px)" srcset="tall.jpg">
  <img src="fallback.jpg" alt="...">
</picture>
```

#### Padrões de adaptação de layout

**Navegação**: três estágios: hambúrguer + gaveta no mobile, horizontal compacta no tablet, completa com rótulos no desktop. **Tabelas**: transforme em cards no mobile usando `display: block` e atributos `data-label`. **Divulgação progressiva**: use `<details>/<summary>` para conteúdo que pode ser recolhido no mobile.

#### Testes: não confie só no DevTools

A emulação de dispositivos do DevTools é útil para o layout, mas deixa escapar:

- Interações reais de toque
- Restrições reais de CPU/memória
- Padrões de latência de rede
- Diferenças de renderização de fontes
- O aparecimento da interface do navegador e do teclado

**Teste no mínimo em**: um iPhone real, um Android real e um tablet, se relevante. Celulares Android baratos revelam problemas de desempenho que você nunca verá em simuladores.

---

**Evite**: design desktop-first. Detecção de dispositivo em vez de detecção de recursos. Bases de código separadas para mobile/desktop. Ignorar tablet e paisagem. Presumir que todos os dispositivos mobile são potentes.
