A tipografia carrega informação, hierarquia e voz. Melhore-a dentro do mundo visual estabelecido; não substitua a identidade a menos que o usuário tenha pedido.

---

## Modo do visitante

- **Persuade (persuadir) + Experience (experiência):** a tipografia de display pode carregar a voz. Use contraste decidido e escala responsiva quando a composição se beneficiar.
- **Operate (operar) + Read (ler):** estabilidade, escaneabilidade e medida vêm primeiro. Uma única família bem ajustada e uma escala fixa de papéis costumam ser o certo.
- **Nativo:** siga [ios.md](ios.md) ou [android.md](android.md), incluindo o dimensionamento da plataforma e o comportamento de acessibilidade.

Se a substituição da tipografia criar uma nova identidade, encaminhe por [new-work.md](new-work.md) e atualize o DESIGN.md. Caso contrário, preserve as famílias confirmadas e melhore o uso delas.

## Duas avaliações isoladas

Quando uma ferramenta de subagente estiver disponível e permitida, execute estas avaliações de forma independente; caso contrário, execute-as você mesmo nesta ordem. Não deixe os achados do detector ancorarem a avaliação de design.

1. **Avaliação tipográfica:** inspecione páginas e estilos representativos. Responda a cada pergunta abaixo com um arquivo, seletor ou valor computado:
   - **Autoridade e adequação:** quais fontes, pesos e papéis estão estabelecidos? Eles combinam com o produto e com o mundo visual selecionado, ou são padrões não examinados? Cada família é necessária?
   - **Hierarquia:** os papéis de título, corpo, rótulo, metadados e dados podem ser distinguidos num relance? Tamanhos ou pesos adjacentes estão próximos demais para cumprir funções diferentes?
   - **Escala e consistência:** existe uma escala de papéis deliberada, ou uma coleção de valores arbitrários? Os papéis repetidos permanecem idênticos entre telas e estados?
   - **Leitura:** o texto de corpo fica dentro de uma medida confortável de 45–75 caracteres? Altura de linha, ritmo de parágrafos, contraste e tracking estão ajustados à fonte, à largura, ao idioma e à superfície reais?
   - **Estresse:** o que acontece com títulos longos, expansão por localização, zoom, contêineres estreitos, pesos ausentes e fallback de fonte?
   - **Entrega:** apenas os recursos usados são carregados? As métricas de fallback, a estratégia de carregamento e as configurações de fontes variáveis evitam texto invisível e reflow disruptivo?
2. **Varredura mecânica:** execute:

```bash
.kiro/skills/impeccable/scripts/impeccable detect --json --scope type [target files or dirs]
```

Inspecione também valores de fonte dinâmicos ou arbitrários que o detector não consegue interpretar. Sintetize as duas avaliações antes de editar, anotando o que cada uma detectou sozinha. Uma varredura limpa é um piso, não uma prova de boa tipografia.

## Defina o sistema

Antes de editar, declare:

- os papéis de que a interface precisa;
- o contraste pretendido entre esses papéis;
- a medida de leitura e a densidade;
- quais fontes e pesos existentes têm autoridade;
- quaisquer restrições de desempenho, localização ou acessibilidade.

Use o menor número de papéis e famílias que torne a hierarquia inconfundível. Combine tamanho, peso, espaço e tom de forma deliberada em vez de pedir que o tamanho sozinho faça todo o trabalho. Nomes de papéis e tokens devem descrever o propósito, não os valores.

## Aplique

- Mantenha o texto de corpo confortavelmente legível e ampliável. Use 1rem / 16px como piso comum do corpo na web, a menos que um papel denso, uma convenção da plataforma ou uma configuração do usuário justifique outra coisa.
- Mantenha a prosa na faixa de 45–75ch. Ajuste a altura de linha inversamente à medida: linhas mais largas geralmente precisam de mais entrelinha.
- Compense texto claro sobre superfícies escuras nos três eixos perceptivos: um pouco mais de altura de linha, um toque a mais de tracking e um passo a mais de peso quando a fonte precisar.
- Ajuste a altura de linha à fonte, à largura, ao idioma e ao contraste, não a uma proporção universal.
- Mantenha os papéis repetidos consistentes entre telas e estados.
- Use recursos numéricos, tabulares, de código e de rótulo quando o conteúdo se beneficiar deles.
- Carregue apenas os arquivos de fonte e os pesos usados. Forneça fallbacks com métricas compatíveis e evite bloquear o texto.
- Deixe a tipografia de display de marketing responder ao espaço disponível quando for útil; mantenha superfícies densas de produto e de leitura espacialmente previsíveis.
- Preserve o zoom do navegador, as configurações de fonte do usuário, o Dynamic Type e o dimensionamento de texto da plataforma.
- Use o espaçamento entre parágrafos ou o recuo da primeira linha como ritmo principal de parágrafo; combinar os dois geralmente marca a fronteira duas vezes.

Não torne a tipografia decorativa às custas da compreensão, nem introduza uma segunda família sem um papel claro que só ela possa desempenhar.

## Verifique

- Os papéis primário, secundário, de corpo e de metadados são reconhecíveis sem ler o texto.
- Textos longos continuam confortáveis nas larguras e idiomas relevantes.
- A tipografia pertence ao produto e ao seu mundo visual estabelecido.
- O carregamento não cria reflow disruptivo nem texto invisível.
- Os caminhos de zoom, dimensionamento de texto, foco, contraste e viewport reduzido continuam utilizáveis.
- A varredura mecânica final não tem achados sem explicação.

Responda a cada item com evidência renderizada ou do código-fonte e depois execute a varredura novamente. Não substitua a verificação por um “sim” vazio.

Quando a hierarquia se sustentar, passe para `/impeccable polish`.

## Parâmetros de assinatura do modo live

Toda variante declara um parâmetro geral `scale` e constrói sua escala tipográfica em função de `var(--p-scale, 1)`.

```json
{"id":"scale","kind":"range","min":0.85,"max":1.3,"step":0.05,"default":1,"label":"Scale"}
```

Adicione no máximo um parâmetro de combinação ou de peso quando ele representar uma escolha real do sistema. Siga o contrato de parâmetros de [live.md](live.md).
