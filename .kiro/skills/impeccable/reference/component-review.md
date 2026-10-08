# Revisão do plano e dos assets

Use este checkpoint em builds guiados por comp (mockup) assim que cada região raster tiver sua plate e o gate de plates as tiver pontuado, antes de existir qualquer código de página. O comp aprovado é a referência. O usuário revisa duas coisas: as plates que serão entregues e o plano de produção de todo o resto, ou seja, quais regiões o código desenha. Uma região pintada planejada como código é o erro mais caro que um build guiado por comp comete, e é aqui que o usuário o detecta. Texto, controles e chrome são julgados depois, na primeira viewport montada.

## Planejar, capturar, servir

Rode `.kiro/skills/impeccable/scripts/impeccable component-review plan`. Ele grava `.impeccable/review/components.json` a partir da especificação medida, do comp e dos arquivos de plate, e se recusa, nomeando cada uma, enquanto alguma região raster estiver sem sua plate. Nunca grave nem edite esse arquivo nesta etapa: o pacote é derivado da especificação, então toda alteração pertence ao arquivo de regiões.

Se o harness (ferramenta de agente) expuser `component_review`, chame-o com `manifest_path` definido como `.impeccable/review/components.json`. O host captura, apresenta a revisão e devolve as decisões do usuário. Uma solicitação suspensa está aguardando o usuário; não é um build com falha nem uma aprovação.

Caso contrário, rode `.kiro/skills/impeccable/scripts/impeccable component-review capture --manifest .impeccable/review/components.json` e, em seguida, inicie `.kiro/skills/impeccable/scripts/impeccable component-review serve --session <returned session>` em segundo plano. Abra a URL que ele imprimir no navegador disponível e aguarde o usuário; `serve` sai com 0 quando ele envia. Leia as decisões com `.kiro/skills/impeccable/scripts/impeccable component-review status --session <id>`; `.kiro/skills/impeccable/scripts/impeccable component-review verify --manifest .impeccable/review/components.json` confirma a aprovação e recusa entradas pendentes, que precisam de ajustes ou obsoletas. Nunca envie a página nem grave um recibo em nome do usuário.

`serve` sai com 2 quando esta sessão não tem navegador (o mesmo sinal da página de decisão) e com 4 quando fecha após 30 minutos ociosos sem decisão. Em ambos os casos, ninguém está revisando: pare de aguardar, não aprove nada por conta própria e não avance o build além deste checkpoint. Encerre a execução e relate a revisão do plano e dos assets como pendente, com o ID da sessão, para que o usuário possa retomá-la. Uma revisão em espera é trabalho pendente, não um build concluído.

## Aja conforme o recibo

Aplique as decisões do usuário tal como foram dadas, nunca o seu próprio veredito favorável no lugar delas.

- **approve**: quando todos os itens estiverem aprovados e o inventário confirmado, avance para o hero (seção hero).
- **revise** (uma plate): regenere essa plate no mesmo caminho com o feedback do usuário.
- **revise with split** (um asset): substitua essa região no arquivo de regiões por suas camadas: uma plate de moldura com uma abertura transparente (kind `plate`, mesma caixa), o conteúdo como sua própria região `image` na caixa da abertura e cada parte móvel (uma veneziana, uma porta) como sua própria plate. Nomeie cada camada a partir da região original, como `<id>-frame`, `<id>-view` ou `<id>-shutter-left`, para que a próxima rodada a mostre como parte da solicitação do usuário. Rode novamente `.kiro/skills/impeccable/scripts/impeccable comp-spec --comp <comp.png> --regions <regions.json>` e produza as plates.
- **revise** (um item do plano): altere o arquivo de regiões conforme o feedback indicar (redimensione ou estenda uma região raster, separe um material em sua própria região de plate ou ajuste a região de código), rode novamente `.kiro/skills/impeccable/scripts/impeccable comp-spec --comp <comp.png> --regions <regions.json>` e produza as novas plates.
- **reclassify** (uma região de código): no arquivo de regiões, mude o `kind` dessa região para o que o usuário escolheu e reescreva o `note` dela para descrever o material. Rode novamente `.kiro/skills/impeccable/scripts/impeccable comp-spec --comp <comp.png> --regions <regions.json>` e depois produza as novas plates, com o asset producer quando houver subagentes disponíveis.
- **missing**: adicione a região ao arquivo de regiões, rode novamente `.kiro/skills/impeccable/scripts/impeccable comp-spec --comp <comp.png> --regions <regions.json>` e produza a plate dela se for raster.

