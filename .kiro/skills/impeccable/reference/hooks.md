# /impeccable hooks

Gerencie o **hook do detector de design** do projeto atual.

O hook executa o detector de design do impeccable em edições diretas de arquivos relevantes para design (`.tsx`, `.jsx`, `.html`, `.vue`, `.svelte`, `.astro`, `.css`, `.scss`, `.sass`, `.less`, `.ts`, `.js`). Claude Code, Codex e GitHub Copilot usam um hook pós-uso de ferramenta e inserem um breve lembrete de sistema no contexto do agente após a edição; achados recebem um prompt de correção, problemas pendentes recebem um novo lembrete, e arquivos limpos com cara de UI recebem uma breve confirmação, a menos que o modo silencioso esteja ativado (`hook.quiet` na configuração). Arquivos `.ts` e `.js` simples continuam sendo analisados, mas ficam em silêncio a menos que o detector encontre algo. O Cursor usa `preToolUse` para bloquear escritas propostas ruins antes que elas aconteçam e fica em silêncio quando permite uma escrita limpa. O Grok Build dispara a mesma análise PostToolUse para marcar os arquivos tocados e depois exibe os achados no `additionalContext` do Stop. Não espere um lembrete por edição no Grok: o Grok descarta esse stdout.

As regras do detector rodam em dois níveis. O hook por edição exibe apenas o nível imediato: problemas mecânicos e inequívocos que valem a interrupção de uma edição, como imagens quebradas, conteúdo transbordando, falhas de contraste e legibilidade, texto em gradiente, sombras de brilho e desvio do design system. Todo o resto (cadência da copy, gosto de paleta e tipografia, ritmo do layout) é adiado para uma passada profunda no evento de hook `Stop`, que executa o conjunto completo de regras sobre cada arquivo de UI tocado na sessão e exibe os achados restantes uma única vez, sem duplicar o que a passada por edição já reportou. Uma sessão sem nada a reportar termina em silêncio. Defina `hook.perEditRules` como `"all"` em `.impeccable/config.json` para restaurar o conjunto completo de regras em toda edição. A passada profunda do Stop está ligada para Claude Code, Codex e Grok Build, que disparam um evento de hook `Stop` nativo. O Cursor não recebe uma (seu hook de parada não é disparado de forma consistente; o filtro pré-escrita o cobre), e os eventos do tipo stop do GitHub Copilot não devolvem contexto ao modelo, então eles mantêm o detector completo por edição. O Grok também dispara um Stop somente de observação com `reason: "shutdown"` após `end_turn`; pule esse, analise apenas `end_turn`.

Todo hook é uma passada mecânica. Os reflexos que nenhum scanner captura vivem em [craft-floor.md](craft-floor.md), que a skill carrega antes de editar a UI, então eles se aplicam haja ou não um hook ligado. Uma sessão sem hook automático recebe uma diretiva `MANUAL_DETECTOR_REQUIRED` de `impeccable context` pedindo uma única execução do detector no final.

Este comando ativa e desativa o hook **por projeto** editando `.impeccable/config.json` (a configuração unificada do Impeccable; as configurações de runtime do hook ficam sob a chave `hook`, e os ignores compartilhados do detector ficam sob `detector`). Sobrescritas por desenvolvedor, incluindo a decisão de consentimento de instalação (`hook.consent`) que a CLI registra, ficam no `.impeccable/config.local.json`, que é ignorado pelo git. Defina `hook.enabled: false` para desligar o hook, `hook.quiet: true` para silenciar as confirmações de limpo/pendente, ou `hook.auditLog` com um caminho de arquivo para um log NDJSON. As variáveis de ambiente legadas `IMPECCABLE_HOOK_DISABLED`, `IMPECCABLE_HOOK_QUIET` e `IMPECCABLE_HOOK_LOG` continuam sendo respeitadas e sobrescrevem esses valores de configuração quando definidas.

