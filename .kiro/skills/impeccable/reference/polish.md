> **Contexto adicional necessário**: padrão de qualidade e restrições de entrega.

O polish é refinamento, nunca um redesign disfarçado. Preserve o mundo visual do design atual, o conteúdo, o comportamento e tudo o que estiver fora do escopo. Se o próprio conceito estiver errado, diga isso e recomende um redesign ou `bolder` em vez de introduzir uma substituição às escondidas.

Um resultado do detector é evidência de defeito, não prova de qualidade. Inspecione a experiência renderizada e o caminho real de interação.

## 1. Estabeleça o sistema

Leia o DESIGN.md e tokens, componentes compartilhados, padrões e fluxos vizinhos representativos. Se não existir um sistema formal, use as convenções coerentes do projeto.

Classifique cada desvio antes de corrigi-lo:

- **token ausente:** o sistema precisa de um valor reutilizável;
- **implementação avulsa:** um componente ou padrão compartilhado existente deveria substituí-la;
- **incompatibilidade conceitual:** o fluxo, a arquitetura da informação ou a hierarquia difere de áreas comparáveis do produto;
- **defeito local:** a implementação está simplesmente incompleta ou inconsistente.

Corrija a causa no nível correto mais restrito. Pergunte quando um princípio vinculante do sistema não puder ser inferido.

## 2. Reúna as evidências

Use você mesmo o recurso nos tamanhos representativos da superfície: desktop e mobile na web; em uma plataforma nativa (`ios` / `android` / `adaptive`), as classes de dispositivo suportadas no simulador, emulador ou hardware, capturadas conforme a seção Verifying the build da referência da plataforma. Determine:

- se o caminho está funcionalmente completo;
- o padrão de qualidade pretendido e o tempo disponível;
- restrições conhecidas ou trabalho deliberadamente inacabado;
- os estados, tamanhos de conteúdo, papéis e métodos de entrada que os usuários realmente vão encontrar.

Se existir uma critique anterior, use-a como uma das entradas:

```bash
.kiro/skills/impeccable/scripts/impeccable critique-storage latest "<resolved target>" --json
```

A saída com código 0 retorna JSON com o `body` do snapshot mais recente e uma identidade exata em `snapshot_file`. Guarde o `snapshot_file` até o fim da passada. Para um alvo que é um arquivo local, o helper compara a impressão digital exata do conteúdo atual do arquivo com a impressão digital capturada pela critique. Conteúdo inalterado, esteja ele staged, unstaged ou não rastreado, continua atual; qualquer alteração de byte, exclusão ou substituição por algo que não seja um arquivo fecha o backlog que ele identificou, preservando seu histórico de tendência, e sai com código 2. Um alvo que é uma URL não tem impressão digital local e continua atual até ser fechado explicitamente. Quando estiver atual, incorpore os achados P0/P1 relevantes do `body` e nomeie o snapshot lido. O código de saída 2 significa que não existe nenhum ou que o alvo mudou. Faça uma passada independente em qualquer caso.

## 3. Triagem

Separe os defeitos funcionais dos cosméticos e corrija nesta ordem:

1. tarefas quebradas ou bloqueadas, perda de dados, estado enganoso e caminhos inacessíveis;
2. estados ausentes de carregamento, vazio, erro, sucesso, desabilitado e permissão;
3. desvios de fluxo, hierarquia, responsividade e design system;
4. inconsistências visuais e de movimento;
5. limpeza de código e de assets.

Não aperfeiçoe um canto deixando o resto abaixo do mesmo padrão de qualidade.

## 4. Faça o polish do caminho inteiro

### Fluxo e hierarquia

- Acompanhe os modelos mentais, a terminologia, a revelação, o roteamento, o comportamento de salvamento e os padrões otimistas ou pessimistas vizinhos.
- Torne óbvios a tarefa principal e o estado atual sem achatar todos os elementos ao mesmo peso.
- Garanta que os caminhos de chegada, transição, vazio e recuperação se conectem em vez de se comportarem como telas isoladas.

