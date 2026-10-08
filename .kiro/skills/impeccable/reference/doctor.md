Relate e repare o descompasso entre os artefatos do Impeccable deste projeto e o que a versão instalada lê: PRODUCT.md, DESIGN.md e seu arquivo auxiliar `.impeccable/design.json`, `.impeccable/config.json`, briefings de superfície persistidos e o hook de design.

Isto é manutenção, não design. Não faça redesign de nada, não abra arquivos além dos que o relatório nomeia e não rode nenhum outro comando como efeito colateral.

## O que este comando cobre, e o que não cobre

Três tipos de descompasso aparecem sob o rótulo "desatualizado". Mantenha-os separados:

- **Versão da ferramenta.** A skill instalada é mais antiga que a publicada. `impeccable context` informa isso na inicialização como `UPDATE_AVAILABLE` e `npx impeccable update` corrige. Não é tarefa deste comando.
- **Descompasso de esquema.** Um artefato foi escrito por um Impeccable mais antigo: campos que nada lê, campos agora esperados, arquivos em locais aposentados. É mecânico, e este comando repara a maior parte.
- **Descompasso de verdade.** O código evoluiu e o documento não o descreve mais. Nenhuma comparação de arquivos resolve isso. `document` é responsável pelo DESIGN.md, `init` é responsável pelo PRODUCT.md, e a tarefa deste comando é entregar a eles uma lacuna específica em vez de uma suspeita vaga.

## Etapa 1: Rode a verificação

```
.kiro/skills/impeccable/scripts/impeccable doctor --json
```

Acrescente `--target <path>` quando o usuário tiver nomeado um workspace, arquivo ou rota em um monorepo. Sem isso, o relatório descreve a raiz do repositório e, em um monorepo, esse muitas vezes é o projeto errado.

A saída traz `findings` (cada um com `id`, `artifact`, `path`, `severity`, `summary`, `fix`) e, em um monorepo, `workspaces` com a resolução de produto e de design de cada app. `ruleRegistryAvailable: false` significa que os ids de regras ignoradas não puderam ser validados; diga isso em vez de dar a entender que essa lista está limpa.

Um array `findings` vazio é o bom resultado. Diga isso em uma linha e pare.

## Etapa 2: Aja conforme a severidade

A severidade diz o que deve acontecer, não o quão grave é.

- **`auto`** não envolve decisão. Rode `.kiro/skills/impeccable/scripts/impeccable doctor --fix` uma vez para aplicá-los e depois relate em uma linha o que foi alterado. Não peça permissão antes e não pergunte sobre eles depois.
- **`mention`** exige que o usuário saiba, mas não que decida nada agora. Apresente cada um em uma frase com a correção oferecida.
- **`route`** exige um comando específico. Nomeie o comando e a lacuna que ele fecharia. Rode-o somente se o usuário pedir neste turno; `init` e `document` são conversas, não reparos que você executa sem supervisão.

Relate os três grupos em uma única passada. Achados não são erros e o comando não falha por causa deles.

## Etapa 3: Campos obsoletos são vinculantes

Um achado que relata um campo obsoleto (`## Register` é o atual) não é uma observação de estilo. Trate esse campo como ausente em todas as decisões daqui em diante, seja qual for o valor que ele contenha, e ofereça excluir a seção. Preservá-lo "por precaução" é a forma como um eixo aposentado continua direcionando a saída atual.

## Etapa 4: Não exagere sobre o descompasso de verdade

`design-md-drift` conta os commits nos diretórios de código-fonte visual desde a última edição do DESIGN.md. Uma contagem de commits não é uma contradição. Relate o número, diga o que ele mede e, se o usuário quiser saber se o documento está de fato errado, leia o DESIGN.md em comparação com os tokens e componentes atuais e responda a partir disso. Nunca afirme que o DESIGN.md está desatualizado porque o número é grande.

A mesma contenção se aplica a `workspace-context-inherited`. A herança é um comportamento projetado. Se um único registro de produto descreve fielmente vários apps é uma pergunta para o usuário, não um defeito a corrigir.

## Notas sobre monorepo

- `workspace-platform-native-evidence` é o achado que mais importa aqui: um workspace que contém arquivos de build nativos enquanto herda um registro da raiz que resolve para web recebe orientação web durante toda a sua vida e nunca carrega [ios.md](ios.md) ou [android.md](android.md). O reparo é um PRODUCT.md filho nesse workspace, porque um único registro herdado não comporta duas plataformas.
- `config-project-roots-match-nothing` significa que todos os globs de `projectRoots` falharam, então a raiz do repositório está silenciosamente fazendo o papel de projeto ativo. Um diretório de workspace renomeado é a causa habitual. Relate os padrões e pergunte quais diretórios eles deveriam nomear.
- `config-invalid-build-path` e `config-build-path-unset` dizem respeito a uma única chave, `buildPath` em `.impeccable/config.json` (ou no `.impeccable/config.local.json` ignorado pelo git, que prevalece para aquele desenvolvedor). Ela contém `comp` ou `code` e define se novas superfícies são construídas a partir de um comp (mockup) gerado ou diretamente em código. Um valor não lido não recai no caminho oposto, então um projeto que pretendia `code` vem construindo guiado por comp; relate o valor exato. O achado de chave não definida só dispara quando um projeto fez trabalho de direção e nunca registrou uma preferência, e a oferta só cabe nele quando há geração de imagens disponível entre as suas ferramentas. Sem geração de imagens, não há o que escolher nem o que dizer.
- Use a tabela `workspaces` para mostrar ao usuário quais apps têm contexto próprio, quais herdam e quais não têm nenhum, antes de propor qualquer mudança.

## Desativar a verificação na inicialização

`impeccable context` relata o subconjunto barato desses achados no início da sessão, limitado a uma vez por semana por projeto. Defina `"stalenessCheck": false` em `.impeccable/config.json` para silenciá-lo, ou `IMPECCABLE_NO_STALENESS_CHECK=1` para uma sessão. Este comando continua funcionando com a verificação desativada, e essa é a combinação a sugerir para um usuário que quer o relatório somente quando pedir.
