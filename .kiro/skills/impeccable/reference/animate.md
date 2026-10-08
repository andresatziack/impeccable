> **Contexto adicional necessário**: restrições de desempenho.

Use movimento para explicar estado, relação e hierarquia, ou para criar um momento autoral que a superfície tenha merecido. Decoração sem propósito é dívida de animação.

---

## Modo do visitante

- **Persuade (persuadir) + Experience (experiência):** o movimento pode carregar a voz. Prefira uma única sequência focal ensaiada a revelações repetidas de seções.
- **Operate (operar) + Read (ler):** o movimento serve a feedback, estado e continuidade. Mantenha as transições rotineiras rápidas e não faça os usuários esperarem por coreografias de carregamento de página.
- **Nativo (`ios` / `android` / `adaptive`):** siga a seção Motion de [ios.md](ios.md) ou [android.md](android.md), incluindo o comportamento de Reduzir Movimento da plataforma. Não aplique as ferramentas web abaixo.

## Encontre a função

Inspecione a linguagem de movimento existente, os estados de interação, os dispositivos-alvo e o orçamento de desempenho. Encontre apenas os lugares onde o movimento:

- reconheceria uma ação;
- tornaria legível uma mudança de estado ou uma relação espacial;
- preservaria a continuidade em uma navegação ou mudança de layout;
- direcionaria a atenção em um momento significativo;
- encarnaria o mundo visual selecionado.

Pergunte apenas quando uma restrição relevante não puder ser inferida. Não anime uma área estática só porque ela existe.

## Defina a tese de movimento

Escreva um plano curto antes da implementação:

- **Momento focal:** a sequência ou interação que merece autoria, se houver.
- **Continuidade:** as mudanças de estado, layout ou navegação que precisam de explicação.
- **Feedback:** os controles e resultados que precisam de reconhecimento.
- **Orçamento:** quais efeitos podem ser custosos e com que frequência eles rodam.

O momento focal precisa vir deste produto e do conceito desta superfície. Um fade-and-rise genérico, um hover que levanta o elemento, uma camada de parallax ou uma revelação no scroll não são uma tese.

## Escolha o material pelo significado

Transform e opacity são bases confiáveis, não a paleta inteira. Escolha propriedades pelo que a transição comunica:

- **Continuidade e relação:** movimento de elemento compartilhado, transforms no estilo FLIP, view transitions ou deslocamento espacial deliberado.
- **Foco e profundidade:** mudanças controladas de blur, filter, backdrop, luz ou sombra.
- **Revelação e composição:** máscaras, clip paths, recortes ou oclusão controlada.
- **Matéria e energia:** cor, posição de gradiente, textura, distorção ou efeitos de shader quando o mundo visual e o runtime os suportam.
- **Estado e feedback:** a menor mudança que torna causa e resultado inconfundíveis.

Não empilhe técnicas em busca de espetáculo. Uma ideia de material forte, levada ao longo da sequência focal e de estados de apoio discretos, costuma bastar.

O stagger entre irmãos é apropriado quando uma lista aparece como lista. Limite o atraso total e nunca reinterprete cada seção rolada como uma lista escalonada.

## Tempo e easing

O tempo deve expressar distância e consequência:

| Duração | Uso típico |
|---|---|
| 100–150 ms | feedback imediato |
| 150–300 ms | mudança de estado rotineira |
| 300–500 ms | transição de layout, overlay ou view |
| 500–800 ms | uma entrada focal deliberadamente autoral |

A saída deve ser mais rápida que a entrada. Use uma desaceleração natural como `cubic-bezier(0.16, 1, 0.3, 1)` para chegadas confiantes; não use curvas de bounce ou elásticas por reflexo. Feedback longo parece latência.

## Implemente de acordo com o runtime

- Use transições e keyframes de CSS para estado declarativo e sequências delimitadas.
- Use a Web Animations API ou a biblioteca de movimento já existente no projeto para interrupção, sequenciamento e valores dinâmicos.
- Use View Transitions ou técnicas de elemento compartilhado quando a continuidade entre estados for o objetivo.
- Use movimento guiado por scroll somente quando a própria relação com o scroll carregar significado, com um fallback robusto.
- Não adicione uma dependência para um efeito que a stack existente consegue expressar de forma limpa.

Mantenha o conteúdo visível no estado padrão para que scripts com falha não escondam a página. Evite animar casualmente propriedades que determinam o layout, como `width`, `height`, `top`, `left` e margens; use FLIP, transforms ou técnicas de grid quando apropriado. Restrinja trabalhos de blur, filter, sombra, canvas e shader a regiões isoladas. Aplique `will-change` apenas durante animações conhecidas. Meça nos viewports e dispositivos-alvo em vez de presumir que transform significa rápido.

## Acessibilidade e controle

Respeite as preferências de reprodução automática e de som. Qualquer loop não essencial deve parar quando estiver fora da tela ou oculto.

Toda animação web precisa de um caminho `prefers-reduced-motion` com uma alternativa intencional. Remova ou reduza o deslocamento espacial, preservando as transições de opacidade, cor e estado que carregam significado. Movimento reduzido significa animações em menor número e mais suaves, não desativar todo o movimento; o feedback que confirma uma ação deve continuar legível.

## Verifique

- O movimento focal é específico do mundo visual e da superfície selecionados.
- Cada animação de apoio explica feedback, estado ou relação.
- Interrupção e uso repetido se comportam corretamente.
- Os caminhos de desktop, mobile e teclado continuam utilizáveis.
- O caminho `prefers-reduced-motion` reduz o deslocamento sem apagar feedbacks ou mudanças de estado significativos.
- Efeitos custosos permanecem fluidos no dispositivo-alvo.
- Remover uma animação faria perder significado ou caráter autoral, não apenas decoração.

Quando o movimento justificar seu lugar, passe para `/impeccable polish` para a etapa final.
