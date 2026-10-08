> **Contexto adicional necessário**: plataformas/dispositivos-alvo e contextos de uso.

Adapte um design **nativo** existente (`ios` / `android` / `adaptive`) para um contexto diferente: outra classe de dispositivo, orientação, plataforma ou origem. A armadilha é tratar a adaptação como redimensionamento. A tarefa é repensar a experiência para o novo contexto, dentro das convenções de plataforma de [ios.md](ios.md) / [android.md](android.md); leia a referência da plataforma-alvo antes de planejar, caso a etapa de Setup ainda não tenha feito isso.

## Avaliar o desafio de adaptação

1. **Contexto de origem**: para o que ele foi projetado e que suposições fez? (Só celular? Só retrato? As convenções de uma única plataforma? Um site?)
2. **Contexto-alvo**: qual classe de dispositivo (celular, tablet, dobrável), orientação, plataforma e postura de uso (com uma mão, em movimento, vs. com as duas mãos, em repouso)?
3. **O que quebra**: navegação que não cabe no alvo, layouts que esticam em vez de se reestruturar, gestos ou controles que não existem lá?

## Estratégias de adaptação

### Celular → Tablet (iPad / telas grandes)

- **Reestruture, não estique.** Uma UI de celular ampliada num tablet é o modo de falha. Use size classes (iOS) / window size classes (Android) para trocar a estrutura.
- **A navegação muda de forma**: a tab bar permanece ou vira uma barra lateral no iPad; a barra de navegação do Android vira um trilho (rail) ou uma gaveta (drawer) em largura expandida.
- **Use a largura**: split view / mestre-detalhe (lista + detalhe lado a lado), grids com várias colunas, popovers onde os celulares usavam sheets.
- **Multitarefa é um tamanho, não um caso extremo**: o Split View do iPad e o modo multijanela do Android podem lhe entregar uma janela com largura de celular num tablet; um layout guiado por size classes resolve os dois sem custo extra.

### Orientação e dobráveis

- A paisagem reestrutura (painéis lado a lado, controles reposicionados); nunca corte nem use letterbox. Trave a orientação apenas quando a tarefa realmente exigir.
- Dobráveis (Android): reaja à postura e à dobradiça por meio das window size classes; teste dobrado, desdobrado e no modo mesa (tabletop).

### Plataforma → plataforma (iOS ↔ Android)

Traduza as convenções; nunca as transplante:

| iOS | Android |
|---|---|
| Tab bar | Barra de navegação / trilho / gaveta |
| Voltar deslizando pela borda, chevron de voltar | Gesto / botão de Back preditivo |
| Switch, segmented control, seletores do sistema | Switch Material, chips, seletores Material |
| Action sheet | Bottom sheet / diálogo Material |
| SF Symbols, SF Pro, Dynamic Type | Material Symbols, Roboto, escala em sp |
| Cores semânticas do sistema, materiais | Papéis de cor do Material, elevação tonal |
| Transições de push/sheet do sistema | Container transform, shared-axis, fade-through |

Reconstrua a navegação e os controles no vocabulário do alvo; leve a camada expressiva da marca (intenção da paleta, destaque tipográfico, personalidade do movimento) por meio do sistema de temas do alvo.

### Web → nativo (portando um site ou web app)

Reconforme, não apenas reflua. Substitua a navegação web pelo modelo da plataforma, controles com formato de HTML por controles da plataforma, affordances de hover por outras pensadas primeiro para toque, e tipografia baseada em px por Dynamic Type / sp. Depois, submeta o resultado à referência completa da plataforma; o teste de desleixo de lá é o critério de aceitação.

## Implementar e verificar

- Conduza a estrutura por **size classes / window size classes**, nunca por verificações de modelo de dispositivo.
- Respeite as áreas seguras e os insets de janela em toda nova configuração (notch, dobradiça, barra de status, teclado).
- Teste em simuladores para ter amplitude e depois em hardware real para ter a verdade: pelo menos um celular e um tablet por plataforma distribuída, nas duas orientações, com tela dividida onde houver suporte.

Quando a adaptação parecer nativa em cada contexto, passe para `/impeccable polish` para a passada final.

**NUNCA**:
- Entregue um layout de celular esticado num tablet
- Porte os controles ou a navegação de uma plataforma para a outra
- Esconda funcionalidades essenciais em dispositivos menores (se importa, faça funcionar)
- Trave a orientação para fugir de um bug de layout
- Confie apenas em simuladores (postura, gestos e desempenho exigem hardware)
