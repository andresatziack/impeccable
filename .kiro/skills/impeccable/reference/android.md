# Plataforma Android

Para apps Android nativos: Jetpack Compose, Android Views, React Native, Expo e Flutter rodando em hardware Android.

No nativo, o modo do visitante restringe o que a expressão pode sobrepor. O Material Design 3 rege a estrutura, a navegação e a interação em todos os modos; a marca se expressa por meio do sistema de temas do Material (papéis de cor, escala tipográfica, forma, movimento). Um app multiplataforma com Material em toda parte que também é distribuído para iPhone ainda deve ao iOS as garantias do sistema operacional nesse hardware: insets de área segura, Reduce Motion, gesto de voltar deslizando a partir da borda.

## O teste de desleixo do Android

Um usuário fluente em Android confiaria neste app ou tropeçaria em componentes fora da especificação? O sinal mais comum é um app de iOS vestindo a pele do Android: uma navegação apenas inferior copiada do iPhone, uma seta de voltar que ignora o gesto Back do sistema, switches e diálogos em formato Cupertino. O Material 3 é o livro de regras; siga seus componentes e aplique o tema da marca por meio dele.

## Layout e estrutura

- **Navegação Material, ajustada ao tamanho.** Barra de navegação (inferior, 3–5 destinos) em largura compacta; trilho de navegação (navigation rail) ou gaveta (drawer) em largura expandida. Nunca entregue a barra inferior do celular sem alterações num tablet.
- **O Back do sistema sempre funciona.** Respeite o gesto de Back preditivo e o botão Back; nunca prenda o usuário nem sequestre o gesto.
- **De borda a borda com insets de janela.** Aplique os insets da barra de status, da barra de navegação, do recorte da tela e do IME para que o conteúdo nunca fique escondido atrás das barras do sistema ou do teclado.
- **Barra superior do app para o contexto da tela**; combine com um FAB quando a tela tiver uma única ação primária.

## Áreas de toque

- **Mínimo de 48×48 dp** para toda área de toque, com pelo menos 8 dp entre elas.

## Tipografia

- **Escala tipográfica do Material.** Papéis Display, Headline, Title, Body e Label (cada um em large/medium/small). Mapeie o texto para papéis; nunca escolha tamanhos à mão em cada tela.
- **Roboto é a fonte do sistema**; aplique uma fonte da marca por meio da escala tipográfica, mantendo corpo de texto, rótulos e controles legíveis e consistentes.
- **Unidades sp, nunca px fixos**, para que a tipografia siga a configuração de tamanho de fonte do sistema.

## Cor e temas

- **Papéis de cor do Material** (primary, on-primary, surface, surface-variant, secondary-container, outline, error). Os tokens de papel resolvem automaticamente as variantes claro/escuro e de contraste; hex bruto quebra nesses casos.
- **Dynamic Color (Material You)** onde fizer sentido: derive o esquema do papel de parede do usuário no Android 12+, com um fallback estático.
- **O tema escuro é um esquema de primeira classe.** Projete-o e teste-o; nunca uma inversão rápida.
- **Elevação tonal.** Transmita elevação pelos níveis tonais padrão de superfície (mais sombra quando apropriado); nada de sombras projetadas arbitrárias.

## Componentes e movimento

- **Componentes Material.** Botões (filled / tonal / outlined / text), FAB, switches, chips, snackbars, bottom sheets, diálogos Material, barra/trilho/gaveta de navegação. Nunca porte controles do iOS nem invente equivalentes.
- **Um FAB, uma ação primária.** Nunca empilhe FABs nem gaste um em uma tarefa secundária.
- **Snackbars para feedback transitório** (com ação quando útil, nunca um toast para isso); diálogos apenas para decisões que precisam interromper.
- **Padrões de movimento do Material.** Container transform, shared-axis e fade-through, com easing e durações padrão; respeite a configuração do sistema Remover animações com um crossfade ou um corte instantâneo.

## Verificando o build

- **As capturas de tela vêm do emulador ou de um dispositivo conectado, nunca de um navegador.** Faça o build e instale, depois capture com `adb exec-out screencap -p > <path>` (escolha um dispositivo com `adb -s <serial>` quando houver vários conectados). Capture cada classe de dispositivo para a qual o app é distribuído, pelo menos um celular e, quando tablets forem um alvo, um tablet, e grave os arquivos onde o fluxo de revisão os espera.
- **Tema escuro e escala de fonte fazem parte da verificação.** `adb shell cmd uimode night yes` alterna o tema; `adb shell settings put system font_scale 1.3` (restaure `1.0` depois) revela os rótulos cortados que um layout fixo esconde; com vários alvos conectados, o `-s <serial>` da captura também vai nesses comandos.
- **Emuladores dão amplitude; gestos, taxas de atualização e desempenho exigem hardware.** Diga qual deles produziu a evidência.
