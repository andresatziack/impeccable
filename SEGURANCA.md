# Relatório de segurança: Impeccable

- **Data:** 8 de outubro de 2026
- **Commit analisado:** `778c8a7b` (branch `main` do fork `andresatziack/impeccable`)
- **Pacotes NPM analisados:** `impeccable@4.1.0` e `@impeccable/cli-linux-x64@0.1.11`
- **Motor nativo analisado:** `engine-v0.1.11`

## Veredito

**Não encontrei código malicioso.** Nada no projeto ou nos pacotes publicados no NPM rouba credenciais, abre backdoor, cria persistência, minera criptomoeda, ofusca código ou executa algo escondido durante a instalação.

O projeto **faz** algumas conexões de rede, todas documentadas, com objetivo claro e desligáveis (veja [Comunicação de rede](#comunicação-de-rede)). Também há **uma vulnerabilidade de severidade média** em uma dependência de TLS (veja [Dependências](#dependências)).

## O que o `npx impeccable` faz de verdade

Esta foi a parte mais sensível da análise, porque é o que roda no seu computador.

1. **O pacote `impeccable` (17 KB)** tem só 6 arquivos: `cli/bin/cli.js` (95 linhas), `package.json`, `LICENSE` e READMEs.
   - Não tem `preinstall`, `install`, `postinstall` nem `prepare`. **Nada roda no momento do `npm install`.** Os únicos scripts de ciclo de vida são `prepack`/`postpack`, que só rodam para o mantenedor ao publicar.
   - O `cli.js` publicado é **byte a byte idêntico** ao do repositório (SHA-256 `6ba6a0e7…`, igual à tag `cli-v4.1.0`).
2. **O `cli.js` é só um "shim".** Ele procura o binário nativo nesta ordem:
   1. a variável `$IMPECCABLE_BIN`;
   2. o pacote opcional `@impeccable/cli-<so>-<arq>`;
   3. o cache `~/.impeccable/bin/<versão>/`;
   4. um download de `github.com/pbakaus/impeccable/releases`.

   O download é verificado com SHA-256 e **recusado** se o hash faltar ou não bater ("fail closed"). Nada é gravado antes da verificação.
3. **O pacote `@impeccable/cli-linux-x64`** contém apenas o binário e o `package.json`, também sem scripts de instalação. O hash dele (`0221607e…`) é **idêntico** ao do binário da release no GitHub, que foi publicado pelo `github-actions[bot]`.
4. **As `devDependencies`** (Playwright, Puppeteer, SDKs de IA etc.) **não são instaladas** para quem usa `npx impeccable`. Elas só servem para quem desenvolve o próprio projeto.

## Procedência do código

- **O fork é idêntico ao original.** O `andresatziack/impeccable` não tem nenhum commit além dos do `pbakaus/impeccable`. Comparei com `git log upstream..main` e o resultado foi vazio.
- **O maior volume de autoria é do mantenedor.** São 1.809 commits do autor (Paul Bakaus), além de commits de bots (sincronização e Dependabot) e de colaboradores externos.
- **Os binários são compilados pelo GitHub Actions** (`release-engine.yml`):
  - todas as actions estão fixadas por SHA de commit, o que protege contra a troca de uma tag de action;
  - o executável de Windows recebe assinatura de código via Azure Artifact Signing.
- **Compilei o motor eu mesmo** a partir da tag `engine-v0.1.11` com `cargo build --release --locked`. Funcionou em 2 min 35 s e passou no `engine-probe`.
  - O hash do meu build é diferente do oficial. Isso é esperado: o oficial é estático (musl), e builds Rust não são reprodutíveis bit a bit.
  - Por isso, **compilar do fonte é a opção de maior confiança**, e é a recomendada no [guia de instalação](INSTALACAO-KIRO.md).
- **O `.sha256` vem do mesmo lugar que o binário.** Ele garante integridade (o download não veio corrompido), mas não autenticidade: se a conta do GitHub fosse comprometida, o atacante trocaria os dois arquivos.
- **Os pacotes da skill baixados por `npx impeccable install/update` são mais protegidos.** Eles têm assinatura Ed25519 verificada contra uma chave embutida no binário (`scripts/bundle-signing-keys.json`).

## O que foi analisado

| Área | Como | Resultado |
|---|---|---|
| Pacotes NPM publicados | Baixei com `npm pack`, conferi todos os arquivos, os scripts e os hashes | Limpos, iguais ao repositório |
| Launcher da skill (`scripts/impeccable`, `impeccable.cmd`) | Leitura completa | Só procura e executa o binário. Downloads verificados por SHA-256, sem `curl \| sh` |
| Motor Rust (~212 mil linhas, 16 crates) | Varredura de todo uso de rede (`ureq`, `tiny_http`, `TcpListener`), de execução de processos (`Command::new`), de URLs, de leitura de arquivos sensíveis (`.ssh`, `.aws`, `.npmrc`, cookies, carteiras), de persistência (`.bashrc`, crontab, LaunchAgents) e de telemetria | Nenhum acesso a credenciais ou persistência. Rede detalhada abaixo |
| Scripts injetados no navegador (`live-browser*.js`, `modern-screenshot.umd.js`) | Busca por `eval`, `new Function`, `atob`, `fetch`, `WebSocket`, `sendBeacon`, `document.cookie` | Sem `eval`. Todos os `fetch` vão para `http://localhost:<porta>` com token. `modern-screenshot` é uma biblioteca pública de captura de tela |
| Dependências Rust (`Cargo.lock`, 209 crates) | Todas vêm do crates.io. `cargo audit` com a base RustSec (1.295 alertas) | 1 alerta médio (abaixo) |
| Arquivos `.md` da skill (instruções para a IA) | Busca por injeção de prompt, ordens de esconder ações do usuário, exfiltração | Nada suspeito |
| Script de build (`build.rs`) | Leitura | Só gera código de cascata CSS, sem rede |

## Comunicação de rede

Todas as conexões são HTTPS para `impeccable.style` ou `github.com`, ou locais (`127.0.0.1`/`localhost`).

| Quando | Destino | O que é enviado | Como desligar |
|---|---|---|---|
| `impeccable context` (no início da sessão, no máximo 1 vez a cada intervalo, com cache) | `GET https://impeccable.style/api/version` | Nada além da requisição; só lê a versão mais nova | `IMPECCABLE_NO_UPDATE_CHECK=1` ou `"updateCheck": false` em `.impeccable/config.json` |
| `concept-seed` (sorteio de direções visuais) | `GET https://impeccable.style/api/roll?scope=…&key=…&mode=…` | Parâmetros do sorteio, sem conteúdo do seu projeto | Funciona em modo degradado se a API estiver inacessível (`IMPECCABLE_API_URL` apontando para um endereço inexistente) |
| Telemetria após a escolha de uma direção | `POST https://impeccable.style/api/chosen` | Anônimo: id do card escolhido, tipo e escopo. O código confirma que nomes e conteúdo do seu projeto não saem da máquina | `DO_NOT_TRACK=1` ou `IMPECCABLE_NO_TELEMETRY=1` |
| `generate-image` (só se você configurar uma chave) | `https://api.openai.com/v1` | O prompt da imagem e as referências, com a **sua** chave de API | Não configure a chave; a skill usa a ferramenta de imagem do próprio agente |
| Imagens de catálogo | `https://impeccable.style/worlds/cards/…` | Download de imagens públicas | `IMPECCABLE_CARD_BASE` |
| Modo live, revisão de componentes e página de decisão | Servidores locais em `127.0.0.1` | Comunicação entre o navegador e o motor, protegida por token | Não use `live` |
| Primeira execução sem binário | `github.com/pbakaus/impeccable/releases` | Download do motor, verificado por SHA-256 | Instale o binário antes (veja o guia) |

## Outros comportamentos que você deve conhecer

Nenhum destes é malicioso, mas vale saber:

- **O modo `live` edita o seu código.** Ele injeta temporariamente um `<script src="http://localhost:PORTA/live.js">` no HTML de desenvolvimento e remove ao terminar. Também pode propor um patch de CSP restrito a `NODE_ENV === "development"`, e sempre pede consentimento antes.
- **O motor executa outros programas.** Ele roda `git`, para ver arquivos alterados, e `node --check`, para validar a sintaxe de arquivos editados. Para abrir URLs, usa o navegador do sistema (`xdg-open`/`open`/`cmd start`). No modo live, pode chamar a CLI `claude`, se ela estiver instalada e você a selecionar para aplicar edições.
- **Arquivos gravados no seu projeto:** `.impeccable/` (configuração, estado e mocks), `PRODUCT.md` e `DESIGN.md`. Fora do projeto, só o cache `~/.impeccable/`.
- **As instruções da skill são um prompt para a IA.** O `SKILL.md` e as referências orientam o agente a rodar comandos do launcher. Não encontrei nenhuma instrução para agir escondido do usuário. O texto pede, pelo contrário, que o agente não atualize sem você pedir e que relate erros literalmente.

## Dependências

| Crate | Versão | Alerta | Severidade | Correção |
|---|---|---|---|---|
| `rustls` | 0.23.43 | [RUSTSEC-2026-0285](https://rustsec.org/advisories/RUSTSEC-2026-0285): mensagens de handshake TLS 1.3 aceitas entre níveis de criptografia | Média (5.3) | `>= 0.23.45` |

O risco prático é baixo: o motor só usa TLS para falar com `impeccable.style`, `github.com` e `api.openai.com`. Quem compila do fonte pode corrigir antes de compilar:

```bash
cargo update -p rustls --precise 0.23.45
```

Esse passo está incluído como opcional no guia.

## Recomendações

1. **Instale sem NPX** seguindo o [INSTALACAO-KIRO.md](INSTALACAO-KIRO.md), compilando o motor do código-fonte.
2. Exporte `DO_NOT_TRACK=1` e `IMPECCABLE_NO_UPDATE_CHECK=1` se quiser zero contato não solicitado com `impeccable.style`.
3. Fixe a versão: use um commit ou tag conhecido e só atualize depois de revisar o diff.
4. Não rode `npx impeccable update` nesta instalação. Ele usaria o NPM e sobrescreveria a skill traduzida.