Declare extensões de templates do lado do servidor em **`detector.extensions`** quando o projeto usar arquivos Blade, Twig, ERB ou Handlebars; caso contrário, o hook os ignora porque ficam fora da lista de extensões embutida. Uma entrada por extensão, `{ "ext": ".blade.php", "engine": "html" }`. `engine` escolhe o analisador (`html` para templates de markup, `text` para arquivos semelhantes a JS/TS/CSS) e o padrão é `html`. A correspondência é feita contra o final do nome do arquivo, então extensões duplas como `.blade.php` e `.html.erb` funcionam. A configuração apenas adiciona extensões; a lista embutida sempre se aplica.

Análises manuais com `npx impeccable detect` usam por padrão a mesma configuração de filtros do projeto: `detector.ignoreRules`, `detector.ignoreFiles`, `detector.ignoreValues` e `detector.designSystem.enabled`. `hook.enabled` controla apenas a execução automática do hook, não as análises manuais pela CLI. Use `npx impeccable detect --no-config ...` para uma execução crua do detector que ignora a configuração/contexto do projeto. Use `npx impeccable ignores ...` para CRUD direto pela CLI sobre os mesmos ignores do detector.

Harnesses suportados: Claude Code (`.claude/settings.local.json` no projeto, que é ignorado pelo git para que o hook fique local à máquina; um hook que você mover para o `settings.json` compartilhado também é respeitado onde estiver), Codex (`.codex/hooks.json` no projeto), Cursor (`.cursor/hooks.json` no projeto), Grok Build (`.grok/hooks/impeccable.json` no projeto; requer `/hooks-trust` ou `--trust`) e GitHub Copilot (`.github/hooks/impeccable.json` no projeto, um arquivo compartilhado pela equipe e commitado que tanto a Copilot CLI quanto o agente na nuvem leem). Para a Copilot CLI, hooks em nível de repositório disparam assim que `.github/hooks/impeccable.json` é commitado no branch padrão do repositório.

No **Cursor**, `preToolUse` verifica o conteúdo proposto de escritas Write/Edit/Shell e nega somente quando o detector real encontra um problema. A mensagem de negação fica visível para o agente como o erro da ferramenta, então o agente pode reconsiderar antes que a escrita ruim aconteça.

O Gemini instala hooks de sessão e de conclusão em `.gemini/settings.json`, mesclados às configurações já existentes (comentários são tolerados; um arquivo com comentários recebe backup em `settings.json.bak` antes da reescrita, e um arquivo que não é JSON válido é deixado intacto). Ele não instala um hook de detector por edição. O hook `BeforeTool` apenas reescreve comandos de shell que executam `build-phase`, e somente no macOS e no Linux; no Windows (onde o Gemini executa hooks pelo PowerShell) nenhum id de sessão chega ao shell, então uma construção por comp não fica vinculada à sessão e o lembrete de conclusão fica em silêncio.

## Roteamento

O primeiro argumento é a ação. O padrão é `status`.

| Ação | O que faz |
|---|---|
| `status` | Imprime o estado atual, os caminhos de configuração compartilhada/local, as regras / arquivos / valores ignorados e a sobrescrita por variável de ambiente. |
| `on` | Define `enabled: true` em `.impeccable/config.json`, registra o consentimento local do hook como aceito e instala/repara os manifestos de hook dos provedores quando a skill está instalada. |
| `off` | Define `enabled: false` em `.impeccable/config.json`. |
| `ignore-rule <id>` | Acrescenta `<id>` a `detector.ignoreRules`; para `overused-font`, exige `--all-values`. Suprime a regra no projeto inteiro. |
| `ignore-file <glob>` | Acrescenta `<glob>` a `detector.ignoreFiles`. Suprime **todas** as regras para os arquivos correspondentes. |
| `ignore-value <id> <value> [--shared] [--reason "..."]` | Acrescenta uma supressão de regra/valor ao `.impeccable/config.json` compartilhado. |
| `ignore-value <id> <value> --local [--reason "..."]` | Acrescenta uma supressão privada de regra/valor ao `.impeccable/config.local.json`. |
| `ignore-value <id> "*" --file <glob> [--file <glob>...]` | Desliga uma regra apenas nos arquivos correspondentes, deixando-a ativa em todo o resto. Repita `--file`, ou use `--file=<glob>` / `--files=<glob>`. Um `"*"` sozinho sem `--file` é recusado: use `ignore-rule <id>` se você realmente quer dizer o projeto inteiro. |
| `reset` | Apaga a configuração do projeto, o cache de deduplicação e a fila de pendências do Cursor, e remove as entradas do hook de todo manifesto de provedor que `on` instala, incluindo o arquivo commitado do Copilot (um `settings.json` compartilhado pela equipe que `on` nunca escreve nunca é tocado). |

