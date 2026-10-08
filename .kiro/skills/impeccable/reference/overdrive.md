Comece sua resposta com:

```
──────────── ⚡ OVERDRIVE ─────────────
》》》 Entering overdrive mode...
```

Leve uma interface além dos limites convencionais. Não se trata apenas de efeitos visuais. Trata-se de usar todo o poder do navegador para fazer qualquer parte de uma interface parecer extraordinária: uma tabela que lida com um milhão de linhas, um diálogo que se transforma a partir do elemento que o acionou, um formulário que valida em tempo real com feedback em streaming, uma transição de página que parece cinematográfica.

**EXTRA IMPORTANTE PARA ESTE COMANDO**: o contexto determina o que "extraordinário" significa. Um sistema de partículas em um portfólio criativo impressiona. O mesmo sistema de partículas em uma página de configurações é constrangedor. Mas uma página de configurações com salvamentos otimistas instantâneos e transições de estado animadas? Isso também é extraordinário. Entenda a personalidade e os objetivos do projeto antes de decidir o que é apropriado.

### Proponha antes de construir

Este comando tem o maior potencial de dar errado. NÃO pule direto para a implementação. Você DEVE:

1. **Pensar em 2-3 direções diferentes**: considere técnicas, níveis de ambição e abordagens estéticas diferentes. Para cada direção, descreva brevemente como o resultado ficaria e que sensação passaria.
2. **Obter a escolha do usuário antes de escrever qualquer código.** Pergunte diretamente ao usuário para esclarecer o que você não consegue inferir. Coloque a descrição de cada direção e seus trade-offs (suporte de navegadores, custo de desempenho, complexidade) dentro da própria opção, para que o usuário escolha entre coisas que ele consegue ler. Uma pergunta estruturada bloqueia a mensagem em que vai até que o usuário responda, então direções escritas junto da pergunta ficam invisíveis enquanto se pede ao usuário que escolha entre elas.
3. Só prosseguir com a direção que o usuário confirmar.

Pular esta etapa arrisca construir algo constrangedor que precisará ser jogado fora.

### Itere com automação de navegador

Efeitos tecnicamente ambiciosos quase nunca funcionam de primeira. Você DEVE usar ativamente ferramentas de automação de navegador para pré-visualizar seu trabalho, verificar visualmente o resultado e iterar. Não presuma que o efeito está certo; confira. Espere várias rodadas de refinamento. A distância entre "tecnicamente funciona" e "parece extraordinário" é vencida com iteração visual, não apenas com código.

---

## Avalie o que "extraordinário" significa aqui

O tipo certo de ambição técnica depende inteiramente daquilo com que você está trabalhando. Antes de escolher uma técnica, pergunte: **o que faria um usuário DESTA interface específica dizer "uau, que legal"?**

### Para superfícies visuais/de marketing
Páginas, seções hero, landing pages, portfólios: o "uau" costuma ser sensorial: uma revelação guiada pela rolagem, um fundo com shader, uma transição de página cinematográfica, arte generativa que responde ao cursor.

### Para UI funcional
Tabelas, formulários, diálogos, navegação: o "uau" está em como ela SE SENTE: um diálogo que se transforma a partir do botão que o acionou via View Transitions, uma tabela de dados que renderiza 100 mil linhas a 60fps via virtual scrolling, um formulário com validação em streaming que parece instantânea, arrastar e soltar com física de mola.

### Para UI crítica em desempenho
O "uau" é invisível, mas sentido: uma busca que filtra 50 mil itens sem piscar, um formulário complexo que nunca bloqueia a thread principal, um editor de imagens que processa quase em tempo real. A interface simplesmente nunca hesita.

### Para interfaces com muitos dados
Gráficos e dashboards: o "uau" está na fluidez: renderização acelerada por GPU via Canvas/WebGL para conjuntos de dados enormes, transições animadas entre estados dos dados, layouts de grafos dirigidos por força que se acomodam naturalmente.

**O fio condutor**: algo na implementação vai além do que os usuários esperam de uma interface web. A técnica serve à experiência, não o contrário.

## O kit de ferramentas

Organizado pelo que você está tentando alcançar, não pelo nome da tecnologia.

### Faça as transições parecerem cinematográficas
- **View Transitions API** (mesmo documento: todos os navegadores; entre documentos: sem Firefox): transformação de elementos compartilhados entre estados. Um item de lista que se expande em uma página de detalhe. Um botão que se transforma em um diálogo. É o mais próximo de animações FLIP nativas.
- **`@starting-style`** (todos os navegadores): anime elementos de `display: none` até visíveis apenas com CSS, incluindo keyframes de entrada
- **Física de mola**: movimento natural com massa, tensão e amortecimento em vez de cubic-bezier. Bibliotecas: motion (antigo Framer Motion), GSAP, ou escreva seu próprio solucionador de molas.

### Vincule a animação à posição de rolagem
- **Animações guiadas pela rolagem** (`animation-timeline: scroll()`): só CSS, sem JS. Parallax, barras de progresso e sequências de revelação, tudo guiado pela posição de rolagem. (Chrome/Edge/Safari; Firefox: apenas com flag; sempre forneça um fallback estático)

