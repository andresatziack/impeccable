# Impeccable CLI

Detecte antipadrões de UI e problemas de qualidade de design pela linha de comando e instale a skill de design Impeccable no seu harness (ferramenta de agente) de programação com IA. O detector analisa arquivos HTML, CSS, JSX, TSX, Vue e Svelte com 59 regras determinísticas, incluindo sinais de UI gerada por IA, violações de acessibilidade e problemas gerais de qualidade de design.

O pacote npm é um pequeno launcher (inicializador). Ele executa o binário do motor `impeccable` para a sua plataforma, instalado junto como dependência opcional (`@impeccable/cli-<os>-<arch>`), e recorre a um cache por usuário ou a um download único quando esse pacote está ausente.

## Início rápido

```bash
# Install skills into your AI harness (Claude, Cursor, Gemini, etc.)
npx impeccable install

# Non-interactive install for a specific scope
npx impeccable install -y --providers=claude,codex --scope=project

# First command to run inside your AI harness
/impeccable init

# Update skills to the latest version
npx impeccable update

# Install or update skills without hook manifests
npx impeccable install --no-hooks

# Link skills from a Git submodule checkout
npx impeccable link --source=.impeccable --providers=claude,cursor

# List all available commands
npx impeccable help

# Scan files or directories for anti-patterns
npx impeccable detect src/

# Scan a live URL (uses an installed Chrome, Chromium, or Edge)
npx impeccable detect https://example.com

# JSON output for CI/tooling
npx impeccable detect --json src/
```

`npx impeccable skills <command>` é o namespace legado e continua funcionando.

## O que ele detecta

**Sinais de "AI Slop"**: padrões que gritam "isto foi gerado por IA":
- Bordas de destaque em abas laterais, texto em gradiente nos títulos
- Gradientes roxos/violeta e paletas de ciano sobre fundo escuro
- Modo escuro com destaques brilhantes, conflitos entre borda + border-radius

**Problemas de tipografia**: fontes usadas em excesso (Inter, Roboto), hierarquia tipográfica achatada, uma única família de fontes

**Cor e contraste**: violações de WCAG AA, texto cinza sobre fundos coloridos, preto/branco puros

**Layout e composição**: cards aninhados, espaçamento monótono, layouts com tudo centralizado

**Movimento**: easing com quique/elástico, transições de propriedades de layout

**Qualidade**: texto de corpo minúsculo, padding apertado, linhas longas demais, alvos de toque pequenos

59 regras determinísticas de detector no total. Veja o catálogo completo em [impeccable.style/slop](https://impeccable.style/slop).

## Códigos de saída

- `0`: análise concluída sem achados primários (avisos consultivos ainda podem ser listados)
- `1`: pelo menos um alvo solicitado não pôde ser analisado
- `2`: análise concluída com achados primários

Falhas operacionais têm precedência quando uma análise de múltiplos alvos é parcial. No modo JSON, o stdout continua sendo um array de achados e os diagnósticos são escritos no stderr.

## Opções

```
impeccable detect [options] [file-or-dir-or-url...]

  --json      Output findings as JSON
  --scope     Only report rules in a design domain (type, layout)
  --help      Show help
```

## Requisitos

- Node.js 22.18+ para executar `npx impeccable`. O motor em si é um binário autocontido e não precisa de runtime; a skill instalada no seu harness o chama diretamente.
- Para análises de URL, um Chrome, Chromium ou Edge instalado (defina `IMPECCABLE_BROWSER` para apontar para um deles).
- Atrás de um proxy que inspeciona TLS, os downloads confiam no repositório de certificados do seu sistema operacional, além das raízes da Mozilla incluídas. Defina `SSL_CERT_FILE` ou `SSL_CERT_DIR` para usar um pacote de CAs específico.

Ordem de busca do binário: `IMPECCABLE_BIN`, o pacote da plataforma, `~/.impeccable/bin/<version>/` e, por fim, um download da versão fixada para esse cache. Defina `IMPECCABLE_BIN` apontando para um build local para pular tudo isso.

## Parte do Impeccable

Esta CLI faz parte do [Impeccable](https://impeccable.style), um pacote de skills de design multiprovedor para ferramentas de desenvolvimento com IA. O conjunto completo inclui 24 comandos para Claude, Cursor, GitHub Copilot, Gemini, Codex, Hermes Agent, Veto e outros.

## Licença

[Apache 2.0](https://github.com/pbakaus/impeccable/blob/main/LICENSE)
