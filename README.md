<h1 align="center"> Relação entre notas de deputados brasileiros (2022) de acordo com dois diferentes sites </h1>

**1.0	Problema de negócio**

Um momento importante da política é pós eleição, como acompanhar os parlamentares eleitos? Começando pela quantidade de deputados federais, no Brasil atualmente são 513, se torna muito trabalhoso acompanhar o desempenho de todos. Essa é uma das propostas dos sites https://indice.legislabrasil.org/public/ e https://www.politicos.org.br/.  

No entanto a avaliação de cada site é baseada em critérios próprios, isso é bom ou ruim? Para você, leitor, uma resposta possível é: depende! Sim, depende da sua própria visão sobre o que é ser um bom político. Se ela estiver alinhada com as propostas dos sites, provavelmente a resposta será: sim, é um bom avaliador. Se ela não tiver alinhada, provável que sua resposta seja o oposto. Mas como são esses critérios? São interpretações do que é bom ou ruim ou são puramente dados?  

Esse projeto tem por objetivo comparar notas de desempenho de Deputados Federais do Brasil em 2022 entre os diferentes sites, ou seja, obter dados dessas duas fontes e compará-las, com o propósito de saber se há uma relação definida por regressão linear múltipla entre as quatro notas do LegislaBr e a média do Politicos.org, encontrar um padrão entre as fontes usando Machine Learning. Em suma um problema no qual é preciso que os dados sejam coletados para fazer uma análise exploratória seguindo da aplicação de Machine Learning para que se possa iniciar uma discussão pautada nos dados se existe algum tipo de relação entre as notas dos sites.  

Esse problema é sugestão do professor **Vinícius Souza**, *https://www.linkedin.com/in/vmesquita/*, em uma postagem no site LinkedIn.com, onde ele provocava aspirantes a Cientista de Dados a saírem dos datasets mais comuns geralmente usados por iniciantes e procurar a dificuldade em **dados reais**.  

Os sites em questão serão abordados da seguinte maneira:
https://www.legislabrasil.org/, chamaremos de **LegislaBr** e  
https://www.politicos.org.br/, será chamado de **Politícos.org**.  

Para iniciar a análise a primeira etapa foi conhecer os sites, como são seus critérios e depois buscar os dados necessários. O site **LegislaBr** utiliza 17 notas subdivididas em 4 grupos:
1.	PRODUÇÃO LEGISLATIVA: Mede o trabalho efetuado pelo parlamentar na elaboração, análise e votação de instrumentos legislativos.
2.	FISCALIZAÇÃO: Mede a fiscalização que o deputado concretiza em relação ao Executivo Federal
3.	MOBILIZAÇÃO: Mede a capacidade do parlamentar de articulação e cooperação com outros agentes políticos
4.	ALINHAMENTO PARTIDÁRIO: Alinhamento partidário do parlamentar em relação à votação da maioria do seu partido.  

Os detalhes de cada grupo podem ser verificados no próprio site, pelo link: https://indice.legislabrasil.org/public/metodologia.  
O site Politicos.org utiliza uma média de 4 notas, de acordo com a metodologia fornecida pelo próprio site:
Fórmula: [((APx3)+(AD/3))/4] + AC + OT = Nota Final

1.	AP = Antiprivilégio
2.	AD = Antidesperdício
3.	AC = Anticorrupção
4.	OT = Outros  

Aplicando a seguinte metodologia, pelo site:
Pesos:  
•	Para calcular a pontuação final dos políticos, aplicar os seguintes pesos:  
•	Antiprivilégios (Votações): 3x  
•	Antidesperdício (Presenças e Economia de Verbas): 3÷  
•	Anticorrupção (Ficha Limpa): quando há processos, subtrai pontos da nota final  
•	Outros: soma ou subtrai pontos da nota final.  

Obs: A montagem da fórmula exibida no site ficou confusa, após verificação foi constatado que faltava um ( ) e foi corrigida aqui na apresentação.  

**2.0 Dados Coletados**

Após definir o problema a ser resolvido é iniciada a etapa de coleta de dados, conseguir os dados: 4 notas do LegislaBr e a média final das notas do Politicos.org.  

O produto do webscraping do LegislaBr foi um .csv com 565 linhas e 7 colunas, contendo nome, estado e partido de cada deputado além das notas dos 4 grupos (produção legislativa, fiscalização, mobilização e alinhamento partidário). O arquivo está em:
https://github.com/AndreMoura88/Notas_de_deputados/blob/main/datasets/raw_data/legislaBr_raw.csv  

Para o Politicos.org foi gerado outro .csv, agora com 497 linhas e duas colunas, contendo o nome do deputado e a média da sua nota. O arquivo está em: 
https://github.com/AndreMoura88/Notas_de_deputados/blob/main/datasets/raw_data/politicos-org_raw.csv  

Lembrando que as notas estão no intervalo de 0 a 10. Os dois datasets foram comparados e foi verificado que 78 deputados federais não constavam nos dois sites. Então foram fundidos pelo nome do deputado para que ficasse somente os deputados citados nos dois sites.  