### Layout e tipografia

- Alinhe ao grid e à escala de espaçamento do projeto; corrija o alinhamento óptico, além do matemático.
- Agrupe bem próximos os conteúdos relacionados e separe generosamente os grupos distintos.
- Mantenha consistente a tipografia de mesma função; teste a medida, a quebra de linha, a expansão da localização, o zoom e o carregamento de fontes.
- Verifique todas as viewports suportadas em vez de corrigir apenas a captura de tela atual.

### Cor, imagens e ícones

- Use tokens semânticos e significados de cor estáveis entre os temas.
- Verifique o contraste de texto, controles e foco em todos os estados.
- Mantenha coerentes as famílias de ícones, o traço/peso, o dimensionamento e o alinhamento óptico.
- Evite deslocamento de layout causado por imagens; use proporções corretas, fontes responsivas e texto alternativo útil.

### Interação e estado

- Todo controle precisa de comportamento adequado de padrão, hover, foco, ativo, desabilitado, carregamento, erro e sucesso.
- Preserve o foco visível do teclado, a ordem lógica de tabulação, os rótulos e alvos de toque adequados à plataforma.
- Mantenha o movimento coerente, interrompível e com bom desempenho. Não acrescente animação só para tornar o polish visível.
- Valide conteúdo longo, ausente, localizado, offline, lento e com permissão limitada onde o produto puder encontrá-lo.

### Conteúdo e código

- Mantenha consistentes a terminologia, as maiúsculas, a pontuação e a copy factual. Pergunte antes de alterar afirmações.
- Remova saídas de depuração, código morto, imports não utilizados, estilos obsoletos e duplicações criadas pelo polish.
- Substitua implementações personalizadas por componentes compartilhados onde o sistema for dono do padrão.
- Promova a tokens os valores genuinamente reutilizáveis; não crie uma abstração de sistema para uma única exceção local.

## 5. Verifique e finalize

Percorra o caminho completo novamente com mouse, teclado e toque, quando aplicável. Verifique:

- layouts mobile, intermediário e largo na web; classes de tamanho de celular e tablet em ambas as orientações suportadas no nativo;
- estados de carregamento, vazio, erro, sucesso, desabilitado, conteúdo longo e conteúdo ausente;
- zoom, contraste, foco, semântica e nomes para leitores de tela;
- erros de console, deslocamento de layout, latência de interação e carregamento de imagens em todos os casos; navegadores suportados na web; versões de SO suportadas, avisos de runtime e quadros perdidos no nativo;
- concordância com o DESIGN.md, os recursos vizinhos e o escopo do usuário.

Siga as orientações de qualidade fornecidas por `impeccable context` e pelos hooks e depois rode quaisquer outros comandos de QA relevantes. O contexto só solicita uma varredura manual quando nenhum detector automático estiver ativo; nunca acrescente outra passada de detector. Corrija os defeitos reais e documente apenas exceções intencionais e restritas. Uma varredura limpa não substitui o julgamento visual.

Finalize com um diff do código-fonte: remova alterações acidentais, código órfão, valores redundantes e artefatos temporários. Entregue apenas quando o recurso estiver funcionalmente completo e com acabamento consistente em todo o caminho.

Quando esta passada resolver todos os Priority Issues que tirou de um snapshot, feche esse snapshot:

```bash
.kiro/skills/impeccable/scripts/impeccable critique-storage close "<resolved target>" "<snapshot_file returned by latest>"
```

Isso fecha apenas o snapshot que esta passada de fato processou; se uma critique mais recente tiver chegado nesse meio-tempo, o backlog dela continua ativo. Não feche quando nenhum snapshot tiver sido lido, quando o `snapshot_file` não tiver sido guardado ou quando ainda restarem Priority Issues.