Em seguida, rode `plan`, `capture` e `serve` de novo. Decisões inalteradas são mantidas, então o usuário vê apenas o que mudou. Qualquer alteração na especificação, ou uma plate substituída após a aceitação, exige uma nova rodada; o gate da fase de build continua fechado até que a revisão da especificação atual seja aceita.

## Montar e revisar

Construa a primeira viewport a partir das plates e do plano aprovados e rode o gate do hero. A revisão humana não dispensa as verificações de integridade dele. Após três tentativas de hero com falha, ou três tentativas de responsividade com falha antes de qualquer primeira viewport ser aceita, pare de iterar e apresente a revisão da primeira viewport com o build atual; o olho do usuário decide o que as medições não conseguiram.

Apresente um segundo manifesto em `.impeccable/review/hero.json`. Este você mesmo escreve, e o formato dele é fixo:

```json
{
  "schemaVersion": 2,
  "stage": "hero",
  "id": "hero",
  "title": "First viewport",
  "comp": {"path": ".impeccable/mocks/comp.png", "width": 1536, "height": 1024},
  "components": [{
    "id": "first-viewport",
    "name": "First viewport",
    "medium": "HTML / CSS",
    "note": "Assembled first viewport",
    "box": {"x": 0, "y": 0, "w": 1, "h": 1},
    "preview": {"kind": "page", "path": "index.html"},
    "dependencies": ["styles.css", "assets/plates/sky.png", "fonts/display.woff2"]
  }]
}
```

- `schemaVersion` é 2. A versão 3 é o pacote de revisão do plano e exige o stage `components`. `codeRegions` e `specSha256` existem apenas na versão 3 e são recusados aqui. `reviewGroup` também é recusado: a versão 3 o rejeita, e qualquer outra versão só o aceita em um preview de página em um manifesto de stage `components`, nunca em um manifesto de hero.
- `comp.path` é o valor de `comp` em `.impeccable/build/spec.json`, e `width` e `height` são o tamanho em pixels desse PNG.
- Exatamente um componente, com `box` exatamente `{"x": 0, "y": 0, "w": 1, "h": 1}` e `name`, `medium` e `note` como strings.
- `preview.path` é a entrada da página que o build verifica nos gates (`artifact` em `.impeccable/build/state.json`).
- `dependencies` lista todos os outros arquivos que a página carrega (folhas de estilo, scripts, plates, imagens, fontes) como strings simples. Todo caminho, aqui e acima, é relativo ao projeto e simples: sem `./` ou `/` no início, sem `..`, sem URL, sem `?`, `#`, `%`, `:` nem barra invertida. Uma requisição a um arquivo ausente desta lista, ou a outro host, faz a captura falhar.

A referência continua sendo o comp aprovado. Chame a mesma ferramenta de revisão do host, ou rode `capture`, `serve` e `verify` com este manifesto. Um feedback de que precisa de ajustes inicia outra rodada de montagem.

A aceitação encerra a revisão humana deste build: nunca peça aprovação do plano, dos assets ou da montagem novamente. Enquanto a página renderizar o que o usuário aceitou, a pontuação do hero, a verificação de paleta e todas as medições numéricas são apenas consultivas; os vetos de material continuam valendo (uma plate ausente ou não referenciada, uma ilustração em SVG, um recorte orgânico, uma plate cortada, tinta inventada, falha de presença renderizada). Uma região de texto, controle ou chrome que a captura de tela aceita também não tem fica resolvida pela aceitação; uma plate que ela não tem é uma pergunta para o usuário, explicada no motivo do gate: não se afaste por conta própria do que ele aceitou, mas execute a resposta dele, inclusive posicionar a plate. Quando a captura não corresponder mais à captura de tela aceita, restaure o que o usuário aceitou; até lá, as medições se aplicam. Conclua o restante da página, o comportamento responsivo, as verificações de finalização e a documentação usando a primeira viewport aceita como direção visual. Isso é calibração da primeira viewport, não uma afirmação de que o usuário revisou o resto da página. Edições em folhas de estilo compartilhadas não reabrem a aprovação. Preserve a direção aceita; uma alteração explícita posterior do usuário é uma nova tarefa.

A captura da página montada executa scripts inline e scripts locais declarados a partir das entradas fixadas. APIs de rede, frames e workers não estão disponíveis; a viewport inicial precisa se estabilizar antes da captura. Mantenha a página real e declare seus scripts em vez de remover comportamento para passar na revisão.