### Renderize além do CSS
- **WebGL** (todos os navegadores): efeitos de shader, pós-processamento, sistemas de partículas. Bibliotecas: Three.js, OGL (leve), regl. Use para efeitos que o CSS não consegue expressar.
- **WebGPU** (Chrome/Edge; Safari 26+; Firefox no Windows/macOS; apenas com flag no Firefox para Linux/Android): computação de GPU de nova geração, mais poderosa que o WebGL. Sempre tenha fallback para WebGL2.
- **Canvas 2D / OffscreenCanvas**: renderização personalizada, manipulação de pixels ou tirar totalmente a renderização pesada da thread principal via Web Workers + OffscreenCanvas.
- **Cadeias de filtros SVG**: mapas de deslocamento, turbulência, morfologia para efeitos de distorção orgânica. Animáveis via CSS.

### Faça os dados parecerem vivos
- **Virtual scrolling**: renderize apenas as linhas visíveis em tabelas/listas com dezenas de milhares de itens. Nenhuma biblioteca é necessária em casos simples; TanStack Virtual para os complexos.
- **Gráficos acelerados por GPU**: visualização de dados renderizada em Canvas ou WebGL para conjuntos de dados grandes demais para SVG/DOM. Bibliotecas: deck.gl, renderizadores personalizados baseados em regl.
- **Transições de dados animadas**: transforme entre estados do gráfico em vez de substituí-los. `transition()` do D3 ou View Transitions para gráficos baseados em DOM.

### Anime propriedades complexas
- **`@property`** (todos os navegadores): registre propriedades CSS personalizadas com tipos, permitindo animar gradientes, cores e valores complexos que o CSS normalmente não consegue interpolar.
- **Web Animations API** (todos os navegadores): animações controladas por JavaScript com o desempenho do CSS. Componíveis, canceláveis, reversíveis. A base para coreografias complexas.

### Amplie os limites de desempenho
- **Web Workers**: tire a computação da thread principal. Processamento pesado de dados, manipulação de imagens, indexação de buscas: qualquer coisa que causaria travamentos.
- **OffscreenCanvas**: renderize em uma thread de Worker. A thread principal fica livre enquanto visuais complexos são renderizados em segundo plano.
- **WASM**: desempenho quase nativo para funcionalidades com computação pesada. Processamento de imagens, simulações físicas, codecs.

### Interaja com o dispositivo
- **Web Audio API**: áudio espacial, visualizações reativas ao áudio, feedback sonoro. Exige um gesto do usuário para começar.
- **APIs de dispositivo**: orientação, luz ambiente, geolocalização. Use com moderação e sempre com a permissão do usuário.

**OBSERVAÇÃO**: este comando trata de aprimorar como uma interface SE SENTE, não de mudar o que um produto FAZ. Adicionar colaboração em tempo real, suporte offline ou novas capacidades de backend são decisões de produto, não aprimoramentos de UI. Concentre-se em fazer as funcionalidades existentes parecerem extraordinárias.

## Implemente com disciplina

### Aprimoramento progressivo é inegociável

Toda técnica deve degradar com elegância. A experiência sem o aprimoramento ainda precisa ser boa.

```css
@supports (animation-timeline: scroll()) {
  .hero { animation-timeline: scroll(); }
}
```

```javascript
if ('gpu' in navigator) { /* WebGPU */ }
else if (canvas.getContext('webgl2')) { /* WebGL2 fallback */ }
/* CSS-only fallback must still look good */
```

### Regras de desempenho

- Mire em 60fps. Se cair abaixo de 50, simplifique.
- Inicialize recursos pesados (contextos WebGL, módulos WASM) de forma preguiçosa, apenas quando estiverem perto da viewport.
- Pause a renderização fora da tela. Elimine o que não dá para ver.
- Teste em dispositivos intermediários reais, não apenas na sua máquina de desenvolvimento.

### O polimento faz a diferença

A distância entre "legal" e "extraordinário" está nos últimos 20% de refinamento: a curva de easing de uma animação de mola, o deslocamento de tempo em uma revelação escalonada, o movimento secundário sutil que faz uma transição parecer física. Não entregue a primeira versão que funciona; entregue a versão que parece inevitável.

**NUNCA**:
- Entregue efeitos que causem travamentos em dispositivos intermediários
- Use APIs de ponta sem um fallback funcional
- Adicione som sem consentimento explícito do usuário
- Use ambição técnica para mascarar fundamentos de design fracos; corrija-os primeiro com outros comandos
- Empilhe vários momentos extraordinários competindo entre si. Foco gera impacto, excesso gera ruído

## Verifique o resultado

- **O teste do uau**: mostre a alguém que ainda não viu. A pessoa reage?
- **O teste da remoção**: tire o efeito. A experiência parece empobrecida, ou ninguém percebe?
- **O teste do dispositivo**: rode em um celular, um tablet, um Chromebook. Continua fluido?
- **O teste do contexto**: isso faz sentido para ESTA marca e ESTE público?

"Tecnicamente extraordinário" não significa usar a API mais nova. Significa fazer uma interface realizar algo que os usuários não imaginavam que um site pudesse fazer.
