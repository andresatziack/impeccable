# Mapa de regiões

Um mapa de regiões nomeia o que está de fato visível no comp (mockup) aprovado antes da produção de assets. Ele não é a construção de uma página nem uma aprovação de assets.

1. Rode `.kiro/skills/impeccable/scripts/impeccable comp-spec --comp <comp.png> --grid` e abra a imagem original e a imagem com grade.
2. Rode `.kiro/skills/impeccable/scripts/impeccable comp-spec --schema` para ver os campos do JSON. Escreva `regions.json` com um array `regions`. Cada região precisa de um `id` estável, `kind`, `note` e exatamente um entre `pixelBox`, `box` normalizado ou `grid`. Use as dimensões do comp original.
3. Rode `.kiro/skills/impeccable/scripts/impeccable comp-spec --comp <comp.png> --regions regions.json --inspect-map`. A saída aponta para um relatório, uma sobreposição, recortes exatos e folhas `COMPARE` de referências mascaradas. O padrão imprime os achados; `--json` imprime o relatório inteiro, com os caminhos das folhas em `comparisonSheets`.
4. Abra primeiro as folhas de comparação para inspecionar juntos os recortes afetados e depois abra recortes individuais onde for preciso mais detalhe. Compare os limites deles com o original. Inspecione os pixels de primeiro plano excluídos, assim como os erros de geometria. Toda caixa de código sobreposta que não seja contêiner é mascarada por inteiro, incluindo o espaço vazio dentro dela. Delimite elementos de texto separados separadamente, para que a arte nos vãos continue visível; um contêiner descreve a extensão do layout deles e nunca substitui seus filhos. Corrija o mapa e inspecione de novo; use um novo diretório de saída a cada vez. Zero erros não certifica a precisão dos recortes. Avisos de cobertura são dicas, não prova de completude.

Se o pedido terminar no mapeamento, pare com o mapa, o relatório de inspeção e os achados não resolvidos. Para continuar uma construção, meça o mapa inspecionado com `comp-spec --comp <comp.png> --regions regions.json` e siga [new-work.md](new-work.md).

`--auto` produz um esqueleto de faixas horizontais, não identificação de elementos. Ele é opcional e não substitui a autoria de um mapa.

## Contenção

`parentId` identifica uma região `container: true` que a envolve. Pai e filhos mantêm IDs e recortes separados. A contenção nunca transfere aprovação.

## O que varia de forma independente

Divida as regiões pelo que varia de forma independente: conteúdo que o site troca (fotos de ambientes, produtos, pessoas), partes móveis (qualquer coisa que um hover ou a interação característica mova) e estrutura (molduras, entornos, ornamentos). Uma janela com venezianas abertas para um ambiente é composta de três tipos de região: o entorno como uma placa com uma abertura transparente, o ambiente como uma região de imagem por baixo dela e cada veneziana como sua própria placa. Regiões sobrepostas são compostas na página. O `comp-spec` sinaliza uma região raster cuja nota nomeia uma moldura e a vista para a qual ela se abre (`baked-composite`), e a revisão do plano e dos assets a mostra primeiro ao usuário.

## Material pintado

Ao medir, o `comp-spec` sinaliza uma região `text`, `control` ou `chrome`, incluindo contêineres, cujo recorte pareça pintado (`painted-pixels`: muitas cores, gradientes suaves) e a lista em seu resumo. A revisão do plano e dos assets mostra primeiro ao usuário as regiões sinalizadas e aquelas marcadas como `codeDrawn` (material pintado que você escolheu desenhar em código). Não deixe essa detecção a cargo dele: se uma região for material pintado (uma figura, uma fotografia, uma superfície de metal ou papel), classifique-a agora como `plate`, `image` ou `texture`.

Recortes do comp são apenas evidência de referência, nunca assets de produção. O inspetor do mapa marca seus PNGs como derivados do comp.
