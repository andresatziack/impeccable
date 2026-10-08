# Plataforma iOS

Para apps nativos de iOS / iPadOS: SwiftUI, UIKit, React Native, Expo e Flutter rodando em hardware Apple.

No nativo, o modo do visitante restringe o que a expressão pode sobrepor. A conformidade com as HIG rege a estrutura, a navegação e a interação em todos os modos; a marca se expressa pela camada que a plataforma deixa aberta (tint, tipografia, movimento, conteúdo).

## O teste de desleixo do iOS

Um usuário fluente em iPhone confiaria neste app ou pararia diante de controles fora da especificação? O sinal revelador é "portado de um site": barras de navegação reinventadas, gestos de voltar personalizados, botões com cara de web, affordances que dependem de hover. Use por padrão os componentes da plataforma; afaste-se deles apenas por um motivo pelo qual o usuário lhe agradeceria.

## Layout e estrutura

- **Área segura.** Faça o layout dentro dos insets da área segura. Nenhum controle sob o notch, a Dynamic Island, o indicador de início ou os cantos arredondados.
- **Navegação do sistema.** Tab bar para 2–5 seções de nível superior (seções, nunca ações), pilha de navegação para hierarquia, sheet para tarefas autocontidas. Nada de navegação global personalizada, nada de metáforas misturadas.
- **O voltar deslizando pela borda continua vivo.** O gesto de voltar pela borda esquerda é memória muscular; nunca o desative nem o cubra.
- **Títulos grandes** nas telas de nível superior, recolhendo-se para inline na rolagem. Telas de detalhe profundas permanecem inline.

## Áreas de toque

- **Mínimo de 44×44 pt** para todo controle tocável, com espaço de respiro entre alvos adjacentes.

## Tipografia

- **Dynamic Type.** Use os estilos de texto do sistema (de Large Title a Caption) para que o texto siga o tamanho de leitura do usuário. Nada de tamanhos em pontos fixos no código.
- **A San Francisco sustenta a UI.** Corpo de texto, rótulos e controles ficam em SF Pro / SF Compact; uma fonte da marca pode aparecer em momentos de display.
- **Piso de 11 pt**; o Body tem 17 pt.

## Cor e materiais

- **Cores semânticas do sistema** (label, secondaryLabel, systemBackground, separator, tint). Elas se adaptam automaticamente ao Dark Mode e ao aumento de contraste; hex bruto quebra nesses casos.
- **O Dark Mode é uma aparência de primeira classe.** Projete e teste as duas.
- **Uma única cor de tint** comanda os elementos interativos; decoração não é função dela.
- **Materiais do sistema** para desfoque e translucidez atrás de barras e sheets; nada de glassmorphism feito à mão.

## Componentes e controles

- **Controles da plataforma.** Switch, segmented control, stepper, seletores do sistema, action sheets, alertas, menus de contexto, ações de deslizar. Reinventá-los por estilo é o desleixo nativo mais comum.
- **SF Symbols** para a iconografia: alinhados à linha de base, compatíveis com Dynamic Type, com variantes de peso e escala. Não misture um conjunto de ícones da web.
- **Modalidade deliberada.** Sheet para uma subtarefa focada e dispensável, cobertura em tela cheia para imersão. Cancelar/Concluir claros; respeite o deslizar para dispensar, a menos que a perda de dados exija uma proteção.
- **Listas agrupadas/inset** para conteúdo com formato de configurações; nada de pilhas de cards sob medida.

## Movimento

- **Transições do sistema.** O push desliza, as sheets sobem, a dispensa inverte a entrada. Transições personalizadas que brigam com o modelo de navegação desorientam.
- **Respeite o Reduce Motion.** Crossfade em vez de parallax e de grandes deslizamentos.

## Verificando o build

- **As capturas de tela vêm do Simulator, nunca de um navegador.** Faça o build e execute, depois capture com `xcrun simctl io booted screenshot <path>` (com vários em execução, substitua `booted` pelo UDID do alvo obtido em `xcrun simctl list devices booted`; nomes de exibição podem colidir, o UDID nunca). Capture cada classe de dispositivo para a qual o app é distribuído, pelo menos um iPhone e, quando o iPad for um alvo, um iPad, e grave os arquivos onde o fluxo de revisão os espera.
- **Dark Mode e Dynamic Type fazem parte da verificação.** `xcrun simctl ui booted appearance dark` alterna a aparência, reutilizando o UDID da captura quando vários estão iniciados; uma checagem num tamanho grande de Dynamic Type revela o truncamento que um layout fixo esconde.
- **Simuladores dão amplitude; postura, gestos e desempenho exigem hardware.** Diga qual deles produziu a evidência.
