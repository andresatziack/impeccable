Configuração única do projeto para o modo live. Carregado a partir de [live.md](live.md) somente quando `impeccable live` informa `config_missing` / `config_invalid`, quando `configDrift` precisa ser tratado ou quando a configuração não tem `cspChecked`. Não faz parte do caminho crítico de cada sessão.

## Escreva a configuração

Crie o arquivo no `path` informado pela inicialização (padrão `.impeccable/live/config.json`):

```json
{
  "files": ["<path-or-glob>", "<path-or-glob>", ...],
  "exclude": ["<optional-glob>", ...],
  "insertBefore": "</body>",
  "commentSyntax": "html",
  "cspChecked": true
}
```

`files` é o alvo da injeção: **os arquivos HTML que o navegador de fato carrega**, não necessariamente o código-fonte (rastreado vs gerado não importa aqui; o wrap tem sua própria proteção contra arquivos gerados). As entradas são caminhos literais ou globs. `exclude` (opcional) ignora arquivos que um glob de `files` incluiria (templates de e-mail, fixtures de demonstração). `cspChecked` registra que a etapa de CSP abaixo já foi executada; ausente na primeira configuração.

**Caminhos excluídos de forma rígida (não podem ser sobrescritos):** `**/node_modules/**` e `**/.git/**`; injetar ali instrumentaria código de terceiros.

**Sintaxe de glob:** `**` corresponde a qualquer número de segmentos (inclusive zero), `*` corresponde dentro de um segmento, `?` corresponde a um caractere. Os caminhos são relativos à raiz do projeto, com barras normais.

| Framework | `files` | `insertBefore` | `commentSyntax` |
|-----------|---------|----------------|-----------------|
| SPA com um único shell (Vite / React / HTML puro) | `["index.html"]` | `</body>` | `html` |
| Next.js (App Router) | `["app/layout.tsx"]` | `</body>` | `jsx` |
| Next.js (Pages) | `["pages/_document.tsx"]` | `</body>` | `jsx` |
| Nuxt | `["app.vue"]` | `</body>` | `html` |
| Svelte / SvelteKit | `["src/app.html"]` | `</body>` | `html` |
| TanStack Router (SPA, Vite) | `["index.html"]` | `</body>` | `html` |
| TanStack Start (SSR) | `["src/routes/__root.tsx"]` | `<Scripts` | `jsx` |
| Astro | `[" <root layout .astro>"]` | `</body>` | `html` |
| Várias páginas (HTML separado por rota) | glob `["public/**/*.html"]` sobre o diretório servido | `</body>` | `html` |

Escolha uma âncora que exista em todos os arquivos (`</body>` funciona quase sempre); `insertAfter` insere depois de uma linha. Para sites de várias páginas, prefira um glob para que novas páginas sejam incluídas automaticamente. Para sites cujas páginas são reconstruídas por um gerador, a injeção sobrevive somente até a próxima regeneração: execute `impeccable live` de novo após cada build (o accept não é afetado; ele escreve no código-fonte verdadeiro pelo fluxo de fallback).

**Adaptadores de framework (detectados automaticamente no momento da injeção).** Toda injeção registra o que escreveu em `.impeccable/live/inject-journal.json`; a próxima injeção ou remoção corrige os artefatos deixados por uma falha ou por uma parada no diretório errado. SvelteKit, Nuxt e TanStack Start renderizam o shell do documento no servidor, então um `<script>` cru no template de entrada não será executado de forma confiável; `impeccable live-inject` os detecta e encaminha para um adaptador dedicado (SvelteKit: componente raiz somente de desenvolvimento a partir de `+layout.svelte`; Nuxt: plugin `.client.ts` somente de desenvolvimento; TanStack Start: um componente `ImpeccableLiveRoot` gerado, somente de desenvolvimento, em `__root`). O valor de `files` continua sendo uma pista válida para detecção/CSP, mas não é o local literal de inserção. Uma SPA simples com TanStack Router segue o caminho básico do Vite.

## Desvio de configuração

A cada inicialização, o projeto é varrido em busca de arquivos HTML sob raízes comuns de páginas (`public/`, `src/`, `app/`, `pages/`) que a lista `files` resolvida não cobre; eles aparecem como `configDrift.orphans` com uma dica. Diga ao usuário uma vez por sessão quais arquivos não estão cobertos e ofereça adicioná-los ou trocar `files` por um glob. Nunca atualize a configuração automaticamente; quem decide é o usuário. `configDrift` é `null` quando não há desvio.

## Detecção de CSP (somente na primeira vez)

Mantenha todas as permissões abaixo restritas ao desenvolvimento, incluindo edições manuais de middleware e de meta tags. Não altere o CSP de um site de produção já implantado para carregar o helper de localhost; veja [live.md](live.md) para alternativas de inspeção em produção.

Se `config.cspChecked === true`, pule esta seção inteira; o usuário já foi perguntado uma vez.

```bash
.kiro/skills/impeccable/scripts/impeccable detect-csp
```

