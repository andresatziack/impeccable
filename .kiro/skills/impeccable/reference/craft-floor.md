# Padrão mínimo de qualidade

Carregue isto depois que a direção estiver definida e construa sem anunciar o checklist. Um briefing fixado ou o mundo visual assumido prevalecem sobre qualquer coisa aqui; o seu próprio hábito, não. Quando o hook de design está ativo, ele já impõe as verificações mecânicas abaixo enquanto você edita: aja sobre os achados dele em vez de reauditar cada regra.

## Verifique

Cada item abaixo é uma verificação do resultado construído, não uma intenção. Execute-os juntos nas rodadas de inspeção em lote, não como idas separadas para capturas de tela; as verificações compartilham uma única renderização.

- **Contraste:** texto de corpo e de placeholder ≥4,5:1, texto grande ≥3:1. Em superfícies coloridas, tinja o texto secundário a partir daquele matiz ou da cor de primeiro plano; nunca cinza.
- **Profundidade:** sombras têm deslocamento e desfoque suave. Um halo colorido sem deslocamento é decoração.
- **Espaçamento:** grupos compactos, separação generosa, mais espaço acima de um título do que abaixo dele. Leia os valores computados.
- **Tipografia:** medida do corpo de 65–75ch, display no máximo 6rem, tracking mínimo de -0.04em, títulos balanceados, saltos óbvios de escala e peso. Rode a copy real em cada breakpoint e corrija o que transbordar.
- **Movimento:** um momento autoral, não efeitos espalhados nem uma entrada idêntica em cada seção. Ease-out exponencial a partir de um padrão já visível. Vá além de transform e opacity: blur, backdrop-filter, clip-path, mask e sombra fazem parte da paleta quando permanecem fluidos.
- **Estados:** hover, desabilitado, carregando, erro, vazio. Além de conteúdo real, controles funcionando, composição responsiva, foco de teclado.
- **Superfícies do navegador:** as partes que você não desenhou também carregam o design. Seleção de texto, o cursor de texto, barras de rolagem personalizadas, anéis de foco, deslocamento do sublinhado e os numerais em dados tabulares vêm todos com padrões do navegador que não pertencem a nenhum design system. Aplique a eles o tema da paleta. Esse é o sinal mais barato de que uma página foi construída e não montada, e o que os modelos pulam com mais regularidade.
- **Copy:** a linguagem do próprio produto. Controles nomeiam sua ação; erros nomeiam o problema e a forma de recuperação.
- **Cobertura:** cada requisito do briefing presente e encontrável em segundos.

## Recuse

Estes são os padrões da categoria, não proibições: as próprias palavras do briefing podem justificar qualquer um deles. Recorrer a um deles quando o eixo está livre significa que você não estava decidindo; reconhecer isso significa reescrever o elemento, não suavizá-lo.

Esqueletos de página:

- Cards do mesmo tamanho com ícone mais título mais texto como estrutura da página. Cards são o contêiner preguiçoso; cards aninhados estão sempre errados.
- O template de métrica no hero: número grande, rótulo pequeno, estatísticas de apoio, cor de destaque.
- Um kicker ou eyebrow (sobretítulo) acima de um título. Este é uma proibição, não um padrão: nenhum briefing o justifica. O título carrega seu próprio peso; apague o rótulo e deixe o título falar.
- Números de seção (01 / 02 / 03), a menos que a própria sequência carregue informação de que o leitor precisa.
- Um modal para uma tarefa que não precisa nem de interrupção nem de foco protegido.

Hábitos de superfície:

- Texto com gradiente. A ênfase vem do peso ou do tamanho.
- Vidro e blur como decoração em vez de como um efeito específico.
- Um `border-left` ou `border-right` colorido acima de 1px em cards, itens de lista, destaques ou alertas.
- Sombras duras deslocadas (`box-shadow: 4px 4px 0`) fora de um mundo visual que seja de fato neobrutalista. A sombra em bloco sem desfoque é uma fantasia, não um sistema de profundidade; um mundo visual que não a escolheu nunca a justifica como padrão.
- Sparklines, anéis de progresso e retângulos arredondados com sombra suave ocupando o lugar de conteúdo.
- Monoespaçada como fantasia de "técnico" em vez de para código, dados ou medidas.
- Uma fonte de display do sistema (Impact, Arial Black, a sans da plataforma) como voz de display de uma página com mundo visual próprio. Obtenha e hospede você mesmo uma fonte cujo caráter combine com o letreiramento aprovado; a fonte instalada mais próxima é uma falha, não um fallback.
- Glifos Unicode ou emoji ocupando o lugar de um sistema de ícones. Ícones são desenhados, de uma biblioteca real ou em SVG autoral, com um traço e um peso consistentes.
- Máscaras geométricas ocupando o lugar de contornos orgânicos. Um recorte em círculo, polígono ou radial-gradient aproximando a borda de um objeto fotográfico é a versão barata do efeito e fica pior do que omiti-lo. Derive uma máscara alfa da imagem real ou produza um asset recortado.
- Claro ou escuro escolhido pela categoria. Escolha pela cena de uso: quem, onde, sob que luz ambiente.

O padrão mínimo garante a mecânica; ele nunca escolhe a direção. Com todas as verificações em verde, invista a página no mundo visual assumido e, quando estiver dividido entre refinado e comprometido, comprometa-se.
