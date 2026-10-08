O layout transforma a prioridade do produto em ordem de leitura, agrupamento, ritmo e espaço utilizável. Diagnostique o problema estrutural antes de mover caixas.

---

## Modo do visitante

- **Persuade (persuadir) + Experience (experiência):** a composição pode ser assimétrica, fluida ou intencionalmente disruptiva quando o mundo visual selecionado a justifica.
- **Operate (operar) + Read (ler):** estrutura previsível, densidade estável e linearidade navegável são affordances.
- **Nativo:** siga [ios.md](ios.md) ou [android.md](android.md) para navegação, insets, adaptação e alvos de toque.

Preserve o mundo visual estabelecido. Um comando de layout muda a estrutura dentro dele; a substituição da identidade pertence a [new-work.md](new-work.md).

## Duas avaliações isoladas

Quando uma ferramenta de subagente estiver disponível e permitida, execute estas avaliações de forma independente; caso contrário, execute-as você mesmo nesta ordem.

1. **Avaliação de layout:** inspecione estados e viewports representativos. Responda a cada pergunta abaixo com evidência renderizada ou do código-fonte:
   - **Ordem de leitura:** aplique o teste de apertar os olhos. Com os detalhes borrados, você ainda consegue identificar, em ordem, o elemento primário, o elemento secundário e os grupos principais?
   - **Agrupamento:** itens relacionados estão próximos e grupos distintos estão separados, ou os contêineres estão compensando uma proximidade fraca?
   - **Ritmo:** intervalos compactos e generosos criam uma cadência deliberada, ou um único valor de espaçamento se repete até que tudo tenha o mesmo peso?
   - **Estrutura:** a topologia corresponde ao conteúdo e à tarefa? Cards, colunas ou seções repetidos são genuinamente equivalentes, ou apenas um padrão do framework?
   - **Densidade:** a quantidade de informação por região condiz com a frequência de uso, a complexidade da decisão e o modo do visitante?
   - **Adaptação:** nos estados estreito, intermediário, largo, com zoom e localizado, o que é reordenado, recolhido, quebrado em linhas, rolado ou permanece fixo? A ordem do DOM e do foco ainda concorda com a ordem visual?
   - **Extremos:** conteúdo longo, estados vazios, overlays, elementos fixos (sticky), áreas seguras e alvos de toque pequenos expõem falhas estruturais?
2. **Varredura mecânica:** execute:

```bash
.kiro/skills/impeccable/scripts/impeccable detect --json --scope layout [target files or dirs]
```

Inspecione também espaçamentos arbitrários, overflow, empilhamento e comportamento de contêiner que o detector não consegue resolver. Mantenha a evidência mecânica fora da primeira avaliação e depois sintetize as duas passagens antes de editar. Uma varredura limpa não consegue provar hierarquia nem ritmo.

## Defina a tese espacial

Antes de editar, nomeie:

- o caminho principal de leitura ou de tarefa;
- o que pertence junto e o que precisa se separar;
- qual elemento lidera e qual dá suporte;
- a densidade e o ritmo de espaçamento pretendidos;
- como a estrutura muda entre contêineres, viewports, modos de entrada e extremos de conteúdo.

Escolha o modelo estrutural mais simples que expresse essas relações. Use as primitivas de layout de acordo com as relações que elas controlam e nomeie semanticamente os papéis reutilizáveis de espaçamento e de contêiner.

## Aplique

- Agrupe pelo significado. Use a proximidade antes de adicionar contêineres ou decoração.
- Crie ritmo por meio de contraste deliberado entre intervalos compactos e generosos.
- Use uma escala de espaçamento documentada em vez de valores avulsos. Uma base de 4 unidades geralmente oferece os passos intermediários úteis que uma escala só de 8 deixa de fora.
- Deixe a hierarquia seguir a prioridade do produto, não os padrões do framework.
- Mantenha conteúdos distintos visualmente distintos sem transformar cada grupo em um componente isolado.
- Torne o comportamento responsivo estrutural: reordene, recolha, refaça o fluxo ou revele com base no que continua importante.
- Prefira componentes sensíveis ao contêiner quando o mesmo componente aparecer em contextos diferentes.
- Use `gap` para o ritmo entre irmãos quando ele expressar a relação de forma mais direta do que margens nos filhos.
- Mantenha os alvos de toque utilizáveis mesmo quando suas marcas visíveis forem pequenas.
- Use profundidade apenas quando ela esclarecer estado ou hierarquia.
- Faça correções ópticas somente depois de inspecionar o resultado renderizado.

Variação não é um objetivo em si. A repetição deve apoiar o reconhecimento; quebre-a apenas quando o conteúdo ou a prioridade mudarem.

## Verifique

- O teste de apertar os olhos ainda revela, em ordem, o primário, o secundário e os grupos principais.
- O caminho de leitura e de tarefa permanece claro em todos os tamanhos suportados.
- Conteúdos relacionados se agrupam naturalmente; conteúdos não relacionados não se misturam.
- Espaçamentos compactos e generosos criam um ritmo intencional em vez de uma repetição monótona.
- A densidade corresponde à frequência de uso e à complexidade do conteúdo.
- Texto longo, estados vazios, localização, zoom e conteúdo dinâmico não quebram a estrutura.
- A ordem de teclado, de toque e de tecnologias assistivas concorda com a ordem visual.
- A varredura mecânica final não tem achados sem explicação.

Responda a cada item com evidência renderizada ou do código-fonte e depois execute a varredura novamente. Não substitua a verificação por um “sim” vazio.

Quando a estrutura se sustentar, passe para `/impeccable polish`.

## Parâmetros de assinatura do modo live

Toda variante declara um parâmetro geral `density` e constrói o espaçamento em função de `var(--p-density, 1)`.

```json
{"id":"density","kind":"range","min":0.6,"max":1.4,"step":0.05,"default":1,"label":"Density"}
```

Adicione um parâmetro estrutural apenas quando a topologia de fato se ramificar. Siga o contrato de parâmetros de [live.md](live.md).
