# Shape

Descubra o que deve ser feito e como deve funcionar e, em seguida, devolva um briefing de design confirmado, sem código.

## Fase 1: Entrevista de descoberta

Ainda não escreva código nem escolha uma direção visual.

### Cadência

- Use a ferramenta de perguntas estruturadas quando disponível; caso contrário, pergunte e pare.
- Faça duas ou três perguntas relacionadas por rodada e depois espere. Uma rodada é o padrão; acrescente uma segunda apenas quando as respostas revelarem uma lacuna relevante.
- Não despeje um questionário, não repita fatos já resolvidos nem transforme fatos óbvios em menus. Afirme a leitura provável e convide à correção.
- Um prompt escasso exige pelo menos uma rodada de respostas. Um prompt preciso pode precisar apenas de uma confirmação compacta.

### Rodada 1: propósito, pessoas e resultado

Escolha as duas ou três perguntas que mais mudam o resultado:

- Para que serve esta superfície ou funcionalidade, e que problema ela precisa resolver?
- Quem, especificamente, chega até ela, em que situação e em que estado de espírito?
- Qual é a principal coisa que essas pessoas precisam entender ou fazer? Como seria o sucesso?
- O que é verdadeiro exclusivamente aqui e que um produto vizinho ou um template genérico não poderia reivindicar?

### Rodada 2: material, comportamento e limites

Faça esta rodada apenas para decisões relevantes ainda não resolvidas:

- Que conteúdo real, evidências, dados e assets a experiência precisa carregar? Quais são as faixas realistas de mínimo, típico e máximo?
- Quais estados e transições importam: primeira execução, vazio, carregamento, erro, sucesso, permissões, transbordamento ou uso avançado?
- Qual é a fidelidade, a amplitude e a interatividade pretendidas: exploração, tela pronta para produção, fluxo completo ou superfície mais ampla?
- O que deve permanecer intocado? O que faria o resultado parecer errado mesmo que parecesse bem-acabado?
- Quais restrições de plataforma, framework, desempenho, acessibilidade, localização ou entrega são obrigatórias?

Nunca peça valores de CSS nem trilhas estéticas prontas. O new-work é dono das escolhas de mundo visual e de conceito.

## Fase 2: Resolva a direção de design

Para novas superfícies, expansão de marca ou substituição, siga [new-work.md](new-work.md) passando pela autoridade visual, por qualquer oficina de mundo visual e pela escolha de conceito. Reaproveite a descoberta e retorne antes do contrato, da persistência ou da implementação dele. Dentro de um mundo já estabelecido, use o processo de conceito dele apenas quando a composição ou a interação continuarem significativamente em aberto.

## Fase 3: Escreva o briefing

Escreva o menor briefing útil:

1. **Função e público:** quem chega, seu contexto, sua necessidade e o modo do visitante.
2. **Resultado e prova:** tarefa/ação principal, sucesso, evidência real e verdade específica do produto.
3. **Direção selecionada:** autoridade visual, tese estrutural/de interação, sequência, momento focal e consequência para a implementação.
4. **Escopo e limites:** fidelidade, amplitude, interatividade, alvo nomeado, o que permanece intocado e antiobjetivos explícitos.
5. **Estados e faixas:** faixas realistas de conteúdo/dados e estados relevantes.
6. **Interação e layout:** hierarquia, topologia, responsividade, affordances, feedback e transições; intenção, não CSS.
7. **Restrições e decisões em aberto:** plataforma, entrega, acessibilidade, localização, componentes reutilizáveis e escolhas que quem constrói não deve inventar.

Use de três a cinco tópicos quando a tarefa estiver resolvida; use a estrutura completa apenas para planejamentos ambíguos, com várias telas ou independentes. Não repita a conversa.

## Confirme e pare

Apresente o briefing para confirmação explícita ou para uma rodada de correção e então pare: o shape nunca escreve código nem um contrato de direção.

Quando não houver um humano nem um mecanismo de respostas estruturadas, marque as suposições com clareza, devolva o briefing e pare.
