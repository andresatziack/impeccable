# Impeccable

Orientação de design para agentes de programação com IA. 1 skill, 24 comandos, iteração ao vivo no navegador e 59 regras determinísticas de detector para design de frontend gerado por IA.

> 🇧🇷 Tradução para português. Para instalar no Kiro sem NPX, veja [INSTALACAO-KIRO.md](INSTALACAO-KIRO.md). Relatório de segurança: [SEGURANCA.md](SEGURANCA.md).

> **Início rápido:** a partir da raiz do seu projeto, execute `npx impeccable install` e depois execute `/impeccable init` dentro da sua ferramenta de programação com IA. Documentação completa: [impeccable.style](https://impeccable.style).

## Por que o Impeccable?

O [frontend-design](https://github.com/anthropics/skills/tree/main/skills/frontend-design) da Anthropic foi a primeira skill de design amplamente usada para o Claude. O Impeccable começou a partir dali.

Todos os modelos foram treinados nos mesmos templates de SaaS. Pule a orientação e você obtém o mesmo punhado de sinais em todo projeto: Inter para tudo, gradientes de roxo para azul, cards aninhados em cards, texto cinza sobre fundos coloridos, o ícone em quadrado arredondado acima de cada título.

O Impeccable acrescenta:
- **Um único fluxo de configuração.** `/impeccable init` registra a verdade duradoura do produto em `PRODUCT.md`, para que os comandos seguintes conheçam o público, o propósito, o contexto de operação, as restrições, a voz e as evidências, sem confundir esses fatos com a direção visual superficial.
- **24 comandos.** Um vocabulário de design compartilhado com a sua IA: `polish`, `audit`, `critique`, `distill`, `animate`, `bolder`, `quieter` e outros.
- **59 regras determinísticas de detector**, além de verificações de crítica feitas apenas pelo LLM. A CLI e a extensão de navegador executam as regras determinísticas sem LLM e sem chave de API.

## O que está incluído

### A skill: impeccable

A skill é instalada como um único comando:

```bash
/impeccable <command> <target>
```

Comece todo projeto novo com:

```bash
/impeccable init
```

`init` inspeciona o projeto, pergunta apenas sobre lacunas relevantes na verdade duradoura do produto e escreve `PRODUCT.md`. O modo do visitante e a direção visual são escolhidos depois, para cada superfície; sistemas visuais existentes ou recém-construídos são registrados separadamente em `DESIGN.md`.

### 24 comandos

Todos os comandos são acessados por meio de `/impeccable`:

| Comando | O que faz |
|---------|--------------|
| `/impeccable craft` | Fluxo completo de dar forma e depois construir, com iteração visual |
| `/impeccable init` | Configuração única: reúne o contexto duradouro do produto, escreve PRODUCT.md, configura o modo live quando aplicável e recomenda próximos passos |
| `/impeccable document` | Gera o DESIGN.md raiz a partir do código existente do projeto |
| `/impeccable extract` | Extrai componentes e tokens reutilizáveis para o design system |
| `/impeccable shape` | Planeja UX/UI antes de escrever código |
| `/impeccable critique` | Revisão de design de UX: hierarquia, clareza, ressonância emocional |
| `/impeccable audit` | Executa verificações técnicas de qualidade (a11y, desempenho, responsivo) |
| `/impeccable polish` | Passada final, alinhamento ao design system e prontidão para entrega |
| `/impeccable bolder` | Amplifica designs sem graça |
| `/impeccable quieter` | Suaviza designs ousados demais |
| `/impeccable distill` | Reduz à essência |
| `/impeccable harden` | Tratamento de erros, i18n, estouro de texto, casos extremos |
| `/impeccable onboard` | Fluxos de primeiro uso, estados vazios, caminhos de ativação |
| `/impeccable animate` | Adiciona movimento com propósito |
| `/impeccable colorize` | Introduz cor de forma estratégica |
| `/impeccable typeset` | Corrige escolhas de fonte, hierarquia, tamanhos |
| `/impeccable layout` | Corrige layout, espaçamento, ritmo visual |
| `/impeccable delight` | Adiciona momentos de encanto |
| `/impeccable overdrive` | Adiciona efeitos tecnicamente extraordinários |
| `/impeccable clarify` | Melhora copy de UX pouco clara |
| `/impeccable adapt` | Adapta para diferentes dispositivos |
| `/impeccable optimize` | Melhorias de desempenho |
| `/impeccable live` | Modo de variantes visuais: itere sobre elementos no navegador |
| `/impeccable generate` | Gera variantes de um elemento nomeado no navegador ao vivo, sem seleção manual |

Use `/impeccable pin <command>` para criar atalhos independentes (por exemplo, `pin audit` cria `/audit`).

#### Exemplos de uso

```
/impeccable audit blog           # Audit blog hub + post pages
/impeccable critique landing     # UX design review
/impeccable polish settings      # Final pass before shipping
/impeccable harden checkout      # Add error handling + edge cases
```

Ou use `/impeccable` diretamente com uma descrição:
```
/impeccable redo this hero section
```

### Antipadrões

A skill inclui orientação explícita sobre o que evitar:

- Não use fontes usadas em excesso (Arial, Inter, padrões do sistema)
- Não use texto cinza sobre fundos coloridos
- Não use preto/cinza puros (sempre aplique um matiz)
- Não envolva tudo em cards nem aninhe cards dentro de cards
- Não use easing com quique/elástico (parece datado)

## Veja em ação

Visite [o estudo de caso Neo Mirai](https://impeccable.style/cases/neo-mirai) para ver um estudo de caso de antes/depois de um projeto real transformado com os comandos do Impeccable.

## Instalação

A skill não precisa de runtime próprio. Toda cópia da skill traz um pequeno launcher (inicializador) (`scripts/impeccable`, mais `impeccable.cmd` para Windows) que executa o motor do Impeccable, um binário autocontido que fica ao lado do launcher ou é baixado uma única vez, na primeira execução, para `~/.impeccable/bin/`. O Node só entra em cena se você usar o instalador `npx impeccable`, que é um shim em torno do mesmo binário; as opções manual e via Git abaixo funcionam sem ele.

### Opção 1: instalador via CLI (recomendado)

A partir da raiz do seu projeto, execute:

```bash
npx impeccable install
```

Isso mostra as pastas de harness (ferramenta de agente) ou as CLIs instaladas que ele detectou (por exemplo `~/.claude`, `~/.codex`, `~/.grok`, `~/.hermes`, `~/.veto` ou `.cursor` local do projeto), permite que você mantenha o conjunto detectado ou personalize os provedores e, em seguida, pergunta se deve instalar no projeto atual ou globalmente. Use `--providers=claude,codex,cursor,grok,hermes,veto` e `--scope=project|global` para pular essas escolhas em scripts. No Claude Code, Cursor, Codex, GitHub Copilot e Grok Build, ele também instala o manifesto de hook nativo do provedor para o projeto atual. O Veto recebe a skill empacotada em `~/.veto/skills/` e não executa hooks nativos de edição do Impeccable. Funciona com Cursor, Claude Code, Gemini CLI, Codex CLI, Grok Build, Hermes Agent, Veto e todas as outras ferramentas suportadas. Recarregue o seu harness depois.

Para atualizar uma instalação existente, execute:

```bash
npx impeccable update
```

Usuários do Codex devem abrir `/hooks` após a instalação ou atualização e aprovar o hook do projeto quando solicitado. O Codex rastreia a confiança pela definição do hook, então atualizações que alteram `.codex/hooks.json` podem exigir aprovação novamente. Usuários do Grok Build precisam de confiança na pasta do projeto (`/hooks-trust` ou iniciar com `--trust`) antes que os scripts de `.grok/hooks/` sejam executados.

Veja [Permita o hook no seu harness](https://impeccable.style/docs/hooks#allow-the-hook-in-your-harness) para os passos de confiança e verificação específicos de cada harness.

### Opção 2: submódulo Git

Para equipes que querem manter o Impeccable incorporado ao repositório e atualizado via Git, adicione este repositório como submódulo e vincule o build compilado do provedor às pastas do seu harness:

```bash
git submodule add https://github.com/pbakaus/impeccable .impeccable
npx impeccable link --source=.impeccable --providers=claude,cursor
git add .gitmodules .impeccable .claude .cursor
git commit -m "Add Impeccable skills"
```

Use os provedores de que o seu projeto precisa, por exemplo `claude`, `cursor`, `gemini`, `codex`, `github`, `grok`, `hermes`, `opencode`, `pi`, `qoder`, `trae`, `trae-cn`, `rovo-dev`, `vibe` ou `veto`. O comando vincula pastas individuais de skill de `.impeccable/dist/universal/` e não toca em diretórios de skill reais já existentes, a menos que você passe `--force`.

Para atualizar depois:

```bash
git submodule update --remote .impeccable
npx impeccable link --source=.impeccable --providers=claude,cursor
```

### Opção 3: instalação via plugin

**GitHub Copilot no VS Code:**

Instale o [Impeccable pelo Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=renaissance-geek.impeccable) ou execute:

```bash
code --install-extension renaissance-geek.impeccable
```

Requer VS Code 1.109.3+, acesso ao Copilot Chat e um workspace local confiável. Abra o Chat no modo Agent e experimente `/impeccable polish`. Esta extensão, que contém apenas a skill, não instala hooks automáticos; evite ter uma skill Impeccable duplicada no mesmo workspace/perfil. Veja os [detalhes da distribuição para VS Code](docs/VSCODE-EXTENSION.md).

**Claude Code:**
```bash
/plugin marketplace add pbakaus/impeccable
```

> Somente Claude Code. Depois de adicionar o marketplace, abra `/plugin` e instale o Impeccable a partir da lista.

**Grok Build:**
```bash
grok plugin install pbakaus/impeccable#plugin --trust
```

> Somente Grok Build. O sufixo `#plugin` instala o pacote de plugin enxuto (skills, agentes e hooks) em vez do monorepo completo. Em seguida, execute `/impeccable init` em uma sessão do Grok. Instalações com escopo de projeto via `npx impeccable install --providers=grok` também funcionam e escrevem `.grok/skills/` mais `.grok/hooks/impeccable.json`.

### Opção 4: download pelo site

Visite [impeccable.style](https://impeccable.style), baixe o ZIP para a sua ferramenta e extraia no seu projeto.

### Opção 5: copiar do repositório

**Cursor:**
```bash
cp -r dist/cursor/.cursor your-project/
```

> **Observação:** as skills do Cursor exigem configuração:
> 1. Mude para o canal Nightly em Cursor Settings → Beta
> 2. Ative Agent Skills em Cursor Settings → Rules
>
> [Saiba mais sobre as skills do Cursor](https://cursor.com/docs/context/skills)

**Claude Code:**
```bash
# Project-specific
cp -r dist/claude-code/.claude your-project/

# Or global (applies to all projects)
cp -r dist/claude-code/.claude/* ~/.claude/
```

**OpenCode:**
```bash
cp -r dist/opencode/.opencode your-project/
```

**DeepSeek Harness:**
```bash
# Project-specific
cp -r dist/dsh/.dsh your-project/

# Or global (applies to all projects)
mkdir -p "${DSH_HOME:-$HOME/.dsh}/skills"
cp -r dist/dsh/.dsh/skills/* "${DSH_HOME:-$HOME/.dsh}/skills/"
```

A CLI respeita `DSH_HOME` somente quando ele aponta para dentro do seu diretório home (ou para o próprio home); caso contrário, usa `~/.dsh`. Uma cópia manual fora do home não é gerenciada por `impeccable install/update`.

**Hermes Agent:**
```bash
# Global (applies to all projects; uses the active profile, or ~/.hermes by default)
cp -r dist/hermes/.hermes/skills/* "${HERMES_HOME:-$HOME/.hermes}/skills/"

# Or project-specific
cp -r dist/hermes/.hermes your-project/
```

> **Observação:** o Hermes condiciona as skills locais do projeto a uma decisão de confiança
> por repositório (elas são documentos de procedimento, então carregá-las automaticamente
> de qualquer repositório clonado é tratado como um vetor de injeção de prompt). Após uma
> instalação com escopo de projeto, execute `hermes skills trust` uma vez a partir da raiz
> do projeto. Instalações globais no `$HERMES_HOME/skills/` ativo (ou `~/.hermes/skills/`
> quando não definido) carregam sem etapa de confiança. `/impeccable <command>` então
> é roteado pela tabela Commands da skill; o hook de design não é instalado
> no Hermes (não há superfície de hooks).
>
> [Saiba mais sobre as skills do Hermes](https://hermes-agent.nousresearch.com/docs/user-guide/features/skills)

**Pi:**
```bash
cp -r dist/pi/.pi your-project/
```

**Gemini CLI:**
```bash
cp -r dist/gemini/.gemini your-project/
```

> **Observação:** as skills do Gemini CLI exigem configuração:
> 1. Instale a versão preview: `npm i -g @google/gemini-cli@preview`
> 2. Execute `/settings` e ative "Skills"
> 3. Execute `/skills list` para verificar a instalação
>
> [Saiba mais sobre as skills do Gemini CLI](https://geminicli.com/docs/cli/skills/)

**Codex CLI:**
```bash
# Project-local
cp -r dist/agents/.agents your-project/
mkdir -p your-project/.codex
cp dist/codex/.codex/hooks.json your-project/.codex/hooks.json

# Or install the skill user-wide. Copy .codex/hooks.json into each project
# where you want the design hook to run.
mkdir -p ~/.agents/skills
cp -r dist/agents/.agents/skills/* ~/.agents/skills/
```

> O subagente produtor de assets vem aninhado dentro da própria pasta `agents/` da skill, que o Codex descobre automaticamente. Não é necessária uma cópia separada em `.codex/agents/`. O hook é local do projeto porque o Codex descobre hooks a partir de `.codex/hooks.json`, ao lado da configuração confiável do projeto.

**GitHub Copilot:**
```bash
cp -r dist/github/.github your-project/
```

**Trae:**
```bash
# Trae China (domestic version)
cp -r dist/trae/.trae-cn/skills/* ~/.trae-cn/skills/

# Trae International
cp -r dist/trae/.trae/skills/* ~/.trae/skills/
```

> **Observação:** o Trae tem duas versões com diretórios de configuração diferentes:
> - **Trae China**: `~/.trae-cn/skills/`
> - **Trae International**: `~/.trae/skills/`
>
> Após copiar, reinicie o Trae IDE para ativar as skills.

**Rovo Dev:**
```bash
# Project-specific
cp -r dist/rovo-dev/.rovodev your-project/

# Or global (applies to all projects)
cp -r dist/rovo-dev/.rovodev/skills/* ~/.rovodev/skills/
```

**Qoder:**
```bash
# Project-specific
cp -r dist/qoder/.qoder your-project/

# Or global (applies to all projects)
cp -r dist/qoder/.qoder/skills/* ~/.qoder/skills/
```

**Mistral Vibe:**
```bash
# Project-specific
cp -r dist/vibe/.vibe your-project/

# Or global (applies to all projects)
cp -r dist/vibe/.vibe/skills/* ~/.vibe/skills/
```

**Grok Build:**
```bash
# Project-specific
cp -r dist/grok/.grok your-project/

# Or global (applies to all projects)
cp -r dist/grok/.grok/skills/* ~/.grok/skills/
```

> Prefira `npx impeccable install --providers=grok` ou `grok plugin install pbakaus/impeccable#plugin --trust` para que o hook de design também seja instalado. Os hooks de projeto precisam de `/hooks-trust` (ou `--trust`) uma vez por pasta.

**Google Antigravity:**
```bash
# Project-specific
cp -r dist/antigravity/.agent your-project/

# Or global (applies to all projects)
mkdir -p ~/.gemini/config/skills
cp -r dist/antigravity/.agent/skills/* ~/.gemini/config/skills/
```

## Uso

Depois de instalado, todo comando passa pela skill única `/impeccable`:

```
/impeccable audit        # Find issues
/impeccable polish       # Final cleanup
/impeccable distill      # Remove complexity
/impeccable critique     # Full design review
```

Digite `/impeccable` sozinho para ver a lista completa de comandos.

A maioria dos comandos aceita um argumento opcional para focar em uma área específica:

```
/impeccable audit the header
/impeccable polish the checkout form
```

Se você usa um comando com frequência, fixe-o com `/impeccable pin audit` para obter `/audit` como atalho independente.

**Observação:** o Codex usa skills aqui, não comandos `/prompts:`. Abra `/skills` ou digite `$impeccable`. Instalações locais do repositório ficam em `.agents/skills/`; instalações para todo o usuário ficam em `~/.agents/skills/`. O GitHub Copilot usa `.github/skills/`. Reinicie a ferramenta se uma skill recém-instalada não aparecer.

## Mantendo `.impeccable` fora do git

Conforme você executa comandos, o Impeccable escreve arquivos de trabalho em `.impeccable/`: capturas de tela de critique e polish, estado de sessão e de pré-visualização do modo live, caches de runtime e configuração por desenvolvedor. A maior parte disso é efêmera e não deve ser commitada, enquanto alguns arquivos são artefatos compartilhados do projeto que pertencem ao repositório. Adicione este bloco ao `.gitignore` do seu projeto:

```gitignore
# impeccable-ignore-start
# Ephemeral output, runtime state, and per-dev overrides.
# The **/ prefix covers .impeccable at the repo root or in a nested workspace.
# Shared artifacts stay tracked: config.json, live/config.json,
# design.json, surfaces/*.md, critique/*.md.
**/.impeccable/config.local.json
**/.impeccable/hook.cache.json
**/.impeccable/hook.pending.json
**/.impeccable/*.png
**/.impeccable/review/
**/.impeccable/questions/
**/.impeccable/live/server.json
**/.impeccable/live/sessions/
**/.impeccable/live/previews/
**/.impeccable/live/annotations/
**/.impeccable/live/cache/
**/.impeccable/live/manual-edit-apply-transaction.json
**/.impeccable/live/manual-edit-events.jsonl
**/.impeccable/live/manual-edit-evidence/
**/.impeccable/live/pending-manual-edits.json
**/.impeccable/live/deferred-svelte-component-accepts.json
**/.impeccable/live/*.png
# impeccable-ignore-end
```

O bloco é delimitado pelos marcadores `# impeccable-ignore-start` / `# impeccable-ignore-end`, para que você possa reconhecê-lo e atualizá-lo depois. O prefixo `**/` faz cada padrão corresponder tanto se o diretório `.impeccable/` do projeto ativo estiver na raiz do repositório quanto se estiver em um caminho de workspace aninhado como `apps/web/`.

**Mantenha estes rastreados** (são artefatos compartilhados do projeto; não os adicione ao `.gitignore`):

- `.impeccable/config.json` (configuração compartilhada unificada)
- `.impeccable/live/config.json` (conexão do modo live com o framework)
- `.impeccable/design.json` (especificação de design compartilhada)
- `.impeccable/surfaces/*.md` (contratos de estratégia e direção específicos de rota ou artefato)
- `.impeccable/critique/*.md` (relatórios de revisão)

Se um arquivo efêmero (uma captura de tela, `config.local.json`) foi commitado antes de você adicionar o bloco, o `.gitignore` não vai deixar de rastreá-lo automaticamente. Execute `git rm --cached <path>` para parar de rastreá-lo sem apagar sua cópia local.

## Hook de design

No Claude Code, GitHub Copilot, Codex, Cursor e Grok Build, `npx impeccable install` e `npx impeccable update` instalam um manifesto de hook nativo do provedor junto com o conteúdo da skill. O hook executa o detector de design do Impeccable em edições diretas de arquivos de UI e devolve os achados ao fluxo do agente. Claude Code, GitHub Copilot e Codex exibem os achados após a edição (e fazem uma passada mais profunda no Stop, onde houver suporte). O Grok Build analisa após a edição para preparar o Stop e então exibe no Stop; o stdout de PostToolUse nunca chega ao modelo. O Cursor bloqueia gravações propostas ruins antes que sejam aplicadas.

Superfícies de hook instaladas:

- Claude Code: `.claude/settings.local.json` (ignorado pelo git, local da máquina) executa `${CLAUDE_PROJECT_DIR}/.claude/skills/impeccable/scripts/impeccable hook`. Um hook movido para o `settings.json` compartilhado é respeitado no lugar em que está.
- GitHub Copilot: `.github/hooks/impeccable.json` (commitado, compartilhado pela Copilot CLI e pelo agente na nuvem) executa `.github/skills/impeccable/scripts/impeccable hook`. A Copilot CLI o ativa assim que o arquivo estiver no branch padrão do repositório e a pasta for confiável.
- Cursor: `.cursor/hooks.json` executa `.cursor/skills/impeccable/scripts/impeccable hook-before-edit`.
- Codex: `.codex/hooks.json` executa `.agents/skills/impeccable/scripts/impeccable hook`, com um irmão `commandWindows` que chama `impeccable.cmd` para o cmd.exe.
- Grok Build: `.grok/hooks/impeccable.json` executa `.grok/skills/impeccable/scripts/impeccable hook`. Requer `/hooks-trust` ou `--trust`. Os achados chegam ao modelo no Stop, não após cada edição.

Todo comando passa pelo launcher distribuído no diretório `scripts/` da skill (`impeccable`, ou `impeccable.cmd` no Windows), protegido de modo que um launcher ausente seja um no-op silencioso. O launcher executa o binário do motor que vem ao lado dele, ou baixa a versão fixada uma única vez para `~/.impeccable/bin/`. Nenhum Node ou outro runtime é necessário para o hook ou para a skill.

No Claude Code, os hooks de comando instalados são executados independentemente da aprovação de ferramentas do modelo. Portanto, o primeiro evento de edição ou de Stop pode baixar e armazenar em cache o motor mesmo que a sessão negue o comando do launcher ao modelo. Revise os hooks instalados antes de execuções não supervisionadas; para desativar todos os hooks do Claude Code em uma execução, passe `--settings '{"disableAllHooks": true}'`. Veja as [orientações de segurança de hooks do Claude Code](https://code.claude.com/docs/en/hooks#security-considerations).

O instalador preserva entradas de hook e configurações não relacionadas. Se um manifesto de hook estiver malformado, install/update é abortado por padrão; execute novamente com `--force` para fazer backup do arquivo malformado como `.bak` e substituí-lo.

Em um `install`/`update` interativo, o Impeccable explica o hook e oferece instalá-lo (padrão: sim). Sua escolha é lembrada por desenvolvedor no `.impeccable/config.local.json` ignorado pelo git, para que você não seja perguntado de novo; `--no-hooks` pula a instalação naquela execução sem registrar nada. As configurações de ciclo de vida do hook ficam sob a chave `hook` de `.impeccable/config.json`; as exclusões do detector ficam sob `detector`, compartilhadas por `/impeccable hooks` e `npx impeccable detect`.

Para depuração, defina `hook.auditLog` em `.impeccable/config.json` com um caminho (ou a variável de ambiente legada `IMPECCABLE_HOOK_LOG`) para escrever uma linha NDJSON por invocação do hook. Deixe sem definir no uso normal.

## Caminho de construção: comp primeiro ou código primeiro

Quando uma nova superfície é projetada, o Impeccable ou gera primeiro um comp (mockup) de alta fidelidade e constrói para corresponder a ele, ou constrói direto em código, com a ambição escrita em um contrato de direção exclusivo de desenvolvimento no briefing da superfície e verificada no acabamento. Comp primeiro compõe com mais ousadia e leva mais tempo; código primeiro é mais enxuto e rápido. `/impeccable init` pergunta uma vez e registra a resposta como `buildPath` em `.impeccable/config.json`:

```json
{ "buildPath": "comp" }
```

Os valores são `comp` e `code`, e nada mais é lido. Defina-o no `.impeccable/config.local.json` ignorado pelo git para sobrescrever, em uma máquina, o valor commitado pela equipe — que é o que você quer quando o seu harness não tem geração de imagens. Em um monorepo, commite-o uma vez na raiz do repositório, e qualquer workspace que queira algo diferente define o seu próprio. A escolha só aparece onde há geração de imagens disponível, já que sem ela não há o que transformar em comp.

Você não precisa executar `init` de novo para defini-lo em um projeto anterior a essa configuração, nem precisa editar o arquivo à mão. O que estiver registrado é um padrão, não uma trava: toda página de decisão traz um alternador no rodapé, e alterná-lo vale apenas para aquela sessão. Alterne-o em um projeto que não registrou nada e o Impeccable pergunta uma vez, após a rodada, se deve manter a escolha, e então escreve a sua resposta. Esse é todo o caminho de migração para um projeto existente: use o alternador quando o padrão estiver errado e responda à pergunta que vier em seguida.

O Codex exige uma etapa da plataforma que o Impeccable não pode pular com segurança: abra `/hooks` após a instalação ou atualização e aprove o hook do projeto. Não existe fluxo de instalação via marketplace/plugin do Codex para este hook.

Documentação completa do hook: [impeccable.style/docs/hooks](https://impeccable.style/docs/hooks).

A passada no Stop suprime achados pré-existentes confirmados quando há uma linha de base verificada anterior à edição (atualmente, resultados de Edit/Write do Claude para análises de texto). Os demais achados são marcados como novos ou de atribuição desconhecida; desconhecido não é evidência de que a sua sessão causou o problema. Análises explícitas com `detect` permanecem inalteradas.

Os comandos de cópia manual são instruções de fallback/depuração. O caminho normal é:

```bash
npx impeccable install
npx impeccable update
```

## Modo live e sites de produção

O modo live edita um checkout local por meio de um servidor de desenvolvimento ou de HTML estático local. Injetar o helper HTTP de localhost dele em um site de produção implantado, incluindo um site HTTPS, não é suportado. Não desative a segurança do navegador nem enfraqueça a CSP de produção para fazê-lo funcionar.

Use o modo live apenas em projetos que você confia em executar localmente. Aplicar edições de copy executa automaticamente, em um shell e com as suas permissões de usuário, o comando opcional `scripts["impeccable:manual-edit-validate"]` do `package.json`; revise esse script antes de usar o modo live em um checkout desconhecido.

Para inspecionar produção, use `npx impeccable detect https://example.com` ou a extensão de navegador. Elas inspecionam a página renderizada; não oferecem edição de variantes ao vivo nem gravam alterações de volta no seu código-fonte.

## CLI

O Impeccable inclui uma CLI independente para detectar antipadrões sem um harness de IA. `npx impeccable` é um pequeno shim que executa o mesmo binário do motor que a skill usa (instalado como dependência opcional específica da plataforma, ou baixado uma vez para `~/.impeccable/bin/`); o Node só é necessário para o próprio `npx`, e você também pode baixar o binário diretamente e colocá-lo no seu PATH.

```bash
npx impeccable detect src/                   # scan a directory
npx impeccable detect index.html             # scan an HTML file
npx impeccable detect https://example.com    # scan a URL (uses an installed Chrome, Chromium, or Edge)
npx impeccable detect --json .               # CI-friendly JSON output
npx impeccable detect --no-config src/       # raw scan, ignoring project config/context
npx impeccable ignores list                  # show detector ignores
npx impeccable ignores add-file "src/legacy/**"
npx impeccable ignores add-value overused-font Inter --reason "Brand font"
```

O detector identifica 59 problemas determinísticos, abrangendo "AI slop" (bordas em abas laterais, gradientes roxos, easing com quique, brilhos escuros) e qualidade geral de design (comprimento de linha, padding apertado, alvos de toque pequenos, níveis de título pulados e mais).

Os achados legíveis por humanos são diagnósticos escritos no stderr, então redirecione-os com `2> findings.txt`. Use `--json` para resultados legíveis por máquina no stdout. Saída `0` significa que a análise terminou sem achados primários, saída `2` significa que terminou com achados primários e saída `1` significa que pelo menos um alvo solicitado não pôde ser analisado; falhas operacionais têm precedência em uma análise parcial de múltiplos alvos. Análises de URL inspecionam o DOM renderizado, o layout computado e as folhas de estilo vinculadas acessíveis; a segurança do navegador ainda impede a leitura de CSS de outra origem sem CORS. Uma execução limpa do detector é evidência, não prova, de qualidade visual ou de acessibilidade: ela não substitui a inspeção da experiência renderizada nos viewports relevantes.

Por padrão, `detect` respeita a mesma configuração de detector de `.impeccable/config.json` e `.impeccable/config.local.json` usada pelo hook de design: `detector.ignoreRules`, `detector.ignoreFiles`, `detector.ignoreValues` e `detector.designSystem.enabled`. Configurações de ciclo de vida do hook, como `hook.enabled`, afetam apenas a execução automática do hook.

Para uma dispensa que deve acompanhar um arquivo em vez da configuração do repositório, adicione um comentário inline no arquivo: `<!-- impeccable-disable overused-font: exported brand doc -->`. O marcador funciona em qualquer sintaxe de comentário, vale para o arquivo inteiro (ou para uma linha, com `impeccable-disable-line` / `impeccable-disable-next-line`) e é ignorado com `--no-inline-ignores` ou `--no-config`.

Documentação completa do detector: [impeccable.style/docs/detector](https://impeccable.style/docs/detector).

## Ferramentas suportadas

- [Cursor](https://cursor.com)
- [Claude Code](https://claude.ai/code)
- [GitHub Copilot](https://github.com/features/copilot)
- [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness)
- [Gemini CLI](https://github.com/google-gemini/gemini-cli)
- [Codex CLI](https://github.com/openai/codex)
- [Grok Build](https://x.ai/cli)
- [Hermes Agent](https://hermes-agent.nousresearch.com)
- [OpenCode](https://opencode.ai)
- [Pi](https://pi.dev)
- [Kiro](https://kiro.dev)
- [Trae](https://trae.ai)
- [Rovo Dev](https://www.atlassian.com/software/rovo)
- [Qoder](https://qoder.com)
- [Mistral Vibe](https://docs.mistral.ai/vibe/code/overview)
- [Veto](https://github.com/oleg-koval/veto)
- [Google Antigravity](https://antigravity.google)

## Comunidade e ecossistema

Participe das conversas da comunidade e do ecossistema:

- GitHub Discussions: registre bugs, peça funcionalidades e ajude quem está chegando.
- [Impeccable no npm](https://www.npmjs.com/package/impeccable): obtenha a CLI, acompanhe os lançamentos e dê uma estrela ao pacote.
- Siga @pbakaus no Twitter para notas de lançamento, exemplos de relatórios de lint e vídeos com destaques das novas regras.

## Como contribuir

Veja [DEVELOP.md](docs/DEVELOP.md) para as diretrizes de contribuição e as instruções de build.

## Licença

Apache 2.0. Veja [LICENSE](LICENSE).

---

Criado por [Paul Bakaus](https://www.paulbakaus.com)