Essa etapa foi feita através das bibliotecas do python ``` Selenium ``` e ```BeautifulSoup```, como ferramentas de webscraping. Além das bibliotecas mais usuais em análise de dados, como ``` pandas ```, ``` requests ```, ``` re ``` e ``` unidecode```. Usando o Jupyter Notebook e Colab para produção dos notebooks.  

Nessa fase se deu início carregando e unindo os dois datasets gerados na etapa da coleta. Que apresenta 488 linhas e 9 colunas.

<p align="center">
  <img src="https://i.ibb.co/mqnMgTP/Imagem1.png">
</p>
 

3.0 Análise exploratória

Após ter posse dos dados dá se início a parte de análise exploratória. No que foram observados pontos importantes:  

1.	A quantidade de deputados registrados em cada site era diferente. Optou-se por eliminar todos os deputados que não constavam nos dois sites.
2.	Observou-se que a nota média da qualidade dos políticos do site LegislaBr tem correlação negativa = 0,39 com a nota média do site Politicos.org. Como mostrado no gráfico seguinte:  

<p align="center">
  <img src="https://i.ibb.co/j8Cb93j/media-media.png">
</p>


3.	Analisando as metodologias dos sites para atribuição das notas percebeu-se que há espaço para enviesamento nas notas do Politicos.org, pois uma das notas consta do julgamento das ações dos parlamentares nas principais votações do Congresso. Esse julgamento se faz por meio de um conselho. O Conselho é composto por 32 pessoas, mas não é exposto como ele é formado, qual a diversidade do corpo, se é representativo com a população interessada, no caso a população brasileira. Deixando parecer que o certo ou errado na votação do deputado pode representar uma nota enviesada. Com isso surgiu a curiosidade de verificar a nota por ideologia do partido dos deputados. Em resumo, classificando o partido em Esquerda, Centro e Direita. Para tal, foi utilizada a classificação dada pelo jornal Folha, na seguinte matéria: https://www1.folha.uol.com.br/poder/2022/09/o-que-faz-um-partido-ser-de-direita-ou-esquerda-folha-cria-metrica-que-posiciona-legendas.shtml.
4.	No site Politcos.org, comparando a média por ideologia também é constatado que Centro e Direita têm notas similares (notas altas) entre si, e diferentes das notas da Esquerda (notas baixas). No LegislaBr, Direita e Centro tem notas mais baixas que a Esquerda.  

<p align="center">
  <img src="https://i.ibb.co/8rf3jqm/transferir.png">
</p>

5.	Analisando os deputados pela orientação ideológica do partido vemos comportamentos diferentes. Para a direita uma correlação positiva (r=0,39) com uma dispersão menor que nas outras duas categorias. Para os partidos de Centro vemos uma correlação negativa (r= - 0,06) com dispersão maior que para os partidos de Direita. Já para os partidos tidos como de Esquerda mostra correlação negativa (r = - 0,33) com dispersão alta também. As dispersões mais altas são nos valores mais extremos, onde é possível perceber muitos outliers presentes em todas as ideologias. Sendo que na ideologia Direita os outliers estão mais próximos da linha de regressão.  

<p align="center">
  <img src="https://i.ibb.co/1Rdc52q/Imagem4.png">
</p>

O gráfico de densidade também ajuda a identificar a distribuição das notas para cada site. Observando o primeiro gráfico da primeira linha é possível ver a concentração de notas baixas (no intervalo de 2 a 4) para os partidos de direita e centro, enquanto as maiores notas atribuídas a políticos pelo site LegislaBr são alcançadas em sua maioria por partidos de esquerda. No segundo gráfico da primeira linha podemos ver o agrupamento de notas, partidos de esquerda localizados no lado esquerdo superior do mapa, que indica nota alta no LegislaBr e notas baixas no Politicos.org, e partidos de centro e direita localizados na parte inferior direita do gráfico, indicando comportamento oposto aos partidos de esquerda.  

De acordo com a análise feita acima, entende-se que o LegislaBr está mais alinhado com partidos de esquerda e o Politicos.org com políticos de partidos de direita e centro.  

<p align="center">
  <img src="https://i.ibb.co/NWKvvr8/kde.png">
</p>

Comparando as médias por ideologia também é constatado que Centro e Direita tem notas similares e opostas a Esquerda. No LegislaBr o primeiro grupo tem notas mais baixas que o segundo grupo, acontecendo o inverso no Politicos.org.  

<p align="center">
  <img src="https://i.ibb.co/L9zvzRJ/boxplot.png">
</p>

**4.0 Modelagem**  

A etapa de Machine Learning, (/content/drive/MyDrive/Colab_Notebooks/Notas_deputados/notebooks/ND_MachineLearning.ipynb) começa com uma explanação do problema e das metodologias utilizadas por cada um dos sites.  

1.	Outro questionamento levantado foi qual a relação da região do Brasil ao qual o deputado pertence e sua nota, mas foi verificado que não há alterações por região, elas seguem o mesmo padrão da população.  

Em seguida são treinados três modelos de regressão linear:
M1.	Somente com as 4 notas do LegislaBr para prever a nota média do Politicos.org.
M2.	Adicionando a feature 'ideologia', que diz se o partido do deputado é de Esquerda, Centro ou Direita.
M3.	Adicionando a feature 'partido'. 

