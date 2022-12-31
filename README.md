# Notas deputados

Esse projeto tem por objetivo comparar notas de desempenho de Deputados Federais do Brasil em 2022 entre dois diferentes sites, ou seja, obter dados de duas fontes diferentes e compará-las com o propósito de saber se há uma relação, um padrão entre as duas fontes usando Machine Learning. Em suma um problema que precisa que os dados sejam coletados para que se comece uma análise exploratória seguindo da aplicação de Machine Learning para que se possa iniciar uma discussão pautada nos dados do tipo de relação entre os sites. <br> 
Esse problema é sugestão do professor **Vinícius Souza**, *https://www.linkedin.com/in/vmesquita/*, em uma postagem no site LinkedIn.com, onde o mesmo provocava aspirantes a Ciêntista de Dados a saírem dos datasets mais comuns usados por iniciantes e procurar a dificuldade em **dados reais**. <br>

Os sites em questão são:
https://www.legislabrasil.org/, que chamaremos de **LegislaBr** e <br>
https://www.politicos.org.br/, que será chamado de **Politícos.org**. <br>

Após definir o problema a ser resolvido é iniciada a etapa de coleta de dados.
-----------(verificar sugestão dos pontos)

para saber se a nota de cada deputado é similar ou é independente para cada site. Usando como referência os pontos abaixo:

  1) Coleta de dados via web scrapping. Bibliotecas como Selenium e BeautifulSoup podem ajudar.

  2) Análise exploratória: Gráficos de dispersão entre nota padronizada dos dois rankings. São correlacionados? Existem outliers?

  3) Modelo explicativo: Regressão linear múltipla entre os 4 itens do ranking do Legisla Brasil e a nota do Ranking dos Políticos, por exemplo. Os 4 itens do Legisla explicam qual variabilidade do Ranking dos Políticos?

  4) Discussão: Por que são diferentes? Por que são iguais? Existem vieses políticos? São apartidários? Qual sua opinião que justifique os resultados?

Com o problema em mãos é hora de começar a coleta dos dados. Esta etapa foi feita através das bibliotecas do python Selenium e BeautifulSoup. Abaixo a sequência que relaciona o site, o código e o .csv gerado.<br>
<br>
Como saída dessa etapa foram obtidos dois arquivos no formato .csv, um para cada site. <br>
  https://www.legislabrasil.org/ -> Rank_pol_V3.ipynb -> rank_legislabr_completo.csv <br>
  https://www.politicos.org.br/ -> Ranking_pol_site2.ipynb -> df_site2.csv <br>
<br> 

A terceira etapa se deu com análises de diversos tipos com os datasets gerados (Analise_dos_dados.ipynb). E alguns pontos importantes observados:
  1. A quantidade de deputados registrados em cada site era diferente. Optou-se por eliminar todos os deputados que não constavam nos dois sites. A partir daqui uniu-se as duas bases de dados formando um só dataset.
  2. Observou uma correlação negativa entre as notas.
  3. Analisando as metodologias dos sites para atribuição das notas percebeu-se que há espaço para enviesamento nas notas do Site 2, pois uma das notas consta do julgamento das ações dos parlamentares nas principais votações do Congresso. Esse julgamento se faz por meio de um conselho. O Conselho é composto por 32 pessoas, mas não é exposto como ele é formado, qual a diversidade do corpo, se é representativo com a população interessada, no caso a população brasileira. Deixando parecer que o certo ou errado na votação do candidato pode representar uma nota enviesada. Com isso surgiu a curiosidade de verificar a nota por ideologia do partido dos deputados. Em resumo, classificando o partido em Esquerda, Centro e Direita. Para tal, foi utilizida a classificação dada pelo jornal Folha, na seguinte matéria: https://www1.folha.uol.com.br/poder/2022/09/o-que-faz-um-partido-ser-de-direita-ou-esquerda-folha-cria-metrica-que-posiciona-legendas.shtml.
  4. Comparando a média por ideologia também é constatado que Centro e Direita tem notas similares e opostas a Esquerda. No site 1 o primeiro grupo tem notas mais baixas que o segundo grupo, acontecendo o inverso no site 2.
  5. Analisando os deputados pela orientação ideológica do partido vemos comportamentos diferentes. Para a direita uma correlação positiva com uma dispersão menor que nas outras duas categorias. Para os partidos de Centro vemos uma correlação fraca negativa com dispersão maior que para os partidos de Direita. Já para os partidos tidos como de Esquerda mostra correlação negativa com dispersão alta também. As dispersões mais altas são nos valores mais extremos, onde é possível perceber muitos outliers presentes em todas as ideologias. Sendo que na ideologia Direita os outliers estão mais próximos da linha de regressão.
  
A etapa de Machine Learning (MachineLearning.ipynb) começa com uma explanação do problema e das metodologias utilizadas por cada um dos sites. E é reforçado o que é constatado acima:
  1. Após a análise da composição das notas foi observado que o quesito Antiprivilégio abre espaço para se tornar uma variável subjetiva, já que depende de um conselho para julgar o posicionamento do deputado. E apesar da composição do conselho ser pública não fica claro como ela é feita o que abre espaço para que seu posicionamento seja enviesado. E com o agravante dessa nota ser a que tem peso maior na composição final.
  2. Outro questionamento levantado foi qual a relação da região do Brasil ao qual o deputado pertence e sua nota, mas foi verificado que não há alterações por região, elas seguem o mesmo padrão da população.
<br>

Em seguida são treinados três modelos de regressão linear:
  1. Somente com as 4 notas do Site 1 para prever a nota do Site 2.
  2. Adicionando a feature 'ideologia', que diz se o partido do deputado é de Esquerda, Centro ou Direita.
  3. Adicionando a feature 'partido'.
  
Conclusão

  Dos três modelos estudados o M3 foi o que apresentou um R² mais alto (70%). A esse modelo foram adicionadas features catégoricas com base na própria base de dados, isso porque foi observada uma tendência do Site 2 em agrupar notas de acordo com a ideologia do partido político dos deputados.<br>
<br>
  Comparando o desempenho do Modelo M2 e M3 é possível dizer que o partido do deputado também influencia na nota, ou seja, especificando qual o partido do candidato fica mais fácil predizer a nota que ele tem no Site 2 utilizando as informações do Site 1.
<br>  
  No entanto, considerando somente as 4 notas do Site 1 (Modelo M1) não é possível predizer as notas do Site 2, muito provavelmente por conta da dependência das notas com a ideologia do partido. Que pode ser consequência do critério subjetivo observado na metodoligia adotada pelo Site 2 (Ranking dos políticos).<br>
  <br>
  Somente com as notas do site 1 o modelo preditivo fica com um desempenho muito ruim. Como critério de avaliação do modelo foi utilizado o valor do R². No subset de validação foi utilizada a técnica Cross Validation, e foi obtido um valor médio de 0,315. Verificando-se que o modelo de regressão explica 31,5% da variância. A teoria diz que quanto mais variância for explicada pelo modelo de regressão mais próximos os pontos de dados estarão em relação à linha de regressão ajustada. Nesse caso um desempenho ruim.

  

Para mais detalhes vale a leitura dos notebooks aqui listados, quaisquer dúvidas e/ou sugestões são muito bem vindas, é só entrar em contato pelo e-mail: almsm88@gmail.com.
