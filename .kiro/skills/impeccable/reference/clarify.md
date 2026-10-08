> **Contexto adicional necessário**: conhecimento do público e estado emocional.

Reescreva textos de interface pouco claros para que os usuários entendam o que aconteceu, o que importa e o que fazer em seguida. Preserve o significado factual, a terminologia do produto e a voz da marca.

## Audite a linguagem

Leia o caminho de interação inteiro, não strings isoladas. Identifique:

- substantivos, verbos e ações ambíguos;
- jargão interno ou conhecimento presumido;
- rótulos, resultados e estados do sistema vagos;
- consequências, recuperação ou prazos ausentes;
- terminologia e uso de maiúsculas inconsistentes;
- títulos, introduções, textos de ajuda e confirmações redundantes;
- texto que quebra em larguras realistas ou na tradução;
- tom que ignora estresse, risco, sucesso ou urgência.

Infira o público e a tarefa a partir do contexto do produto e da UI ao redor. Pergunte antes de alterar afirmações factuais, sentido jurídico ou um termo que possa ser específico do domínio.

## Defina a hierarquia da mensagem

Para cada estado, decida:

1. o único fato de que o usuário precisa agora;
2. a ação disponível em seguida;
3. o contexto de apoio que muda a decisão;
4. o tom apropriado para este momento.

Diga cada ideia uma vez só. Se o título já explica o estado, a introdução deve acrescentar informação nova ou desaparecer.

## Reescreva por função

### Ações e navegação

Use um verbo e um objeto específicos quando o resultado ainda não for óbvio. Os rótulos devem descrever o que vai acontecer, não o gesto usado para disparar a ação. Mantenha o mesmo substantivo e o mesmo verbo para o mesmo conceito em todo o produto.

Em ações destrutivas, nomeie o objeto e a consequência. Prefira desfazer a confirmar quando a recuperação for segura. Quando a confirmação for necessária, nomeie a ação tanto na mensagem quanto no botão em vez de usar `Yes`, `No`, `OK` ou `Submit`.

### Formulários

Use rótulos persistentes; placeholders são exemplos, não rótulos. Apresente os requisitos de formato e de elegibilidade antes do envio. Explique por que uma informação é solicitada apenas quando isso não for óbvio. O tratamento de obrigatório e opcional deve ser consistente.

A validação diz o que precisa de atenção e como corrigir, sem culpar o usuário. Mantenha as instruções relacionadas perto do campo e anuncie os erros de forma acessível.

### Erros e permissões

Um erro acionável responde:

1. o que falhou;
2. por quê, quando isso é conhecido e útil;
3. como se recuperar ou que alternativa resta.

Não exponha códigos internos como mensagem principal. Não prometa uma causa ou solução que o sistema não tem como saber. Trate com seriedade privacidade, pagamento, exclusão, perda de acesso e trabalho bloqueado; cordialidade é bem-vinda, piadas não.

### Estados de carregamento, vazio e sucesso

O texto de carregamento nomeia a operação real e define uma expectativa honesta quando a espera é significativa. Mostre progresso determinado quando disponível; nunca invente progresso.

Um estado vazio distingue primeiro uso, ausência de resultados, filtros, permissões e falha. Explique o estado e ofereça a próxima ação útil.

O sucesso confirma o resultado concluído e menciona a próxima consequência apenas quando ela muda o que o usuário deve fazer. O sucesso rotineiro deve ser breve.

### Ajuda e texto instrucional

O texto de ajuda responde a uma pergunta implícita em vez de repetir o que o controle já diz. Use divulgação progressiva para detalhes incomuns. O texto de um link deve fazer sentido fora de contexto; controles só com ícone precisam de nomes acessíveis.

## Voz, acessibilidade e localização

A voz permanece consistente; o tom se adapta ao momento. Use linguagem simples sem achatar a terminologia que o público de fato conhece.

- Escreva mensagens completas e traduzíveis em vez de fragmentos concatenados.
- Mantenha variáveis e números estruturados para que os tradutores possam reordená-los.
- Permita expansão em vez de abreviar prematuramente.
- Faça o texto alternativo transmitir a informação da imagem; use alt vazio para decoração.
- Mantenha os nomes para leitores de tela alinhados com os rótulos visíveis e os resultados.
- Não dependa de pontuação, cor ou iconografia para carregar a mensagem sozinhas.

Mantenha um glossário curto de terminologia quando a inconsistência se espalhar pelo produto. Não varie palavras por efeito literário em uma interface.

## Verifique

Leia o fluxo em contexto e teste:

- compreensão sem conhecimento oculto do produto;
- acionabilidade em erros, estados vazios e pontos de decisão;
- precisão factual e terminologia consistente;
- escaneabilidade nas larguras-alvo e com zoom de 200%;
- nomes longos, expansão por localização, pluralização e valores dinâmicos;
- nomes acessíveis e mudanças de estado anunciadas;
- tom adequado à consequência e ao contexto emocional.

A copy final é tão curta quanto pode ser sem remover significado nem recuperação.

Quando a linguagem estiver fluindo bem, passe para `/impeccable polish` para a etapa final.
