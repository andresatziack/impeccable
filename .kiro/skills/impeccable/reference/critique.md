### Propósito

Resolva um alvo estável, execute duas avaliações independentes, sintetize uma crítica de design, persista um snapshot e pergunte ao usuário o que melhorar em seguida. A resposta no chat é a entrega principal; o snapshot é um arquivo histórico dessa execução.

### Invariantes rígidas

- A Avaliação A (revisão de design) e a Avaliação B (evidências do detector/navegador) são ambas obrigatórias.
- As Avaliações A e B DEVEM rodar como dois subagentes isolados sempre que uma ferramenta de subagente/Task estiver exposta. Rodá-las inline neste contexto é "possível", mas NÃO é permitido; isso é uma execução degradada. Inline é permitido SOMENTE quando não existe nenhuma ferramenta de subagente (ou o usuário recusou, em harnesses que perguntam).
- Se você degradar por qualquer motivo, a primeira linha do relatório DEVE ser um banner: `⚠️ DEGRADED: single-context (<reason>)`. Uma crítica degradada silenciosa é uma crítica falha.
- A Avaliação A precisa terminar antes que os achados do detector entrem no contexto de síntese do agente pai. A saída do detector é determinística, mas ainda assim ancora o julgamento.
- Um detector pulado é uma execução de crítica falha, a menos que `impeccable detect` esteja ausente ou trave após uma tentativa real.
- Alvos visualizáveis exigem inspeção no navegador quando ela estiver disponível.
- Qualquer servidor local iniciado apenas para a visualização da crítica deve rodar em segundo plano, ter um método de parada registrado e ser parado antes do relatório final, a menos que o usuário peça para mantê-lo.
- Não afirme que existe uma sobreposição visível ao usuário a menos que a injeção do script tenha dado certo e o detector tenha rodado na página.
- A pergunta é a ÚLTIMA coisa na resposta. Escreva o relatório inteiro primeiro e depois pergunte; nada vem depois da pergunta. A prosa emitida depois de uma pergunta estruturada fica retida até o usuário respondê-la, então um relatório escrito depois da pergunta parece como se a crítica nunca tivesse rodado.
- Uma execução que termina sem as perguntas direcionadas e sem uma linha literal `Questions skipped: <reason>` é uma execução incompleta. O relatório não é o fim; o fechamento é.

### Configuração

1. **Resolva o alvo** para um caminho de arquivo ou URL concreto. Prefira um caminho de código-fonte a uma URL de servidor de desenvolvimento quando ambos identificam a mesma superfície; portas mudam, caminhos não.
   - "a página inicial" -> `site/pages/index.astro` ou `index.html`
   - "o modal de configurações" -> o arquivo do componente principal
   - "esta página" -> a URL atual ou o arquivo-fonte
2. **Confirme que o alvo gera um slug limpo**:
   ```bash
   .kiro/skills/impeccable/scripts/impeccable critique-storage slug "<resolved-path-or-url>"
   ```
   Todos os comandos posteriores também aceitam o alvo resolvido diretamente e derivam internamente o mesmo slug; nunca escreva um slug à mão. Se isso sair com código diferente de zero, pule a persistência e a tendência nesta execução, mas continue a crítica.
3. **Leia `.impeccable/critique/ignore.md`** se existir. Descarte silenciosamente os achados correspondentes; essa é a única entrada de execuções anteriores que a crítica consome.

### Orquestração das avaliações

Delegue a Avaliação A e a Avaliação B a subagentes separados. Eles não podem ver a saída um do outro. Não mostre achados ao usuário até a síntese.

Portão de subagentes (todos os harnesses):
- A menos que um portão específico de harness abaixo substitua este, crie A e B como dois subagentes isolados e paralelos sempre que uma ferramenta de subagente/Task estiver exposta. Esse é o padrão e é obrigatório; não os rode inline só porque é mais rápido.
- "Indisponível" significa exatamente uma coisa: nenhuma ferramenta de subagente/Task está exposta nesta sessão (ou, em harnesses que perguntam, o usuário recusou). Não significa inconveniente.
- Se, e somente se, os subagentes estiverem indisponíveis, recorra à execução sequencial: termine e registre a Avaliação A, depois rode a Avaliação B, depois sintetize e emita o banner de degradação.
- Seja qual for o caminho escolhido, declare-o no cabeçalho do relatório (veja Procedência no cabeçalho do relatório). Pular subagentes sem o banner é a falha mais comum deste comando.

Se houver automação de navegador disponível, cada avaliação cria sua própria aba nova. Nunca reutilize uma aba existente, mesmo que ela já esteja na URL certa.

### Avaliação A: Revisão de design

Leia os arquivos-fonte relevantes e inspecione visualmente a página ao vivo quando houver automação de navegador disponível. Pense como um diretor de design.