Saída `{ shape, signals }`; o shape nomeia o *mecanismo de patch*, então um único modelo cobre muitos frameworks:

- **`null`**: sem CSP; escreva a configuração com `cspChecked: true` e pare aqui.
- **`append-arrays`**: CSP como arrays estruturados de diretivas; corrigível automaticamente (helpers de monorepo com `additionalScriptSrc`/`additionalConnectSrc`, SvelteKit `kit.csp.directives`, Nuxt `nuxt-security`).
- **`append-string`**: CSP como uma string de valor literal; corrigível automaticamente (`next.config.*` `headers()` inline, Nuxt `routeRules`).
- **`middleware`** / **`meta-tag`**: detectado, mas não corrigido automaticamente. Mostre ao usuário os arquivos detectados, peça que ele adicione `http://localhost:8400` a `script-src` e `connect-src` manualmente, então marque `cspChecked: true` e prossiga.

### Pedido de consentimento (use esta redação)

> **Patch de CSP necessário.** Detectei no seu projeto uma Content Security Policy que bloqueia `http://localhost:8400`: o seletor do modo live não carrega sem uma permissão. Esta é a alteração que eu faria:
>
> ```diff
> [file: <patchTarget>]
> [exact diff, 2-5 lines]
> ```
>
> Ela é protegida por `NODE_ENV === "development"`, então a entrada extra só aparece em desenvolvimento e nunca chega à produção. Você pode removê-la a qualquer momento revertendo este arquivo. Aplicar? [s/n]

Em caso de "não": pule o patch, avise que o live não vai funcionar até a permissão ser adicionada manualmente e ainda assim escreva `cspChecked: true` (a pergunta já foi feita). Em caso de "sim": aplique o patch do shape abaixo e então escreva `cspChecked: true`.

### append-arrays

Declare perto do topo do arquivo que contém os arrays de CSP e então acrescente `...__impeccableLiveDev` aos arrays de script-src e connect-src:

```ts
// Dev-only allowance so impeccable live mode can load. Guarded by NODE_ENV.
const __impeccableLiveDev =
  process.env.NODE_ENV === "development" ? ["http://localhost:8400"] : [];
```

Por framework: Next.js + helper de monorepo: edite o `next.config.*` *do app* (não o helper compartilhado), acrescentando a `additionalScriptSrc` / `additionalConnectSrc`. SvelteKit: `svelte.config.js`, `kit.csp.directives['script-src']` e `['connect-src']`. Nuxt + nuxt-security: `nuxt.config.*`, `security.headers.contentSecurityPolicy['script-src']` e `['connect-src']`. Saídas de referência: [nextjs-turborepo/expected-after-patch.ts](https://github.com/pbakaus/impeccable/blob/8dac6ae7e020c43ab10ce9b41939f6fd42627b96/tests/framework-fixtures/nextjs-turborepo/expected-after-patch.ts), [sveltekit-csp/expected-after-patch.js](https://github.com/pbakaus/impeccable/blob/8dac6ae7e020c43ab10ce9b41939f6fd42627b96/tests/framework-fixtures/sveltekit-csp/expected-after-patch.js). Idempotência: se `__impeccableLiveDev` já existir no arquivo, o patch já foi aplicado; apenas marque `cspChecked: true`.

### append-string

Patch em dois pontos: declare uma string somente de desenvolvimento e interpole-a no valor do CSP nas duas diretivas (com espaço inicial para que concatene corretamente; converta os literais em template strings como parte da edição):

```ts
// Dev-only allowance so impeccable live mode can load.
const __impeccableLiveDev =
  process.env.NODE_ENV === "development" ? " http://localhost:8400" : "";
```

- `script-src 'self' 'unsafe-inline'` passa a ser `` `script-src 'self' 'unsafe-inline'${__impeccableLiveDev}` ``
- `connect-src 'self'` passa a ser `` `connect-src 'self'${__impeccableLiveDev}` ``

Por framework: Next.js com `headers()` inline em `next.config.*`; Nuxt `routeRules['/**'].headers['Content-Security-Policy']` em `nuxt.config.*`. Saídas de referência: [nextjs-inline-csp/expected-after-patch.js](https://github.com/pbakaus/impeccable/blob/8dac6ae7e020c43ab10ce9b41939f6fd42627b96/tests/framework-fixtures/nextjs-inline-csp/expected-after-patch.js), [nuxt-csp/expected-after-patch.ts](https://github.com/pbakaus/impeccable/blob/8dac6ae7e020c43ab10ce9b41939f6fd42627b96/tests/framework-fixtures/nuxt-csp/expected-after-patch.ts).

## Solução de problemas

Se o usuário disse "não" ao patch de CSP e depois relatar que o live não funciona: o CSP de desenvolvimento dele bloqueia `http://localhost:8400`. Exclua `cspChecked` de `.impeccable/live/config.json` e execute `impeccable live` de novo; a configuração pergunta outra vez.

Após a configuração, execute `impeccable live` de novo.