Avaliação do modelo M1  

Somente com as notas do LegislaBr o modelo preditivo fica com um desempenho muito ruim. Um dos critérios de avaliação do modelo foi o valor do R² e foi obtido um valor médio de 0,315. Verificando-se que o modelo de regressão explica 31,5% da variância. A teoria diz que quanto mais variância for explicada pelo modelo de regressão, mais próximos os pontos de dados estarão em relação à linha de regressão ajustada. Nesse caso um desempenho ruim.  
O Erro Absoluto Médio consiste na média das distâncias entre valores preditos e reais. Diferentemente do MSE e do RMSE, essa métrica não "pune" tão severamente os outliers do modelo. Essa medida apresenta valor mínimo 0 e não apresenta valor máximo.  
Para o MAE: 1,0612

Avaliação do Modelo M2  

Com a adição da nova feature o modelo M2 conseguiu uma performance nelhor que o anterior, aproximadamente 57%. E um MAE = -0,7277  

Avaliando o Modelo M3  

Uma melhora na base de treino, no subset de validação, foi alcançada com as mudanças para o modelo M3. Com a validação cruzada o valor de R² = 63,6% e um MAE = - 0,6944  

Por meio desses resultados o modelo escolhido foi o M3 para fazer a avaliação na base de testes.  

Os resultados na base de teste foram:
- MAE = 0,649
- MSE = 0,734
- R² = 0,701  

Com uma base de dados não vista pelo modelo o desempenho para o R² foi de 70%.

**5.0 Conclusão**  

Dos três modelos estudados o M3 foi o que apresentou um R² mais alto (70%). A esse modelo foram adicionadas features categóricas com base na própria base de dados, isso porque foi observada uma tendência do Politicos.org em agrupar notas de acordo com a ideologia do partido político dos deputados.  

Avaliando as metodologias dos dois sites é possível perceber que determinados valores têm mais peso na nota que outros. Por exemplo no LegislaBr a nota “Alinhamento partidário” tem um peso muito alto, pois esse quesito é composto por uma única nota, enquanto os outros três quesitos são compostos por várias notas. A figura abaixo mostra os coeficientes para o Modelo M2. Nele foram introduzidas as features dos partidos e é possível ver como elas são as que tem maior impacto no modelo. Como podemos ver os valores nos mostram que pertencer a um partido de esquerda contribui muito negativamente para a nota que será prevista. E com menos intensidade, mas no mesmo sentido, podemos falar o mesmo para o alinhamento partidário. Deputados que tem forte alinhamento partidário de acordo com o LegislaBr terão notas baixas no Politicos.org.  

E de acordo com a metodologia do Politicos.org uma nota que tem alto impacto é a de Antiprivilégios, no entanto ela é uma nota sujeita a uma comparação com as orientações do conselho do site, então dependendo do que este conselho julga como certo ou errado a nota irá variar para alta ou baixa.  

<p align="center">
  <img src="https://i.ibb.co/3hFvg2K/Imagem7.png">
</p>

Já no Modelo M3 o alinhamento partidário perde força na predição e passa a figurar entre os coeficientes com baixa influência, apesar de continuar impactando negativamente na nota. Nesse modelo as features dos partidos têm alto impacto, direita e centro impactando positivamente e esquerda com maior valor de coeficiente negativo. As features que tiveram maior impacto positivos foram as dos partidos Cidadania e Novo.  

<p align="center">
  <img src="https://i.ibb.co/QCsCm6T/Imagem8.png">
</p>


Comparando o desempenho do Modelo M2 e M3 é possível dizer que o partido do deputado também influencia na nota, ou seja, especificando qual o partido do deputado fica mais fácil predizer a nota que ele tem no Politicos.org utilizando as informações do LegislaBr.  

No entanto, considerando somente as 4 notas do LegislaBr (Modelo M1) não é possível predizer as notas do Politicos.org, muito provavelmente por conta da dependência das notas com a ideologia do partido.  

Somente com as notas do LegislaBr o modelo preditivo fica com um desempenho muito ruim. Como critério de avaliação do modelo foi utilizado o valor do R². Verificamos que as notas do LegislaBr explicam em média 31,5% das notas do Politicos.org. Assim sendo, existem critérios não analisados neste estudo que explicam o ranking feito pelo Politicos.org.  

Como ficou claro na EDA, o LegislaBr tem um potencial alinhamento com a Esquerda e o Politicos.org tem alinhamento com a Direita. Visto isso é possível deduzir que diferentes metodologias podem gerar notas de desempenho diferentes (nesse caso quase que opostas de acordo com o partido político). Isso pode gerar confusão na população interessada em saber o desempenho de vários deputados. Pois a nota irá depender de cada veículo de comunicação, o que não mostra consistência na análise de desempenho dos deputados.  

Para mais detalhes vale a leitura dos notebooks aqui listados, quaisquer dúvidas e/ou sugestões são muito bem-vindas, é só entrar em contato pelo e-mail: almsm88@gmail.com.

