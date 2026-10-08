> **Contexto adicional necessário**: somente o elemento-alvo, quando a solicitação não nomeia um que se resolva de forma única na página.

O generate é a via rápida para o modo live: o usuário nomeia um elemento, uma direção e uma quantidade em uma frase e, em menos de um minuto, está alternando entre variantes no navegador. Um comando inicializa o helper, entrega o elemento ao overlay na página que o seu harness já mostra (ele rola até o elemento, o seleciona e dispara o mesmo Go que um clique dispara) e retorna o evento generate; uma edição escreve as variantes; uma chamada responde e aguarda a escolha do usuário, que o próprio helper incorpora ao código-fonte. Este arquivo cuida da infraestrutura dessa via; do evento em diante, o trabalho de design é o de [live.md](live.md), sem alterações, então leia-o por completo agora se ainda não o leu nesta sessão.

**Somente web.** O overlay de navegador do modo live não tem equivalente nativo; em projetos `ios` / `android` / `adaptive`, recuse este comando e ofereça `bolder` ou `quieter` sobre o código-fonte.

É na infraestrutura que a via economiza tempo: um comando inicia a sessão em torno da página que o seu harness já mostra, uma chamada responde e aguarda, e nada aqui é um navegador que você precise ficar vigiando. Não é no trabalho de design que ela economiza tempo. A preparação roda como em qualquer comando (`impeccable context`, esta referência, craft-floor.md antes da edição), e as variantes são planejadas, escritas e aceitas exatamente como uma sessão live as planeja, escreve e aceita.

Três proibições cobrem as formas conhecidas de este comando dar errado:

- **Nunca execute init ou document, e nunca peça PRODUCT.md ou DESIGN.md.** Quando eles existem, o comando de início os imprime em `boot` e você os usa. Quando não existem, ele avisa (`contextMissing`, `contextNote`) e você extrai a identidade a partir do evento (Etapa 3). Um arquivo ausente nunca é motivo para entrevistar o usuário dentro deste comando; ofereça `init` em uma linha depois que a sessão terminar.
- **Nunca escreva à mão um wrapper de variantes nem invente um id de sessão.** Somente o navegador cria ids de sessão (8 caracteres hexadecimais, no Go). Um evento ausente se corrige executando de novo a Etapa 2, nunca com uma edição direta no código-fonte.
- **Não aja sobre os achados de hooks enquanto houver marcadores do live no arquivo**, e não reestilize variantes para satisfazê-los; o accept verifica o arquivo assim que a variante se torna permanente.

## Etapa 1: Interprete a solicitação

Três partes, todas tiradas da frase do usuário:

- **Um número na solicitação**: essa é a quantidade. **Nenhum número**: 3. O protocolo limita a quantidade a 8.
- **A redação da direção** se mapeia para o vocabulário de ações do live; nunca invente um novo valor de ação:
  - **ousado, mais ousado, mais forte, mais impactante**: `bolder`
  - **discreto, mais calmo, mais suave, atenuado**: `quieter`
  - **mais simples, minimalista, enxuto**: `distill`
  - **refinado, ajustado, polido**: `polish`
  - **palavras sobre fonte e tipografia**: `typeset`
  - **palavras sobre cor**: `colorize`
  - **palavras sobre arranjo e espaçamento**: `layout`
  - **palavras sobre dispositivo e breakpoint**: `adapt`
  - **palavras sobre movimento**: `animate`
  - **palavras lúdicas**: `delight`
  - **palavras sobre quebrar regras**: `overdrive`
  - **Redação que carrega intenção, mas nenhuma palavra do vocabulário** ("faça parecer um banco", "mais acolhedor", "mais premium"): `impeccable`, com a redação do usuário passada como prompt.
  - **Uma ação se encaixa E vem junto uma intenção extra** ("mais ousado, mas mantenha monocromático"): essa ação, com o restante como prompt.
  - **A redação não nomeia direção nenhuma** ("melhor", "melhorar", "mais bonito", "diferente", "renovado", "novo", "redesign", "consertar", "algumas opções", "ideias", "alternativas" ou apenas "variantes", sem mais nada): peça diretamente ao usuário que esclareça o que você não consegue deduzir. Faça uma pergunta, oferecendo o vocabulário: *"Que direção as variantes devem seguir? bolder (mais ousado), quieter (mais discreto), mais simples (distill), polido (polish), tipografia (typeset), cor (colorize), layout, movimento (animate), lúdico (delight) ou quebra de regras (overdrive)."* Mapeie a resposta com esta lista; uma resposta que continue aberta ("me surpreenda", "você escolhe") é `impeccable` com a redação original do usuário como prompt, e a Etapa 2 começa a partir dessa resposta.
- **A descrição do elemento** ("os cartões de preço", "o título da seção hero"): a Etapa 2 a resolve para um seletor.

Concluída quando você tiver uma ação do vocabulário (perguntada, quando a solicitação não nomeou direção), uma quantidade de 1 a 8 e a descrição do elemento.

## Etapa 2: Reutilize a página e então inicie

