> **Contexto adicional necessário**: cores de marca existentes.

Introduza a cor como hierarquia, significado e atmosfera. Preserve as convenções de marca e semânticas confirmadas; não substitua um mundo visual sob o pretexto de colori-lo.

---

## Modo do visitante

- **Persuade (persuadir) + Experience (experiência):** a cor pode carregar a voz e dominar grandes regiões quando o mundo visual selecionado pedir isso.
- **Operate (operar) + Read (ler):** a cor codifica principalmente ação, seleção, status, orientação e hierarquia de leitura. A raridade dá força a um acento.

## Audite antes de escolher

Leia o DESIGN.md, os tokens, os assets, os temas atuais e estados representativos. Identifique:

- quais cores são compromissos de marca confirmados;
- os papéis atuais de superfície, texto, ação e semântica;
- lugares onde a escala de cinza esconde hierarquia ou estado;
- falhas de contraste e comunicação feita só por cor;
- requisitos de claro/escuro ou de visualização de dados;
- se a tarefa pede mais cor ou uma nova identidade.

Se for necessária uma nova identidade, use [new-work.md](new-work.md). Pergunte apenas quando uma decisão de marca vinculante não puder ser inferida.

## Escolha uma estratégia

Antes de editar, nomeie a temperatura emocional pretendida, a relação dominante, a faixa de contraste e a dosagem de cor. A estratégia pode ser contida ou imersiva; ela deve seguir o briefing e o mundo visual selecionado, e não uma regra de porcentagem fixa.

Construa papéis, não um saco de amostras:

- fundo (canvas) e superfícies elevadas;
- texto primário e secundário;
- ação, foco e seleção;
- bordas e separadores;
- sucesso, aviso, erro e informação;
- categorias ou escalas de dados quando necessário.

Use o espaço de cor já existente no projeto. Para uma nova paleta web, prefira OKLCH, porque luminosidade e croma podem ser ajustados de forma previsível. Escolha o matiz a partir do significado do produto e da direção visual, nunca de uma associação padrão da categoria.

## Aplique em escala de sistema

- Deixe a cor mais forte dominar uma região ou um papel deliberado em vez de espalhar acentos minúsculos.
- Mantenha a ação principal fácil de encontrar; não gaste a cor dela em decoração.
- Tinja os neutros apenas quando o matiz da marca realmente criar coesão. Cinza neutro é válido quando serve ao mundo visual.
- Em superfícies coloridas, derive o texto secundário do matiz do primeiro plano ou da superfície em vez de usar um cinza genérico desbotado.
- Mantenha os significados semânticos consistentes, mas respeite as convenções da plataforma e do domínio em vez de presumir matizes fixos.
- Para dados, use luminosidade, croma, forma, rótulo ou padrão distintos para que a cor não seja o único código.
- No modo escuro, projete explicitamente a elevação das superfícies e o contraste; não inverta o tema claro mecanicamente.
- Defina valores primitivos e tokens semânticos quando o projeto tiver um sistema de tokens. Mudanças de tema normalmente devem remapear os papéis semânticos.

Decoração sem relação com hierarquia, estado, conteúdo ou o mundo visual não é uma estratégia de cor.

## Contraste e percepção

Verifique os pares computados de primeiro plano/fundo:

| Conteúdo | Mínimo WCAG AA |
|---|---|
| texto de corpo | 4.5:1 |
| texto grande | 3:1 |
| controles, ícones, indicadores de foco | 3:1 |

Não confie só nos olhos. Verifique estados interativos, overlays, texto sobre imagens, conteúdo desabilitado e os dois temas. Simule as deficiências visuais mais comuns. Informações transmitidas por cor também precisam de texto, forma, iconografia ou posição.

Ao derivar rampas em OKLCH, varie a luminosidade e reduza o croma perto do branco e do preto. Não mantenha croma alto em luminosidades extremas só para deixar a matemática uniforme. Prefira cores explícitas a cadeias de overlays translúcidos quando o alfa tornaria o contraste dependente do contexto.

## Verifique

- Cada cor tem um papel estável ou um propósito atmosférico específico do mundo visual.
- A atenção recai sobre a ação, o conteúdo ou o estado pretendidos.
- A paleta funciona em estados discretos, densos, interativos, de erro e vazios.
- Os temas claro e escuro são compostos cada um por si, não invertidos mecanicamente.
- O contraste e as pistas que não dependem de cor passam em todos os estados relevantes.
- O resultado é reconhecivelmente este produto, não um tratamento “colorido” genérico.

Quando a paleta justificar seu lugar, passe para `/impeccable polish` para a etapa final.

## Parâmetros de assinatura do modo live

Quando invocada a partir do modo live, toda variante declara um parâmetro `color-amount`. Escreva o CSS em função de `var(--p-color-amount, 0.5)` para que o usuário possa ir do neutro até a estratégia de cor completa da variante sem regeneração.

```json
{"id":"color-amount","kind":"range","min":0,"max":1,"step":0.05,"default":0.5,"label":"Color amount"}
```

Adicione no máximo dois parâmetros específicos da variante, como paleta, temperatura ou comportamento de tingimento. Siga o contrato de parâmetros de [live.md](live.md).