## Fluxo

1. Resolva a ação a partir do argumento do usuário. Se nenhuma ação foi dada, use `status` como padrão.
2. Invoque o script de administração e repasse a saída literalmente ao usuário:

   ```bash
   .kiro/skills/impeccable/scripts/impeccable hooks <action> [args...]
   ```

3. Se `<action>` for `off`, complemente com uma nota de uma linha: "Pronto. Novas edições não vão disparar o hook de design neste projeto até você executar `/impeccable hooks on`."
4. Se `<action>` for `on`, complemente com: "Pronto. O hook de design vai disparar após o próximo Edit/Write em um arquivo de UI."
5. Se `<action>` for `ignore-value`, `ignore-file` ou `ignore-rule`, apenas imprima a saída do script. O escopo padrão é o `.impeccable/config.json` compartilhado; adicione `--local` somente quando o usuário pedir explicitamente uma exceção privada.
6. Se `<action>` for `status`, apenas imprima a saída do script. Não adicione comentários, a menos que o usuário tenha feito uma pergunta de acompanhamento.

## Triagem de achados

O próprio hook nunca escreve configuração de ignore; toda exceção passa por `impeccable hooks`. Faça a triagem de cada achado em um de três resultados:

- **Problema real de design**: corrija. Nunca adicione um ignore para pular uma correção ou para forçar a passagem de uma escrita bloqueada.
- **Falso positivo confiável ou exceção sancionada**: persista você mesmo o ignore mais restrito e informe-o na sua resposta. O critério é uma evidência que você consiga nomear: uma demo ou fixture intencional, documentação de design ruim, movimento literal ou adequado ao domínio (uma bola que quica), ou uma escolha que o usuário já confirmou. Coloque essa evidência em `--reason` como `"<who decided: evidence>"`; escreva "user confirmed" somente quando o usuário realmente tiver confirmado.
- **Em dúvida**: deixe o achado de pé e pergunte ao usuário em uma linha. Pergunte uma vez; uma pergunta de uma linha custa menos do que o hook disparando de novo em toda edição posterior.

O autoatendimento para em `ignore-value`. `ignore-file` e `ignore-rule` silenciam demais para serem adicionados por julgamento próprio; pergunte ao usuário primeiro.

Prefira a exceção mais restrita:

- Se a linha do achado mostrar um par `ignore-value <rule> <value>`, passe-o para `impeccable hooks ignore-value` com o seu `--reason`. Isso escreve no `.impeccable/config.json` compartilhado por padrão.
- Para achados específicos de valor, como `overused-font` e `bounce-easing`, use `ignore-value` para o valor específico. Não use `ignore-rule overused-font` para uma fonte específica.
- Se o achado não tiver um comando específico de valor, como `side-tab`, restrinja essa única regra ao arquivo: `ignore-value <id> "*" --file <path>`. Execute `npx impeccable detect <path>` primeiro para ver o que realmente dispara ali.
- Recorra a `ignore-file <path>` somente quando o arquivo inteiro estiver fora do escopo da revisão de design: uma fixture, um artefato gerado, uma demo deliberada de slop. Ele silencia permanentemente todas as regras para esse arquivo, incluindo regras que ainda não foram escritas. Uma superfície de UI real com uma regra barulhenta pede o ignore de valor restrito ao arquivo descrito acima.
- Use `ignore-rule <id>` somente quando o usuário pedir para suprimir essa regra inteira no projeto todo. Para supressão ampla de overused-font, use `ignore-rule overused-font --all-values` somente quando o usuário pedir para ignorar fontes superutilizadas em geral.
- Prefira ignores de configuração (os comandos acima) por padrão; eles mantêm as supressões em um único lugar revisável. Recorra a um comentário inline somente quando a dispensa precisar acompanhar um único arquivo que sai do repositório (um documento autônomo gerado/exportado, um arquivo HTML enviado por e-mail). O marcador suportado é `impeccable-disable <rule>` (arquivo inteiro) ou `impeccable-disable-line` / `impeccable-disable-next-line` (uma linha), em qualquer sintaxe de comentário, com um motivo opcional após `:` ou `--`. O detector o respeita por padrão; `--no-inline-ignores` ou `--no-config` o ignora.