**Reutilize** o servidor de desenvolvimento que já está rodando e a aba em que o seu harness já o mostra; um segundo servidor ou uma segunda janela de navegador é a falha que esta etapa evita.

1. **Encontre o servidor de desenvolvimento**, começando pela fonte mais barata, e pare no primeiro acerto: a mensagem do usuário, uma aba do navegador já aberta no app (Claude Code: uma origem em `tabs_context`), um servidor que o seu harness iniciou (Claude Code: `preview_list`), um terminal que imprimiu a URL dele. A origem dele é o seu `--dev-url`. **Nenhum acerto**: deixe `--dev-url` de fora e execute o comando de início sem espera; a inicialização sonda em busca de um servidor em execução e o veredito dela indica o próximo passo. `browser_needed` traz o `devUrl` que encontrou: abra-o como em 2 e então execute de novo com `--dev-url <devUrl> --wait-for-browser 60000`. `no_dev_server` significa que nada está servindo o app: inicie o script de desenvolvimento como o veredito indicar (Claude Code: `preview_start`; Cursor: um terminal em segundo plano; Codex: um exec do qual você faz yield), aguarde a URL dele e então execute de novo com `--dev-url <url>`.
2. **Abra no seu navegador a página que renderiza o elemento e então inicie.** A rota que a solicitação nomeia ou, na falta dela, a que `--target` serve; `--dev-url` recebe somente a origem.
   - **Cursor** (`browser_navigate`) e **Claude Code** (`navigate`, que abre o painel Browser quando ele está fechado e recebe o `tabId` de `tabs_context` quando já há uma aba nessa origem): abra a URL e então execute o comando de início com `--dev-url <url> --wait-for-browser 60000`. A inicialização injeta o overlay e a página recarrega com ele enquanto o comando aguarda. Nesses harnesses, a sua ferramenta de navegador é a única que abre páginas; ali o motor ignora `--open`.
   - **Sem ferramenta de navegador** (Codex, outros): execute o comando de início com `--open --wait-for-browser 120000`; ele abre o navegador do sistema, e a espera mais longa dá tempo para o usuário encontrar a aba. **Retorno `browser_open_failed`**: informe ao usuário a `url` em uma linha e execute de novo com `--wait-for-browser 120000`.

```bash
.kiro/skills/impeccable/scripts/impeccable live-generate --target src/App.jsx --dev-url http://127.0.0.1:5173/ --selector ".pricing-grid" --action bolder --count 3 --boot --wait-for-browser 60000
```

Execute-o em primeiro plano no Cursor e no Claude Code (ele retorna dentro do tempo de espera); no Codex, em um exec do qual você faz yield, do mesmo jeito que a Etapa 3 executa o poll.

- `--target`: o arquivo que renderiza o elemento, quando a solicitação ou o projeto deixa isso óbvio; caso contrário, omita.
- `--dev-url`: a origem encontrada em 1; omita-a e a inicialização sonda.
- `--selector`: primeiro uma classe única, depois uma tag de landmark mais classe, um id por último (cada variante monta uma cópia do elemento, então um id se repete no DOM). **A solicitação nomeia um componente repetido no plural** ("os cartões de preço"): mire o contêiner que guarda o conjunto, para que uma única folha de estilo com escopo reestilize todas as instâncias. Uma leitura do arquivo-fonte que renderiza o elemento é permitida quando o seletor não é óbvio; `--dry-run` resolve e relata sem iniciar nada quando você não tem certeza.
- `--boot`: executa a inicialização da via (PRODUCT.md e DESIGN.md carregados de novo para o helper, arquivos ausentes tolerados, URL de desenvolvimento encontrada, barra inferior oculta durante a vida do helper) e reutiliza um helper que já esteja rodando. O resultado vem junto como `boot`.
- Também disponíveis: `--prompt`, `--text` (mantém somente as correspondências cujo texto visível contém um trecho), `--index` (escolha, a partir de 1, entre as correspondências).

Leia a saída nesta ordem: `boot` (ou `boot.contextMissing` com `boot.contextNote`: a página é a fonte da verdade, conforme a nota), depois `event`, o evento generate para `sessionId`, com as mesmas `_instructions` que o Go de um usuário recebe. Todo veredito traz `_instructions`, e elas prevalecem sobre o que você lembra deste arquivo; os vereditos cujo próximo passo é uma decisão sua:

- **`ambiguous`**: os candidatos são listados; mire o contêiner comum a eles, ou execute de novo com `--text "<visible text>"` ou `--index <n>`.
- **`dev_server_gone`**: o servidor de desenvolvimento parou de responder enquanto o comando aguardava a página (no Cursor, um servidor iniciado por outro chat morre junto com esse chat). Inicie-o como o veredito indicar e então execute de novo com `--dev-url <url>`.
- **`no_match`**: a aba está em uma rota que não renderiza o elemento (navegue até a rota certa e execute de novo), ou o seletor está errado (derive um melhor a partir do código-fonte, ou adicione `--text`).
- **`config_missing` / `config_invalid`** em `bootError`: siga [live-setup.md](live-setup.md) primeiro e então execute de novo.
- **`event: null`** com `ok: true`: o evento demorou mais do que a espera; execute `.kiro/skills/impeccable/scripts/impeccable live-poll` uma vez para coletá-lo e então continue.