Avalie:
- **Especificidade do design**: a composição, a interação e a linguagem visual estão fundamentadas neste produto, ou um produto não relacionado poderia usá-las sem mudanças? Faça esse julgamento antes de ver a saída do detector.
- **Design holístico**: hierarquia, arquitetura da informação, adequação emocional, descoberta, composição, tipografia, cor, acessibilidade, estados, copy e casos extremos.
- **Carga cognitiva**: consulte a seção [Avaliação de carga cognitiva](#cognitive-load-assessment) abaixo; relate as falhas do checklist e os pontos de decisão com >4 opções visíveis.
- **Jornada emocional**: regra do pico-fim, vales emocionais, segurança em momentos de alto risco.
- **Heurísticas de Nielsen**: consulte a seção [Guia de pontuação das heurísticas](#heuristics-scoring-guide) abaixo; pontue todas as 10 heurísticas de 0 a 4, marcando como `n/a` qualquer heurística que a regra de aplicabilidade por modo permita, em vez de forçar um número.

Retorne: veredito de especificidade do design, pontuações das heurísticas, carga cognitiva, jornada emocional, 2-3 pontos fortes, 3-5 problemas prioritários, sinais de alerta das personas, observações menores e perguntas provocativas.

### Avaliação B: Detector + evidências do navegador

Rode o detector incluído e a visualização no navegador como evidência. A Avaliação B é obrigatória e precisa permanecer isolada da Avaliação A até que ambas estejam concluídas.

Varredura via CLI:
```bash
.kiro/skills/impeccable/scripts/impeccable detect --json [target]
```

- Passe arquivos/diretórios de marcação como `[target]`; não passe arquivos só de CSS.
- Para URLs, pule a varredura via CLI e use a visualização no navegador.
- Para árvores muito grandes (500+ arquivos varríveis), restrinja o escopo ou pergunte.
- Código de saída 0 = limpo; 2 = achados.
- Se o ponto de entrada do detector estiver ausente ou falhar ao carregar, relate que a varredura determinística está indisponível e continue com a revisão via navegador/manual.

A visualização no navegador é obrigatória para um alvo visualizável quando houver automação de navegador disponível. Use uma URL localhost de desenvolvimento/estática para arquivos locais; evite `file://`, a menos que o navegador disponível suporte explicitamente esse fluxo. Fluxo da sobreposição:

1. Crie uma aba nova e navegue. Prefira o caminho de captura de tela nativo do harness/canvas do navegador antes de escrever à mão um script Playwright/Puppeteer; só recorra a um script personalizado quando nenhuma ferramenta de navegador nativa estiver exposta.
2. Faça uma verificação prévia da injeção mutável definindo `document.title` e anexando uma tag `<script>`. APIs de avaliação somente leitura não contam.
3. Se a mutação estiver indisponível, pule o servidor live, a apresentação no navegador e a injeção; relate o sinal de fallback.
4. Se a mutação estiver disponível, inicie `.kiro/skills/impeccable/scripts/impeccable live-server --background`, apresente o navegador se houver suporte, rotule como `[Human]`, role até o topo, injete `http://localhost:PORT/detect.js`, aguarde 2-3 segundos, leia as mensagens de console `impeccable` e então pare o servidor live.
5. Para alvos com várias visualizações, injete em 3-5 páginas representativas.

Retorne: JSON/contagens dos achados da CLI, achados do console do navegador, se aplicável, falsos positivos e etapas de navegador puladas/falhas com motivos concretos.

Depois que a Avaliação B retornar achados utilizáveis da CLI, reutilize-os. Não rode `impeccable detect` novamente no agente pai, a menos que a Avaliação B tenha falhado, sido truncada ou omitido a contagem, os nomes das regras ou as localizações dos arquivos.

### Gere o relatório de crítica combinado

Sintetize as duas avaliações em um único relatório. NÃO apenas as concatene. Entrelace os achados, apontando onde a revisão do LLM e o detector concordam, onde o detector pegou problemas que o LLM deixou passar e onde os achados do detector são falsos positivos.

A resposta no chat é a entrega principal voltada ao usuário. Apresente no chat a crítica estruturada completa abaixo; não a substitua por um resumo e um link. O snapshot persistido é um arquivo histórico dessa execução.

Estruture seu feedback como faria um diretor de design:

#### Procedência no cabeçalho do relatório

A primeira linha do relatório DEVE declarar como as avaliações foram executadas, para que uma execução degradada nunca seja silenciosa:
- Dois agentes: `Method: dual-agent (A: <agent-id> · B: <agent-id>)`
- Degradada: `⚠️ DEGRADED: single-context (<reason, e.g. no sub-agent tool exposed>)`

#### Pontuação de saúde do design
> *Consulte a seção [Guia de pontuação das heurísticas](#heuristics-scoring-guide) abaixo.*

Apresente as pontuações das 10 heurísticas de Nielsen como uma tabela:

| # | Heurística | Pontuação | Problema principal |
|---|-----------|-------|-----------|
| 1 | Visibilidade do status do sistema | ? | [achado específico ou "n/a" se estiver sólido] |
| 2 | Correspondência entre o sistema e o mundo real | ? | |
| 3 | Controle e liberdade do usuário | ? | |
| 4 | Consistência e padrões | ? | |
| 5 | Prevenção de erros | ? | |
| 6 | Reconhecimento em vez de memorização | ? | |
| 7 | Flexibilidade e eficiência | ? | |
| 8 | Design estético e minimalista | ? | |
| 9 | Recuperação de erros | ? | |
| 10 | Ajuda e documentação | ? | |
| **Total** | | **??/[máximo aplicável]** | **[Faixa de classificação]** |

O máximo aplicável é 4 vezes o número de heurísticas que você de fato pontuou: **/40** quando todas as dez se aplicam, **/32** quando duas são `n/a`. Nunca imprima `/40` sobre um conjunto parcial.

Seja honesto nas pontuações. Um 4 significa genuinamente excelente. A maioria das interfaces reais pontua entre 20 e 32 de 40.

**Aplicabilidade por modo**: as heurísticas 7 (Flexibilidade e eficiência) e 10 (Ajuda e documentação) podem ser pontuadas como `n/a` em superfícies Persuade (persuadir) e Experience (experiência) (landing pages, campanhas, portfólios, conjuntos de obras), assim como qualquer outra heurística que genuinamente não possa se aplicar à superfície em revisão. Escreva `n/a` na célula de Pontuação com um motivo de uma linha e renormalize o total para o máximo aplicável (por exemplo, **24/32** quando duas heurísticas são n/a), para que a faixa de classificação permaneça proporcional. O snapshot persistido precisa registrar o máximo aplicável e quais heurísticas foram pontuadas como n/a.

#### Veredito de especificidade do design

**Comece aqui.** O resultado parece feito sob medida para este produto ou intercambiável dentro da categoria?

**Avaliação do LLM**: sua avaliação não ancorada da especificidade do design. Cubra a coerência geral, a mesmice estrutural, as escolhas intercambiáveis dentro da categoria e as oportunidades perdidas de dar caráter ao produto.

**Varredura determinística**: resuma o que o detector automatizado encontrou, com contagens e localizações nos arquivos. Aponte quaisquer problemas adicionais que o detector pegou e você deixou passar, e sinalize quaisquer falsos positivos.

**Sobreposições visuais** (se a injeção deu certo): diga ao usuário que as sobreposições agora estão visíveis na aba **[Human]** do navegador dele, destacando os problemas detectados. Resuma o que a saída do console relatou. Se a visualização no navegador foi tentada, mas a injeção falhou, diga que não há nenhuma sobreposição confiável visível ao usuário e relate o sinal de fallback no lugar.

#### Impressão geral
Uma breve reação instintiva: o que funciona, o que não funciona e a maior oportunidade isolada.

#### O que está funcionando
Destaque 2-3 coisas bem feitas. Seja específico sobre por que elas funcionam.

#### Problemas prioritários
Os 3-5 problemas de design de maior impacto, ordenados por importância.

Para cada problema, marque com uma **severidade P0-P3** (veja [Severidade dos problemas abaixo](#issue-severity-p0p3) para as definições):
- **[P?] O quê**: nomeie o problema com clareza
- **Por que importa**: como isso prejudica os usuários ou compromete os objetivos
- **Correção**: o que fazer a respeito (seja concreto)
- **Comando sugerido**: qual comando poderia resolver isso (dentre: /impeccable adapt, /impeccable animate, /impeccable audit, /impeccable bolder, /impeccable clarify, /impeccable colorize, /impeccable critique, /impeccable delight, /impeccable distill, /impeccable document, /impeccable harden, /impeccable layout, /impeccable onboard, /impeccable optimize, /impeccable overdrive, /impeccable polish, /impeccable quieter, /impeccable shape, /impeccable typeset)

#### Sinais de alerta das personas
> *Consulte a [referência de Personas](#persona-based-design-testing) abaixo.*

Selecione automaticamente 2-3 personas mais relevantes para este tipo de interface (use a tabela de seleção da referência). Se `.kiro/settings.json` contiver uma seção `## Design Context` vinda de `impeccable init`, gere também 1-2 personas específicas do projeto a partir das informações de público/marca.

Para cada persona selecionada, percorra a ação principal do usuário e liste os sinais de alerta específicos encontrados:

**Alex (usuário avançado)**: nenhum atalho de teclado detectado. O formulário exige 8 cliques para a ação principal. Onboarding forçado em modal. Alto risco de abandono.

**Jordan (iniciante)**: navegação só com ícones na barra lateral. Jargão técnico nas mensagens de erro ("404 Not Found"). Nenhuma ajuda visível. Vai abandonar no passo 2.

Seja específico. Nomeie os elementos e as interações exatos que falham para cada persona. Não escreva descrições genéricas de personas; escreva o que quebrou para elas.

#### Observações menores
Notas rápidas sobre problemas menores que vale a pena resolver.

#### Perguntas a considerar
Perguntas provocativas que podem destravar soluções melhores:
- "E se a ação principal fosse mais proeminente?"
- "Isto precisa parecer tão complexo?"
- "Como seria uma versão confiante disto?"

**Lembre-se**:
- Seja direto. Feedback vago desperdiça o tempo de todos.
- Seja específico. "O botão de enviar", não "alguns elementos".
- Diga o que está errado E por que isso importa para os usuários.
- Dê sugestões concretas. Corte totalmente o "considere explorar...".
- Priorize sem piedade. Se tudo é importante, nada é.
- Não suavize as críticas. Desenvolvedores precisam de feedback honesto para entregar um ótimo design.

### Entregue o relatório

Escreva agora o relatório completo na resposta do chat, antes de qualquer trabalho de persistência. Esta é a entrega; tudo abaixo disto é contabilidade.

Faça isso primeiro porque a alternativa é a forma mais comum de este comando falhar: o relatório é composto uma única vez, direto no heredoc de persistência, e a execução termina com um arquivo histórico perfeito que ninguém leu. Compor o relatório em um arquivo não é entregá-lo. Se o relatório existir apenas em `.impeccable/critique/`, a execução não produziu nada.

A persistência não é o fim da execução. Depois dela, a resposta continua com a linha de tendência e o fechamento.

### Persista o snapshot

Assim que o relatório acima estiver finalizado, grave-o em `.impeccable/critique/` para que o usuário possa consultá-lo depois e para que `/impeccable polish` possa pegar os problemas prioritários sem copiar e colar.

Pule este passo se o slug da Configuração for nulo (alvo vago ou no nível da raiz).

1. **Escreva o corpo em um arquivo temporário** para poder passá-lo por pipe ao auxiliar. Use o relatório de crítica completo (tabela de heurísticas, veredito de especificidade do design, problemas prioritários, sinais de alerta das personas, observações menores e perguntas), mas pare antes das seções "Pergunte ao usuário" / "Ações recomendadas" que vêm depois.

   Isto é uma cópia do relatório que você já entregou acima, para que comandos posteriores o leiam. Não é a entrega. Se você se pegar compondo o relatório pela primeira vez dentro deste heredoc, você pulou o passo Entregue o relatório; volte e envie-o.

2. **Passe os metadados estruturados** por meio de `IMPECCABLE_CRITIQUE_META` (JSON) e então rode o comando de gravação:
   ```bash
   IMPECCABLE_CRITIQUE_META='{"target":"<user phrasing>","total_score":<n>,"max_score":<n>,"na_heuristics":"<comma-separated numbers, or empty>","p0_count":<n>,"p1_count":<n>}' \
     .kiro/skills/impeccable/scripts/impeccable critique-storage write "<resolved target>" <body-file>
   ```
   `max_score` é o máximo aplicável da tabela de heurísticas (40 quando todas as heurísticas se aplicaram), para que uma execução posterior consiga distinguir um total renormalizado de um total completo. Para um alvo que é arquivo local, o auxiliar também registra uma impressão digital exata do conteúdo, para que o polish consiga distinguir os bytes avaliados de edições posteriores sem depender do estado do Git nem de timestamps. O auxiliar imprime o caminho absoluto que gravou. Deixe esse arquivo no disco. O polish o fecha; esta execução não.

3. **Apague o arquivo temporário do corpo** depois que a tentativa de gravação terminar, quer a gravação tenha dado certo, quer tenha falhado. Se a exclusão falhar, mencione brevemente `temp-file cleanup failed: <reason>` na saída final, mas não bloqueie a crítica.

4. **Leia a tendência** para ter contexto:
   ```bash
   .kiro/skills/impeccable/scripts/impeccable critique-storage trend "<resolved target>" 5
   ```
   Isso retorna um array JSON com as últimas 5 entradas de frontmatter (incluindo a que você acabou de gravar).

5. **Acrescente uma única linha à saída visível ao usuário**, depois do relatório e antes das perguntas:

   > **Tendência de `<slug>` (últimas 5 execuções): 24 → 28 → 32 → 29 → 32 (de 40)**
   > Gravado `.impeccable/critique/<filename>`.

   Leia `max_score` em cada entrada da tendência. Quando todas as entradas compartilham um mesmo máximo, informe-o uma única vez, como acima. Quando forem diferentes, imprima cada pontuação com seu próprio denominador (`24/32 → 30/40`) e observe que as execuções pontuaram conjuntos diferentes de heurísticas, então a linha não é uma comparação equivalente. Trate um `max_score` ausente em uma entrada mais antiga como 40.

   Se esta for a primeira execução para o slug, a tendência é apenas uma pontuação; diga isso: "Primeira execução para este alvo, ainda sem tendência."

6. **Feche a execução.** Vá para Pergunte ao usuário abaixo e emita as perguntas, ou a linha `Questions skipped: <reason>` quando a contagem permitir. A execução não está completa até você fazer isso. A persistência é contabilidade e a limpeza não é um encerramento; parar aqui deixa o usuário com um relatório e nenhum caminho adiante, e deixa o `/impeccable polish` sem prioridades para herdar.

Isto é do tipo "dispare e esqueça". Não mostre ao usuário a saída JSON do auxiliar; apenas a linha de tendência legível e o caminho gravado. Falhas aqui não devem bloquear o restante do fluxo; imprima o erro e siga em frente.

### Pergunte ao usuário

**Depois de apresentar os achados**, faça perguntas direcionadas com base no que foi de fato encontrado. Pergunte diretamente ao usuário para esclarecer o que você não consegue inferir. Essas respostas vão moldar o plano de ação.

Pergunte na mesma mensagem que traz o relatório, com o relatório escrito primeiro e a pergunta por último. Não divida os dois em turnos diferentes: um turno que termina no relatório é um turno que termina, e as perguntas nunca chegam. O que importa é a ordem dentro da mensagem, porque a prosa emitida depois de uma pergunta estruturada fica retida até o usuário responder.

Faça perguntas nesta linha (adapte aos achados específicos; NÃO faça perguntas genéricas):

1. **Direção de prioridade**: com base nos problemas encontrados, pergunte qual categoria mais importa para o usuário agora. Por exemplo: "Encontrei problemas de hierarquia visual, uso de cores e excesso de informação. Qual área devemos atacar primeiro?" Ofereça as 2-3 principais categorias de problemas como opções.

2. **Intenção de design**: se a crítica encontrou um descompasso de tom, pergunte se ele foi intencional. Por exemplo: "A interface parece clínica e corporativa. Esse é o tom pretendido ou ela deveria parecer mais calorosa/ousada/divertida?" Ofereça 2-3 direções de tom como opções, com base no que corrigiria os problemas encontrados.

3. **Escopo**: pergunte quanto o usuário quer encarar. Por exemplo: "Encontrei N problemas. Quer resolver todos ou focar nos 3 principais?" Ofereça opções de escopo como "Só os 3 principais", "Todos os problemas", "Só os problemas críticos".

4. **Restrições** (opcional; pergunte só se for relevante): se os achados tocam muitas áreas, pergunte se algo está fora dos limites. Por exemplo: "Alguma seção deve permanecer como está?" Isso evita que o plano mexa em coisas que o usuário considera prontas.

**Regras para as perguntas**:
- Toda pergunta precisa referenciar achados específicos do relatório. Nunca faça perguntas genéricas do tipo "quem é o seu público?".
- Limite-se a no máximo 2-4 perguntas. Respeite o tempo do usuário.
- Ofereça opções concretas, não prompts abertos.
- Pular é permitido somente quando o relatório listou **menos de 3 Problemas prioritários**. Conte-os; não julgue os achados como "simples" pela intuição. Com 3 ou mais, as perguntas são obrigatórias.

**Portão da pergunta final.** A resposta visível ao usuário precisa incluir as perguntas direcionadas ou trazer a linha literal `Questions skipped: <reason>` informando a contagem que permitiu pular. Cada pergunta precisa incluir 2-3 opções de resposta concretas ligadas aos achados reais da crítica. Não termine apenas com perguntas abertas e não termine sem nenhuma das duas coisas: parar depois do relatório, sem ter perguntado nada e sem imprimir a linha de pulo, é a forma mais comum de este comando falhar.

### Ações recomendadas

**Depois de receber as respostas do usuário**, apresente um resumo de ações priorizado que reflita as prioridades e o escopo do usuário definidos em Pergunte ao usuário.

#### Resumo de ações

Liste os comandos recomendados em ordem de prioridade, com base nas respostas do usuário:

1. **`/command-name`**: breve descrição do que corrigir (contexto específico dos achados da crítica)
2. **`/command-name`**: breve descrição (contexto específico)
...

**Regras para as recomendações**:
- Recomende somente comandos dentre: /impeccable adapt, /impeccable animate, /impeccable audit, /impeccable bolder, /impeccable clarify, /impeccable colorize, /impeccable critique, /impeccable delight, /impeccable distill, /impeccable document, /impeccable harden, /impeccable layout, /impeccable onboard, /impeccable optimize, /impeccable overdrive, /impeccable polish, /impeccable quieter, /impeccable shape, /impeccable typeset
- Ordene primeiro pelas prioridades declaradas pelo usuário e depois pelo impacto
- A descrição de cada item deve trazer contexto suficiente para que o comando saiba no que focar
- Mapeie cada Problema prioritário para o comando apropriado
- Pule comandos que não resolveriam nenhum problema
- Se o usuário escolheu um escopo limitado, inclua apenas itens dentro desse escopo
- Se o usuário marcou áreas como fora dos limites, exclua comandos que mexeriam nessas áreas
- Termine com `/impeccable polish` como passo final se alguma correção foi recomendada

Depois de apresentar o resumo, diga ao usuário:

> Você pode me pedir para rodar esses comandos um de cada vez, todos de uma vez ou na ordem que preferir.
>
> Rode `/impeccable critique` novamente depois das correções para ver sua pontuação melhorar.

---

## Material de referência

As seções abaixo eram antes arquivos de referência separados (`cognitive-load.md`, `heuristics-scoring.md`, `personas.md`). Agora elas ficam inline, para que o fluxo de crítica tenha todo o seu contexto aprofundado em um só lugar.

<a id="cognitive-load-assessment"></a>
### Avaliação de carga cognitiva

Carga cognitiva é o esforço mental total necessário para usar uma interface. Usuários sobrecarregados cometem erros, se frustram e vão embora. Esta referência ajuda a identificar e corrigir a sobrecarga cognitiva.

---

#### Três tipos de carga cognitiva

##### Carga intrínseca: a tarefa em si
Complexidade inerente ao que o usuário está tentando fazer. Você não consegue eliminá-la, mas consegue estruturá-la.

**Gerencie-a**:
- Dividindo tarefas complexas em passos distintos
- Oferecendo apoio (templates, valores padrão, exemplos)
- Com divulgação progressiva: mostre o que é necessário agora, esconda o resto
- Agrupando decisões relacionadas

##### Carga estranha: design ruim
Esforço mental causado por escolhas de design ruins. **Elimine-a sem piedade.** É puro desperdício.

**Fontes comuns**:
- Navegação confusa que exige mapeamento mental
- Rótulos pouco claros que forçam o usuário a adivinhar o significado
- Poluição visual competindo por atenção
- Padrões inconsistentes que impedem o aprendizado
- Passos desnecessários entre a intenção do usuário e o resultado

##### Carga germânica: esforço de aprendizado
Esforço mental gasto construindo entendimento. Esta é uma carga cognitiva *boa*; ela leva ao domínio.

**Apoie-a com**:
- Divulgação progressiva que revela a complexidade aos poucos
- Padrões consistentes que recompensam o aprendizado
- Feedback que confirma o entendimento correto
- Onboarding que ensina pela ação, não por paredes de texto

---

#### Checklist de carga cognitiva

Avalie a interface com base nestes 8 itens:

- [ ] **Foco único**: o usuário consegue concluir a tarefa principal sem ser distraído por elementos concorrentes?
- [ ] **Agrupamento em blocos**: a informação é apresentada em grupos digeríveis (≤4 itens por grupo)?
- [ ] **Agrupamento**: itens relacionados estão agrupados visualmente (proximidade, bordas, fundo compartilhado)?
- [ ] **Hierarquia visual**: fica imediatamente claro o que é mais importante na tela?
- [ ] **Uma coisa de cada vez**: o usuário consegue focar em uma única decisão antes de passar para a próxima?
- [ ] **Escolhas mínimas**: as decisões são simplificadas (≤4 opções visíveis em qualquer ponto de decisão)?
- [ ] **Memória de trabalho**: o usuário precisa lembrar informações de uma tela anterior para agir na atual?
- [ ] **Divulgação progressiva**: a complexidade só é revelada quando o usuário precisa dela?

**Pontuação**: conte os itens que falharam. 0–1 falha = carga cognitiva baixa (bom). 2–3 = moderada (resolva em breve). 4+ = carga cognitiva alta (correção crítica necessária).

---

#### A regra da memória de trabalho

**Humanos conseguem manter ≤4 itens na memória de trabalho ao mesmo tempo** (Lei de Miller revisada por Cowan, 2001).

Em qualquer ponto de decisão, conte o número de opções, ações ou informações distintas que o usuário precisa considerar simultaneamente:
- **≤4 itens**: dentro dos limites da memória de trabalho, administrável
- **5–7 itens**: forçando o limite; considere agrupar ou usar divulgação progressiva
- **8+ itens**: sobrecarregado; os usuários vão pular, clicar errado ou abandonar

**Aplicações práticas**:
- Botões de ação: 1 principal, 1–2 secundários, agrupe o resto em um menu
- Menus de navegação: ≤5 itens de nível superior (agrupe o resto em categorias claras)
- Artigos longos: um único caminho de leitura; reúna os links relacionados em um único bloco no final, em vez de espalhá-los no meio do fluxo
- Barras laterais de documentação: ≤4 opções irmãs visíveis por nível antes de o agrupamento entrar em ação
- Índices de portfólios e galerias: uma decisão por tela (qual peça abrir), não controles de filtro, ordenação e tags todos de uma vez

---

#### Violações comuns de carga cognitiva

##### 1. A parede de opções
**Problema**: apresentar 10+ escolhas de uma vez, sem hierarquia.
**Correção**: agrupe em categorias, destaque a recomendada, use divulgação progressiva.

##### 2. A ponte de memória
**Problema**: o usuário precisa lembrar informações do passo 1 para concluir o passo 3.
**Correção**: mantenha o contexto relevante visível ou repita-o onde for necessário.

##### 3. A navegação escondida
**Problema**: o usuário precisa construir um mapa mental de onde as coisas estão.
**Correção**: sempre mostre a localização atual (breadcrumbs, estados ativos, indicadores de progresso).

##### 4. A barreira do jargão
**Problema**: linguagem técnica ou de domínio força um esforço de tradução.
**Correção**: use linguagem simples. Se termos de domínio forem inevitáveis, defina-os inline.

##### 5. O piso de ruído visual
**Problema**: todo elemento tem o mesmo peso visual; nada se destaca.
**Correção**: estabeleça uma hierarquia clara: um elemento principal, 2–3 secundários, todo o resto atenuado.

##### 6. O padrão inconsistente
**Problema**: ações semelhantes funcionam de forma diferente em lugares diferentes.
**Correção**: padronize os padrões de interação. Mesmo tipo de ação = mesmo tipo de UI.

##### 7. A exigência de multitarefa
**Problema**: a interface exige processar várias entradas simultâneas (ler + decidir + navegar).
**Correção**: sequencie os passos. Deixe o usuário fazer uma coisa de cada vez.

##### 8. A troca de contexto
**Problema**: o usuário precisa pular entre telas/abas/modais para reunir informações para uma única decisão.
**Correção**: coloque juntas as informações necessárias para cada decisão. Reduza o vaivém.

---

<a id="heuristics-scoring-guide"></a>
### Guia de pontuação das heurísticas

Pontue cada uma das 10 Heurísticas de Usabilidade de Nielsen em uma escala de 0–4. Seja honesto: um 4 significa genuinamente excelente, não "bom o bastante".

#### As 10 heurísticas de Nielsen

##### 1. Visibilidade do status do sistema

Mantenha os usuários informados sobre o que está acontecendo por meio de feedback oportuno e adequado.

**Verifique**:
- Indicadores de carregamento durante operações assíncronas
- Confirmação das ações do usuário (salvar, enviar, excluir)
- Indicadores de progresso para processos com várias etapas
- Localização atual na navegação (breadcrumbs, estados ativos)
- Feedback de validação de formulários (inline, não apenas ao enviar)

**Pontuação**:
| Pontuação | Critérios |
|-------|----------|
| 0 | Nenhum feedback; o usuário fica adivinhando o que aconteceu |
| 1 | Feedback raro; a maioria das ações não produz resposta visível |
| 2 | Parcial; alguns estados são comunicados, ainda restam lacunas grandes |
| 3 | Bom; a maioria das operações dá feedback claro, pequenas lacunas |
| 4 | Excelente; toda ação é confirmada, o progresso está sempre visível |

##### 2. Correspondência entre o sistema e o mundo real

Fale a língua do usuário. Siga as convenções do mundo real. A informação aparece em uma ordem natural e lógica.

**Verifique**:
- Terminologia familiar (sem jargão não explicado)
- Ordem lógica das informações, correspondendo às expectativas do usuário
- Ícones e metáforas reconhecíveis
- Linguagem adequada ao domínio para o público-alvo
- Fluxo de leitura natural (prioridade da esquerda para a direita, de cima para baixo)

**Pontuação**:
| Pontuação | Critérios |
|-------|----------|
| 0 | Puro jargão técnico, estranho aos usuários |
| 1 | Majoritariamente confuso; exige conhecimento do domínio para navegar |
| 2 | Misto; alguma linguagem simples, algum jargão escapa |
| 3 | Majoritariamente natural; um termo ou outro precisa de contexto |
| 4 | Fala a língua do usuário com fluência do início ao fim |

##### 3. Controle e liberdade do usuário

Os usuários precisam de uma "saída de emergência" clara de estados indesejados, sem diálogos prolongados.

**Verifique**:
- Funcionalidade de desfazer/refazer
- Botões de cancelar em formulários e modais
- Navegação clara de volta a um lugar seguro (início, anterior)
- Forma fácil de limpar filtros, buscas e seleções
- Saída de processos longos ou com várias etapas

**Pontuação**:
| Pontuação | Critérios |
|-------|----------|
| 0 | Os usuários ficam presos; não há saída sem recarregar a página |
| 1 | Saídas difíceis; é preciso encontrar caminhos obscuros para escapar |
| 2 | Algumas saídas; os fluxos principais têm saída, os casos extremos não |
| 3 | Bom controle; os usuários conseguem sair e desfazer a maioria das ações |
| 4 | Controle total; desfazer, cancelar, voltar e sair em todo lugar |

##### 4. Consistência e padrões

Os usuários não deveriam ter que se perguntar se palavras, situações ou ações diferentes significam a mesma coisa.

**Verifique**:
- Terminologia consistente em toda a interface
- As mesmas ações produzem os mesmos resultados em todo lugar
- Convenções da plataforma seguidas (padrões de UI comuns)
- Consistência visual (cores, tipografia, espaçamento, componentes)
- Padrões de interação consistentes (mesmo gesto = mesmo comportamento)

**Pontuação**:
| Pontuação | Critérios |
|-------|----------|
| 0 | Inconsistente em todo lugar; parece vários produtos diferentes costurados |
| 1 | Muitas inconsistências; coisas semelhantes parecem/se comportam de forma diferente |
| 2 | Parcialmente consistente; os fluxos principais batem, os detalhes divergem |
| 3 | Majoritariamente consistente; desvios ocasionais, nada confuso |
| 4 | Totalmente consistente; sistema coeso, comportamento previsível |

##### 5. Prevenção de erros

Melhor do que boas mensagens de erro é um design que evita os problemas logo de início.

**Verifique**:
- Confirmação antes de ações destrutivas (excluir, sobrescrever)
- Restrições que impedem entradas inválidas (seletores de data, dropdowns)
- Valores padrão inteligentes que reduzem erros
- Rótulos claros que evitam mal-entendidos
- Salvamento automático e recuperação de rascunhos

**Pontuação**:
| Pontuação | Critérios |
|-------|----------|
| 0 | Erros fáceis de cometer; nenhuma proteção em lugar nenhum |
| 1 | Poucas salvaguardas; algumas entradas validadas, a maioria não |
| 2 | Prevenção parcial; erros comuns são pegos, casos extremos escapam |
| 3 | Boa prevenção; a maioria dos caminhos de erro é bloqueada de forma proativa |
| 4 | Excelente; erros quase impossíveis graças a restrições inteligentes |

##### 6. Reconhecimento em vez de memorização

Minimize a carga de memória. Torne objetos, ações e opções visíveis ou fáceis de recuperar.

**Verifique**:
- Opções visíveis (não enterradas em menus ocultos)
- Ajuda contextual quando necessário (tooltips, dicas inline)
- Itens recentes e histórico
- Autocompletar e sugestões
- Rótulos nos ícones (não navegação só com ícones)

**Pontuação**:
| Pontuação | Critérios |
|-------|----------|
| 0 | Muita memorização; os usuários precisam lembrar caminhos e comandos |
| 1 | Majoritariamente memorização; muitos recursos ocultos, poucas pistas visíveis |
| 2 | Alguns auxílios; ações principais visíveis, recursos secundários ocultos |
| 3 | Bom reconhecimento; a maioria das coisas é descobrível, poucas exigências de memória |
| 4 | Tudo descobrível; os usuários nunca precisam memorizar |

##### 7. Flexibilidade e eficiência de uso

Aceleradores, invisíveis para novatos, agilizam a interação de especialistas.

**Verifique**:
- Atalhos de teclado para ações comuns
- Elementos da interface personalizáveis
- Itens recentes e favoritos
- Ações em massa/em lote
- Recursos para usuários avançados que não complicam o básico

**Pontuação**:
| Pontuação | Critérios |
|-------|----------|
| 0 | Um único caminho rígido; sem atalhos nem alternativas |
| 1 | Flexibilidade limitada; poucas alternativas ao caminho principal |
| 2 | Alguns atalhos; suporte básico a teclado, ações em massa limitadas |
| 3 | Bons aceleradores; navegação por teclado, alguma personalização |
| 4 | Altamente flexível; múltiplos caminhos, recursos avançados, personalizável |

##### 8. Design estético e minimalista

As interfaces não devem conter informações irrelevantes ou raramente necessárias. Todo elemento deve servir a um propósito.

**Verifique**:
- Apenas as informações necessárias visíveis em cada etapa
- Hierarquia visual clara direcionando a atenção
- Uso intencional de cor e ênfase
- Nenhuma poluição decorativa competindo por atenção
- Layouts focados e despoluídos

**Pontuação**:
| Pontuação | Critérios |
|-------|----------|
| 0 | Esmagador; tudo compete igualmente por atenção |
| 1 | Poluído; ruído demais, difícil encontrar o que importa |
| 2 | Alguma poluição; conteúdo principal claro, periferia ruidosa |
| 3 | Majoritariamente limpo; design focado, pequeno ruído visual |
| 4 | Perfeitamente minimalista; cada elemento merece seu pixel |

##### 9. Ajude os usuários a reconhecer, diagnosticar e se recuperar de erros

As mensagens de erro devem usar linguagem simples, indicar o problema com precisão e sugerir uma solução de forma construtiva.

**Verifique**:
- Mensagens de erro em linguagem simples (sem códigos de erro para os usuários)
- Identificação específica do problema ("Falta o @ no e-mail", não "Entrada inválida")
- Sugestões de recuperação acionáveis
- Erros exibidos perto da origem do problema
- Tratamento de erros não bloqueante (não apague o formulário)

**Pontuação**:
| Pontuação | Critérios |
|-------|----------|
| 0 | Erros enigmáticos; códigos, jargão ou nenhuma mensagem |
| 1 | Erros vagos; "Algo deu errado" sem nenhuma orientação |
| 2 | Claros, mas inúteis; nomeiam o problema, mas não a correção |
| 3 | Claros e com sugestões; identificam o problema e oferecem próximos passos |
| 4 | Recuperação perfeita; aponta o problema com precisão, sugere a correção, preserva o trabalho do usuário |

##### 10. Ajuda e documentação

Mesmo que o sistema seja utilizável sem documentação, a ajuda deve ser fácil de encontrar, focada em tarefas e concisa.

**Verifique**:
- Ajuda ou documentação pesquisável
- Ajuda contextual (tooltips, dicas inline, tours guiados)
- Organização focada em tarefas (não organizada por funcionalidades)
- Conteúdo conciso e fácil de escanear
- Acesso fácil sem sair do contexto atual

**Pontuação**:
| Pontuação | Critérios |
|-------|----------|
| 0 | Nenhuma ajuda disponível em lugar nenhum |
| 1 | A ajuda existe, mas é difícil de encontrar ou irrelevante |
| 2 | Ajuda básica; existem FAQ ou documentação, mas não contextual |
| 3 | Boa documentação; pesquisável, majoritariamente focada em tarefas |
| 4 | Excelente ajuda contextual; a informação certa no momento certo |

---

#### Resumo da pontuação

**Total possível**: 40 pontos (10 heurísticas × 4 no máximo)

| Faixa de pontuação | Classificação | O que significa |
|-------------|--------|---------------|
| 36–40 | Excelente | Só um polimento menor; pode lançar |
| 28–35 | Bom | Resolva as áreas fracas; base sólida |
| 20–27 | Aceitável | Melhorias significativas necessárias antes que os usuários fiquem satisfeitos |
| 12–19 | Ruim | Reformulação grande de UX necessária; a experiência central está quebrada |
| 0–11 | Crítico | Redesign necessário; inutilizável no estado atual |

Quando heurísticas foram pontuadas como `n/a`, o máximo é menor que 40; leia a faixa pela porcentagem, em vez do número bruto (90%+ Excelente, 70%+ Bom, 50%+ Aceitável, 30%+ Ruim, abaixo disso Crítico). 24/32 é 75%, portanto Bom.

---

<a id="issue-severity-p0p3"></a>
#### Severidade dos problemas (P0–P3)

Marque cada problema individual encontrado durante a pontuação com um nível de prioridade:

| Prioridade | Nome | Descrição | Ação |
|----------|------|-------------|--------|
| **P0** | Bloqueante | Impede totalmente a conclusão da tarefa | Corrija imediatamente; isto impede o lançamento |
| **P1** | Grave | Causa dificuldade ou confusão significativa | Corrija antes do lançamento |
| **P2** | Menor | Incômodo, mas existe uma forma de contornar | Corrija na próxima rodada |
| **P3** | Polimento | Bom de corrigir, sem impacto real no usuário | Corrija se houver tempo |

**Dica**: se estiver em dúvida entre dois níveis, pergunte: "Um usuário entraria em contato com o suporte por causa disto?" Se sim, é no mínimo P1.

---

<a id="persona-based-design-testing"></a>
### Testes de design baseados em personas

Teste a interface pelos olhos de 5 arquétipos de usuário distintos. Cada persona expõe modos de falha diferentes que uma única perspectiva de "diretor de design" deixaria passar.

**Como usar**: selecione 2–3 personas mais relevantes para a interface sob crítica. Percorra a ação principal do usuário como cada persona. Relate sinais de alerta específicos, não preocupações genéricas.

---

#### 1. Usuário avançado impaciente: "Alex"

**Perfil**: especialista em produtos semelhantes. Espera eficiência, odeia ser conduzido pela mão. Vai encontrar atalhos ou ir embora.

**Comportamentos**:
- Pula todo o onboarding e todas as instruções
- Procura atalhos de teclado imediatamente
- Tenta selecionar em massa, editar em lote e automatizar
- Se frustra com etapas obrigatórias que parecem desnecessárias
- Abandona se algo parecer lento ou condescendente

**Perguntas de teste**:
- Alex consegue concluir a tarefa central em menos de 60 segundos?
- Existem atalhos de teclado para ações comuns?
- O onboarding pode ser pulado por completo?
- Os modais podem ser fechados pelo teclado (Esc)?
- Existe um caminho para "usuário avançado" (atalhos, ações em massa)?

**Sinais de alerta** (relate-os especificamente):
- Tutoriais forçados ou onboarding que não pode ser pulado
- Nenhuma navegação por teclado para as ações principais
- Animações lentas que não podem ser puladas
- Fluxos de um item por vez onde a ação em lote seria natural
- Etapas de confirmação redundantes para ações de baixo risco

---

#### 2. Iniciante confuso: "Jordan"

**Perfil**: nunca usou este tipo de produto. Precisa de orientação em cada etapa. Prefere abandonar a tentar descobrir sozinho.

**Comportamentos**:
- Lê todas as instruções com atenção
- Hesita antes de clicar em qualquer coisa desconhecida
- Procura ajuda ou suporte o tempo todo
- Entende mal jargões e abreviações
- Adota a interpretação mais literal de qualquer rótulo

**Perguntas de teste**:
- A primeira ação fica obviamente clara em até 5 segundos?
- Todos os ícones têm rótulo de texto?
- Existe ajuda contextual nos pontos de decisão?
- A terminologia pressupõe conhecimento prévio?
- Existe um "voltar" ou "desfazer" claro em cada etapa?

**Sinais de alerta** (relate-os especificamente):
- Navegação só com ícones, sem rótulos
- Jargão técnico sem explicação
- Nenhuma opção de ajuda ou orientação visível
- Próximos passos ambíguos depois de concluir uma ação
- Nenhuma confirmação de que uma ação deu certo

---

#### 3. Usuário que depende de acessibilidade: "Sam"

**Perfil**: usa leitor de tela (VoiceOver/NVDA) e navegação somente por teclado. Pode ter baixa visão, deficiência motora ou diferenças cognitivas.

**Comportamentos**:
- Percorre a interface de forma linear com Tab
- Depende de rótulos ARIA e da estrutura de títulos
- Não consegue ver estados de hover nem indicadores apenas visuais
- Precisa de contraste de cor adequado (mínimo de 4.5:1)
- Pode usar zoom do navegador de até 200%

**Perguntas de teste**:
- O fluxo principal inteiro pode ser concluído somente pelo teclado?
- Todos os elementos interativos recebem foco, com indicadores de foco visíveis?
- As imagens têm texto alternativo significativo?
- O contraste de cor está em conformidade com WCAG AA (4.5:1 para texto)?
- O leitor de tela anuncia mudanças de estado (carregando, sucesso, erros)?

**Sinais de alerta** (relate-os especificamente):
- Interações só por clique, sem alternativa por teclado
- Indicadores de foco ausentes ou invisíveis
- Significado transmitido apenas pela cor (vermelho = erro, verde = sucesso)
- Campos de formulário ou botões sem rótulo
- Ações com limite de tempo sem opção de extensão
- Componentes personalizados que quebram o fluxo do leitor de tela

---

#### 4. Testador de estresse deliberado: "Riley"

**Perfil**: usuário metódico que leva as interfaces além do caminho feliz. Testa casos extremos, tenta entradas inesperadas e sonda lacunas na experiência.

**Comportamentos**:
- Testa casos extremos de propósito (estados vazios, strings longas, caracteres especiais)
- Envia formulários com dados inesperados (emoji, texto RTL, valores muito longos)
- Tenta quebrar fluxos navegando para trás, recarregando no meio do fluxo ou abrindo em várias abas
- Procura inconsistências entre o que a UI promete e o que de fato acontece
- Documenta os problemas de forma metódica

**Perguntas de teste**:
- O que acontece nos extremos (0 itens, 1000 itens, texto muito longo)?
- Os estados de erro se recuperam com elegância ou deixam a UI em um estado quebrado?
- O que acontece ao recarregar no meio do fluxo? O estado é preservado?
- Existem funcionalidades que parecem funcionar, mas produzem resultados quebrados?
- Como a UI lida com entradas inesperadas (emoji, caracteres especiais, colar do Excel)?

**Sinais de alerta** (relate-os especificamente):
- Funcionalidades que parecem funcionar, mas falham silenciosamente ou produzem resultados errados
- Tratamento de erros que expõe detalhes técnicos ou deixa a UI em um estado quebrado
- Estados vazios que não mostram nada útil ("Nenhum resultado" sem orientação)
- Fluxos que perdem dados do usuário ao recarregar ou navegar
- Comportamento inconsistente entre interações semelhantes em partes diferentes da UI

---

#### 5. Usuário mobile distraído: "Casey"

**Perfil**: usa o celular com uma mão só, em movimento. É interrompido com frequência. Possivelmente está em uma conexão lenta.

**Comportamentos**:
- Usa só o polegar; prefere ações na parte de baixo da tela
- É interrompido no meio do fluxo e volta mais tarde
- Alterna entre apps com frequência
- Tem pouca capacidade de atenção e pouca paciência
- Digita o mínimo possível; prefere toques e seleções

**Perguntas de teste**:
- As ações principais estão na zona do polegar (metade inferior da tela)?
- O estado é preservado se o usuário sair e voltar?
- Funciona em conexões lentas (3G)?
- Os formulários podem usar autocompletar e valores padrão inteligentes?
- Os alvos de toque têm pelo menos 44×44pt?

**Sinais de alerta** (relate-os especificamente):
- Ações importantes posicionadas no topo da tela (fora do alcance do polegar)
- Nenhuma persistência de estado; progresso perdido ao trocar de aba ou ser interrompido
- Campos de texto grandes exigidos onde uma seleção funcionaria
- Assets pesados carregando em todas as páginas (sem lazy loading)
- Alvos de toque minúsculos ou alvos próximos demais uns dos outros

---

#### Selecionando personas

Escolha as personas com base no tipo de interface:

| Tipo de interface | Personas principais | Por quê |
|---------------|-----------------|-----|
| Landing page / marketing | Jordan, Riley, Casey | Primeiras impressões, confiança, mobile |
| Dashboard / admin | Alex, Sam | Usuários avançados, acessibilidade |
| E-commerce / checkout | Casey, Riley, Jordan | Mobile, casos extremos, clareza |
| Fluxo de onboarding | Jordan, Casey | Confusão, interrupção |
| Muitos dados / analytics | Alex, Sam | Eficiência, navegação por teclado |
| Muitos formulários / wizard | Jordan, Sam, Casey | Clareza, acessibilidade, mobile |

---

#### Personas específicas do projeto

Se `.kiro/settings.json` contiver uma seção `## Design Context` (gerada por `impeccable init`), derive 1–2 personas adicionais a partir das informações de público e marca:

1. Leia a descrição do público-alvo
2. Identifique o arquétipo de usuário principal não coberto pelas 5 personas predefinidas
3. Crie uma persona seguindo este template:

```
##### [Role]: "[Name]"

**Profile**: [2-3 key characteristics derived from Design Context]

**Behaviors**: [3-4 specific behaviors based on the described audience]

**Red Flags**: [3-4 things that would alienate this specific user type]
```

Só gere personas específicas do projeto quando houver dados reais de Design Context disponíveis. Não invente detalhes do público; use as 5 personas predefinidas quando não houver contexto.