Exemplo de exceção específica de valor:

```bash
.kiro/skills/impeccable/scripts/impeccable hooks ignore-value overused-font Inter --shared --reason "User confirmed Inter is intentional"
```

Exemplo de exceção por autoatendimento, com a evidência nomeada:

```bash
.kiro/skills/impeccable/scripts/impeccable hooks ignore-value bounce-easing bounce-ball --shared --reason "Agent: literal ball-bounce animation, bounce easing is the subject"
```

Exemplo de exceção de fonte para a regra inteira:

```bash
.kiro/skills/impeccable/scripts/impeccable hooks ignore-rule overused-font --all-values --reason "User asked to ignore overused fonts generally"
```

Exemplo de exceção de uma regra em um arquivo, para um arquivo que ainda vale a pena revisar
em todo o resto:

```bash
.kiro/skills/impeccable/scripts/impeccable hooks ignore-value design-system-font-size "*" --file "src/overlay/widget.js" --reason "Injected widget builds its own type scale; DESIGN.md's ramp describes the site"
```

Exemplo de exceção para o arquivo inteiro, para um arquivo totalmente fora do escopo:

```bash
.kiro/skills/impeccable/scripts/impeccable hooks ignore-file "src/legacy/Card.tsx"
```

## Restrições

- Nunca modifique `.impeccable/config.json` ou `.impeccable/config.local.json` à mão a partir deste comando. Sempre passe por `impeccable hooks` para que as escritas continuem validadas e o formato do arquivo continue consistente. Uma exceção: `detector.extensions` não tem ação de administração, então, quando o usuário pedir para cobrir uma stack de templates, edite esse único campo em `.impeccable/config.json` diretamente e deixe o resto do arquivo intacto.
- Não edite o launcher (inicializador) nem o binário por trás de `impeccable hook` e `impeccable hook-before-edit` a partir deste fluxo. Eles são encanamento da skill.
- O Cursor pode bloquear uma escrita proposta quando o detector encontra um problema real. Claude Code, Codex e GitHub Copilot não bloqueiam a edição; em vez disso, emitem um lembrete pós-edição. Desativar interrompe tanto o bloqueio quanto os lembretes.
- O hook vem junto com a skill Impeccable e é instalado por meio de manifestos locais do projeto: `.claude/settings.local.json`, `.codex/hooks.json`, `.cursor/hooks.json`, `.github/hooks/impeccable.json` e `.gemini/settings.json`. No Codex, o usuário precisa aprovar o hook via `/hooks` na primeira vez. No Cursor, confirme que os hooks estão ativados em Settings -> Hooks. No GitHub Copilot, a CLI carrega `.github/hooks/impeccable.json` assim que ele é commitado no branch padrão do repositório, e o agente na nuvem o lê diretamente do repositório.

## Modos de falha

- Se `.impeccable/config.json` ou `.impeccable/config.local.json` estiver ilegível ou malformado, o hook ignora esse arquivo e usa a configuração válida restante/os padrões. `impeccable hooks status` vai mostrar os arquivos malformados como ignorados.
- Se o usuário pedir para "desativar o hook" globalmente, comece por `/impeccable hooks off` (persistente para este projeto; escreve `hook.enabled: false` na configuração). A variável de ambiente legada `IMPECCABLE_HOOK_DISABLED=1` também funciona como uma sobrescrita pontual que acompanha o shell.

