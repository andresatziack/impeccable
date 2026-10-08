# Instalação do Impeccable no Kiro (sem NPX)

Este guia instala a skill **Impeccable em português** no Kiro (IDE, CLI ou Web) sem usar `npm` nem `npx`. Antes de seguir, veja o [relatório de segurança](SEGURANCA.md) para entender o que é instalado.

## O que vai ser instalado

O Impeccable tem duas partes:

| Parte | O que é | Onde fica |
|---|---|---|
| **Skill** | `SKILL.md` + 41 referências (+ 4 em `degraded/`) traduzidas, que ensinam o agente do Kiro a projetar interfaces | `.kiro/skills/impeccable/` |
| **Motor** | Um binário nativo (Rust, ~18 MB, sem dependências) que faz a detecção de antipadrões, o modo live, o sorteio de direções etc. | `.kiro/skills/impeccable/scripts/bin/<so>-<arq>/impeccable` |

A skill chama o motor pelo launcher `scripts/impeccable` (ou `scripts/impeccable.cmd` no Windows). Quando o binário já está no lugar certo, **o launcher não baixa nada**. É isso que dispensa o NPX.

Este repositório já traz a pasta `.kiro/skills/impeccable/` pronta para o Kiro, com o frontmatter compatível (`name: impeccable`) e todos os caminhos apontando para `.kiro/`. Você só precisa copiá-la e colocar o motor dentro.

## Pré-requisitos