Concluída quando a saída mostrar `ok: true`, um `sessionId` e um `event`, alcançados com no máximo um servidor iniciado e uma aba aberta por você.

## Etapa 3: Gere

O evento é um evento `generate` padrão: o contexto do elemento escolhido, um scaffold preparado no preflight e `_instructions` nomeando a referência da ação, a seção de planejamento e o encaixe exato. Trate-o exatamente conforme **Tratar generate** do live.md, que cuida de tudo, da trava de identidade até a resposta done: leia a referência da ação e o craft-floor.md como ele indica, planeje conforme a seção 4 (identidade primeiro, depois modo, depois três eixos principais diferentes, depois o teste de olhos semicerrados), declare os controles conforme a seção 7 e entregue conforme a seção 6 (uma substituição completa do elemento por variante, o CSS de pré-visualização mais todas as variantes em uma única edição no ponto de encaixe do scaffold). A via não muda nada sobre o que uma variante pode ser: as jogadas que uma sessão live faria neste elemento (um nível promovido, um conjunto reestruturado, um cartão reordenado, uma superfície diferente) também estão abertas aqui. Nunca tire captura de tela da página; a pré-visualização do overlay é o canal de revisão até o accept.

**Responda e aguarde em uma única chamada**, com o arquivo que você escreveu:

```bash
.kiro/skills/impeccable/scripts/impeccable live-poll --reply EVENT_ID done --file src/App.jsx --then-poll
```

Isso responde done (o navegador monta as variantes) e depois bloqueia até a escolha do usuário chegar, então execute-o do jeito que o seu harness executa uma espera longa: **Claude Code** em primeiro plano com o timeout mais longo da sua ferramenta (600000 ms), para que você fique pausado até a escolha chegar; **Codex** em um exec em primeiro plano com yield; **Cursor** em um terminal em segundo plano com notificação em `"type":"(accept|discard|variant_mount_failed|exit)"`. Nunca passe um `--timeout=` curto. Enquanto ele roda, não há mais nada a fazer: nunca use sleep e nunca consulte a saída dele em intervalos; um harness que o coloca em segundo plano acorda você quando ele retorna. `{"type":"timeout"}` significa que o usuário ainda não escolheu: execute `live-poll` de novo e continue aguardando. Se a edição falhar depois que o navegador passou para GENERATING, use `--reply EVENT_ID error "Short reason"` (sem `--then-poll`) para que a barra seja redefinida.

Depois diga ao usuário, em uma linha, onde estão as variantes dele: *"Três variantes [bolder] estão no ar em [os cartões de preço]: alterne com as setas da barra flutuante, ajuste os controles Tune e clique em Accept na escolhida."*

Fora do caminho de replace, leia a seção correspondente do live.md antes de agir: `scaffold.previewMode: "svelte-component"` (pré-visualizações Svelte são editadas como componentes, e o accept delas é mecânico), `mode: "insert"`, `variant_mount_failed`, `steer`, `manual_edit_apply` e qualquer erro de wrap com `fallback: "agent-driven"`.

## Etapa 4: Aceite e encerre

A chamada da Etapa 3 retorna a escolha do usuário. **`discard`**: nada a fazer. **`accept`**: `_acceptResult.carbonize: true` é o caso normal, e a limpeza é a de **Obrigatório após o accept** do live.md, sem alterações: mova as regras da variante aceita para a folha de estilo que já cuida do elemento, com seletores reais, incorpore os valores escolhidos dos controles, desembrulhe o elemento e remova todo atributo `data-impeccable-*`, exclua o bloco `<style>` inline e os dois marcadores `impeccable-carbonize`, então execute `.kiro/skills/impeccable/scripts/impeccable live-complete --id SESSION_ID` e confirme `phase: "completed"`. (`baked: true` só aparece quando o accept foi executado com `--bake`; nesse caso o helper já tornou a variante permanente e nenhum `live-complete` é devido.)

Encerre sem que peçam, assim que a escolha for tratada:

```bash
.kiro/skills/impeccable/scripts/impeccable live-server stop
```

Parar remove o script injetado e recarrega a página uma vez: o usuário vê o design aceito sem o chrome do overlay, ainda servido pelo servidor de desenvolvimento dele. **Nunca encerre nem reinicie o servidor de desenvolvimento**, inclusive um que você tenha iniciado na Etapa 2.

- **O usuário pede mais variantes antes de você encerrar**: pule o encerramento, execute a Etapa 2 de novo para o próximo elemento (o helper é reutilizado) e encerre depois da última escolha.
- **Interrompido ou sem certeza do estado**: `.kiro/skills/impeccable/scripts/impeccable live-status` e depois `live-resume`; o journal em `.impeccable/live/sessions/` é canônico.

Concluída quando o helper estiver parado e o site de desenvolvimento ainda responder com o design aceito.