- `git`
- **Opção A (recomendada, máxima confiança):** Rust estável via [rustup](https://rustup.rs). No Windows, também o "Desktop development with C++" do Visual Studio Build Tools.
- **Opção B (mais rápida):** `curl` e `sha256sum`. No macOS, o `shasum` já vem instalado. No Windows, use `certutil`.

## 1. Baixe o repositório

```bash
git clone https://github.com/andresatziack/impeccable.git ~/impeccable-src
cd ~/impeccable-src
git checkout kiro/seguranca-traducao-instalacao   # branch com a tradução (ou main, depois do merge)
```

A versão do motor que a skill espera está em `.kiro/skills/impeccable/scripts/VERSION` (hoje, `0.1.11`).

## 2. Obtenha o motor

### Opção A: compilar do código-fonte

Compile a mesma versão da release oficial. A tag vem do repositório original:

```bash
cd ~/impeccable-src
git fetch https://github.com/pbakaus/impeccable.git tag engine-v0.1.11
git worktree add ../impeccable-engine engine-v0.1.11
cd ../impeccable-engine

# Opcional: corrige a vulnerabilidade média do rustls (veja SEGURANCA.md)
cargo update -p rustls --precise 0.23.45

cargo build --release -p impeccable
./target/release/impeccable engine-probe   # deve imprimir: impeccable-engine 0.1.11
```

- **Se você pulou o `cargo update`:** use `cargo build --release --locked -p impeccable`. Assim o build usa exatamente as versões do `Cargo.lock` que foram auditadas.
- **Tempo:** cerca de 2 a 3 minutos.
- **Resultado:** `target/release/impeccable`, ou `target\release\impeccable.exe` no Windows.
- **Rede:** o `cargo` só baixa crates do crates.io. O `rustup` pode instalar sozinho o alvo `wasm32`, que vem do `rust-toolchain.toml`.

### Opção B: baixar o binário oficial e verificar o hash

```bash
# Escolha: linux-x64 | linux-arm64 | darwin-arm64 | darwin-x64 | windows-x64.exe
ALVO=linux-x64
curl -fLO https://github.com/pbakaus/impeccable/releases/download/engine-v0.1.11/impeccable-$ALVO
sha256sum impeccable-$ALVO       # macOS: shasum -a 256 impeccable-$ALVO
```

Compare o resultado com os hashes oficiais. Eu os conferi em 08/10/2026, e o do `linux-x64` é idêntico ao do pacote publicado no NPM:

| Alvo | SHA-256 |
|---|---|
| `linux-x64` | `0221607e1f535af937ea267c347b1f90233b85dc2563cbd2eeefdf42e0e5c594` |
| `linux-arm64` | `a3036c5c2da08ac6a14fa4886e96a8b1651beab332af75cbe328092528d6e40a` |
| `darwin-arm64` | `7427918d6e75507401a1b7b691eefe58a7c01a2fc63b712016ffc5a0ac1c05e6` |
| `darwin-x64` | `dfd1b6a5b719273bf703642e8ce60152a696cd1a76870ddab3cefa57bcce86d0` |
| `windows-x64.exe` | `605b5b442d2a65d270ef989de13371444849526249f511d397ca7424c184db49` |

Se o hash não bater, **não use o arquivo**.

- **No macOS:** remova a quarentena com `xattr -d com.apple.quarantine impeccable-$ALVO`.
- **No Windows:** o `.exe` oficial é assinado digitalmente.

## 3. Instale a skill no projeto

A instalação recomendada é **por projeto**. Todos os comandos da skill usam o caminho `.kiro/skills/impeccable/scripts/impeccable` relativo à raiz do projeto, e é também o único escopo que o Kiro Web enxerga.

**Linux / macOS**

```bash
PROJETO=~/meu-projeto                 # raiz do projeto onde você vai usar a skill
SO_ARQ=linux-x64                      # linux-arm64 | darwin-arm64 | darwin-x64
BINARIO=../impeccable-engine/target/release/impeccable   # opção A (ou o arquivo baixado na opção B)

cd ~/impeccable-src
mkdir -p "$PROJETO/.kiro/skills"
cp -R .kiro/skills/impeccable "$PROJETO/.kiro/skills/"
mkdir -p "$PROJETO/.kiro/skills/impeccable/scripts/bin/$SO_ARQ"
cp "$BINARIO" "$PROJETO/.kiro/skills/impeccable/scripts/bin/$SO_ARQ/impeccable"
chmod +x "$PROJETO/.kiro/skills/impeccable/scripts/impeccable" \
         "$PROJETO/.kiro/skills/impeccable/scripts/bin/$SO_ARQ/impeccable"
```

**Windows (PowerShell)**

```powershell
$Projeto = "C:\caminho\meu-projeto"
$Binario = "..\impeccable-engine\target\release\impeccable.exe"   # ou o impeccable-windows-x64.exe baixado
cd $HOME\impeccable-src
New-Item -ItemType Directory -Force "$Projeto\.kiro\skills" | Out-Null
Copy-Item -Recurse .kiro\skills\impeccable "$Projeto\.kiro\skills\"
New-Item -ItemType Directory -Force "$Projeto\.kiro\skills\impeccable\scripts\bin\windows-x64" | Out-Null
Copy-Item $Binario "$Projeto\.kiro\skills\impeccable\scripts\bin\windows-x64\impeccable.exe"
```

**Não faça commit do binário.** Acrescente esta linha ao `.gitignore` do seu projeto:

```gitignore
.kiro/skills/impeccable/scripts/bin/
```

A pasta da skill em si (os `.md` e scripts) pode, e deve, ser commitada, para que todos da equipe e o Kiro Web a recebam.

### Instalação global (opcional, só IDE/CLI)

Para ter a skill em todos os projetos, copie a pasta para `~/.kiro/skills/impeccable` (Windows: `%USERPROFILE%\.kiro\skills\impeccable`) e coloque o binário em `scripts/bin/<so>-<arq>/` do mesmo jeito.

As referências citam `.kiro/skills/impeccable/scripts/impeccable` como caminho de fallback. O `SKILL.md` instrui o agente a usar o diretório real da skill, mas, se algum comando falhar com "arquivo não encontrado", prefira a instalação por projeto. Se a mesma skill existir nos dois lugares, a do projeto vence.

### Kiro Web

O Kiro Web só lê `.kiro/skills/` **do repositório**. Como o binário fica fora do git, na primeira execução o launcher baixa o motor da release oficial do GitHub para o cache `~/.impeccable/bin/0.1.11/` do sandbox, e **recusa** o download se o SHA-256 não bater. Se você não quiser nenhum download, a alternativa é commitar o binário `linux-x64` (~18 MB) em `scripts/bin/linux-x64/` e remover a linha correspondente do `.gitignore`.

## 4. Privacidade (recomendado)

O motor tem uma checagem de atualização e uma telemetria anônima (detalhes no [SEGURANCA.md](SEGURANCA.md#comunicação-de-rede)). Para desligar as duas, exporte as variáveis no perfil do seu shell. O Kiro herda o ambiente do terminal ou do sistema.

```bash
# ~/.bashrc, ~/.zshrc etc.
export DO_NOT_TRACK=1
export IMPECCABLE_NO_UPDATE_CHECK=1
```

No Windows, rode `setx DO_NOT_TRACK 1` e `setx IMPECCABLE_NO_UPDATE_CHECK 1` e depois reinicie o Kiro.

Para desligar só a checagem de atualização em um projeto, sem depender do ambiente (vale também no Kiro Web), crie `.impeccable/config.json` na raiz do projeto:

```json
{ "updateCheck": false }
```

## 5. Verifique a instalação

Na raiz do projeto, rode estes três comandos:

```bash
.kiro/skills/impeccable/scripts/impeccable engine-probe   # impeccable-engine 0.1.11
.kiro/skills/impeccable/scripts/impeccable context        # imprime NO_PRODUCT_MD... (normal num projeto novo)
.kiro/skills/impeccable/scripts/impeccable detect src/    # varre seus arquivos de UI
```

No Windows, sem `sh`, use `.kiro\skills\impeccable\scripts\impeccable.cmd` no lugar.

Depois abra uma **nova sessão** no Kiro (as skills são carregadas ao iniciar a sessão) e confira:

- **Kiro CLI:** `/context show` deve listar a skill `impeccable`.
- **Qualquer cliente:** digite `/impeccable`. O agente deve mostrar o menu de comandos em português.
- **Primeiro uso real:** rode `/impeccable init`. Ele entrevista você e grava o `PRODUCT.md` do projeto.

A skill também é ativada **automaticamente** quando você pede trabalho de interface ("melhore o layout desta página", "faça uma auditoria de acessibilidade"), porque o Kiro compara o pedido com a `description` dela, que está em português e contém os termos-chave em inglês.

## 6. Ajustes específicos do Kiro

### Permitir os comandos da skill sem confirmação (IDE/CLI)

O agente executa o launcher muitas vezes por sessão. Para não precisar aprovar cada execução, adicione esta regra a `~/.kiro/settings/permissions.yaml`:

```yaml
rules:
  - capability: shell
    match: [".kiro/skills/impeccable/scripts/impeccable *"]
    effect: allow
```

Essa regra libera **só** o launcher da skill; outros comandos continuam pedindo aprovação. No Kiro Web isso não é necessário: o agente roda em um sandbox isolado, sem prompts de aprovação.

### Detector automático após cada edição (opcional)

O comando `/impeccable hooks on` instala hooks para Claude Code, Codex, Cursor, Grok e Copilot, mas **não para o Kiro**. Sem hook, a skill já recebe a diretiva `MANUAL_DETECTOR_REQUIRED` e roda o detector uma vez no fim de cada trabalho de UI. Isso basta para a maioria dos casos.

Se quiser o detector rodando a cada arquivo de UI que o agente salvar, crie `.kiro/hooks/impeccable-detector.json` no projeto:

```json
{
  "version": "v1",
  "hooks": [
    {
      "name": "Impeccable: detector de design",
      "description": "Depois que o agente salva um arquivo de UI, roda o detector de antipadrões do Impeccable nele.",
      "trigger": "PostFileSave",
      "matcher": "\\.(html|css|scss|sass|less|jsx|tsx|vue|svelte|astro)$",
      "action": {
        "type": "agent",
        "prompt": "Você acabou de salvar um arquivo de UI. Rode `.kiro/skills/impeccable/scripts/impeccable detect --json <arquivo salvo>` uma única vez e corrija os achados primários que forem problemas reais de design, sem refazer o restante. Se o arquivo contiver marcadores data-impeccable-* do modo live, ignore este lembrete."
      }
    }
  ]
}
```

Os gatilhos de arquivo do Kiro só disparam em edições feitas pelo agente, nunca nas suas edições manuais.

## 7. Atualizar sem NPX

**Não use `npx impeccable update`.** Ele usaria o NPM e sobrescreveria a skill traduzida pela versão em inglês. Atualize assim:

1. `cd ~/impeccable-src && git pull` e revise o diff (`git log -p`).
2. Se `.kiro/skills/impeccable/scripts/VERSION` mudar, repita o passo 2 com a nova tag `engine-v<versão>`.
3. Repita o passo 3 (copiar a pasta e o binário) em cada projeto.

**Cuidado ao sincronizar este fork com o original:** a pasta `.kiro/skills/impeccable/` é gerada a partir de `skill/`. O build (`bun run build`) e o workflow `sync-generated-output.yml`, que roda em push na `main` alterando `skill/**` ou `scripts/**`, regeneram essa pasta **em inglês** e apagam a tradução. Ao trazer mudanças do original, guarde a versão traduzida e traduza de novo apenas os arquivos que mudaram. Você também pode desativar esse workflow nas configurações de Actions do fork.

## 8. Desinstalar

```bash
rm -rf .kiro/skills/impeccable .kiro/hooks/impeccable-detector.json   # no projeto
rm -rf ~/.impeccable                                                  # cache do motor, se existir
```

Os arquivos que a skill criou no seu projeto (`PRODUCT.md`, `DESIGN.md`, `.impeccable/`) são seus. Apague-os só se não quiser mais o contexto de design.

## Observações

- **Mensagens do motor em inglês:** as diretivas que o motor imprime (`NO_PRODUCT_MD`, `MODE RULES`, `UPDATE_AVAILABLE` etc.) vêm do binário e continuam em inglês. O agente as entende normalmente e responde a você em português.
- **Só a pasta do Kiro foi traduzida:** a tradução cobre `.kiro/skills/impeccable/`. As cópias para outros agentes (`.claude/`, `.cursor/` etc.) continuam em inglês, e o motor não depende delas.
- **Títulos que ficaram em inglês:** em `reference/mode-*.md`, os títulos `## Directions` e `## Comps` ficaram em inglês de propósito, porque o motor localiza essas seções pelo texto. Também continuam em inglês os comandos, caminhos, flags e blocos de código.
