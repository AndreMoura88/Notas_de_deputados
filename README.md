<h1 align="center"> Análise de Notas Parlamentares brasileiros (2022): Avaliando a Convergência de Indicadores Legislativos através de Regressão Linear,  utilizando Web Scraping, Análise Exploratória de Dados (EDA) e Machine Learning </h1>

### **1.0	Problema de negócio**

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

### **2.0 Dados Coletados**

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
 

### 3.0 Análise exploratória

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

### **4.0 Modelagem**  

O objetivo principal dessa análise é verificar se as 4 notas do Legisla Brasil (site 1) explicam a variabilidade da nota do Ranking dos políticos (site 2) usando um modelo de regressão linear.

Nesta etapa da análise, será realizado um teste de hipótese com o objetivo de verificar se os dados disponibilizados pelo site **Legisla Brasil** possuem poder preditivo sobre as notas atribuídas pelo **Ranking dos Políticos**.

Embora não haja uma relação causal direta entre os dois rankings, parte-se da premissa de que os indicadores quantitativos atribuídos pelo Legisla Brasil — como produção legislativa, fiscalização, mobilização e alinhamento partidário — podem estar correlacionados com a percepção ou avaliação capturada pelo Ranking dos Políticos.

A aplicação de um **teste de hipótese** é fundamental neste contexto para avaliar se há evidências estatísticas que sustentem a possibilidade de previsão da nota_site2 a partir das variáveis do Legisla Brasil, orientando assim as etapas subsequentes da modelagem e validação preditiva.

- Formulação das Hipóteses: <br>
    
Para verificar se as variáveis provenientes do Legisla Brasil podem ser utilizadas para prever as notas do Ranking dos Políticos, formulamos as seguintes hipóteses:

Hipótese geral: <br>
H₀: As variáveis do Legisla Brasil não são capazes de prever ou explicar a nota_site2. <br>
H₁: As variáveis do Legisla Brasil são capazes de prever ou explicar a nota_site2. <br>

Este teste permitirá verificar, de forma quantitativa, se a inclusão das variáveis do Legisla Brasil melhora a capacidade preditiva do modelo, quando comparado a um modelo sem essas variáveis. Caso rejeitemos a hipótese nula, teremos evidência de que essas variáveis podem contribuir para uma modelagem mais precisa.

Foi aplicado os métodos de Pearson e Spearman, os quais apresentaram os seguintes resultados: <br>

**Pearson**: r=-0.394, p=0.00000 <br>
**Spearman**: rho=-0.219, p=0.00000 <br>

Pearson: r = -0.394, p ≈ 0.000. Correlação **moderada** e **negativa** entre avg_site1 e nota_site2. <br>
Como o p-valor é muito menor que 0.05, essa correlação é estatisticamente significativa.<br>
Ou seja: conforme as notas do site 1 aumentam, as do site 2 tendem a diminuir moderadamente.
<br>
<br>
Spearman: rho = -0.219, p ≈ 0.000. Correlação **fraca** e **negativa**, mas também estatisticamente significativa. <br>
Spearman verifica relação monotônica, não necessariamente linear.<br>
Mesmo sem suposição de linearidade, existe uma associação significativa entre as notas.<br>

Esses resultados nos permitem rejeitar a hipótese nula de ausência de associação, indicando que existe relação estatística entre as avaliações dos dois sites, ainda que negativa.

Assim, é justificável prosseguir com a modelagem preditiva para verificar se as variáveis quantitativas provenientes do Legisla Brasil, isoladamente ou em conjunto, podem ser utilizadas para prever as notas do Ranking dos Políticos."

- Para dar início a modelagem vamos fazer algumas ponderações:<br>
- Testaremos mais de um modelo de regressão (Regressão Linear e LightGBM)<br>
- Vamos ver o poder preditivo somente das variáveis numéricas (as notas atribuidas pelo LegislaBr)<br>
- Em seguida iremos ver a influência das variáveis categóricas já presentes nos dados e depois a influência da variável 'Ideologia'. <br>
Qual o impacto delas no poder preditivo do modelo?<br>

- **Modelo I** - Modelagem somente da variáveis numéricas<br>

A **regressão linear**, utilizando apenas variáveis numéricas associadas à atuação parlamentar, apresentou um erro médio absoluto de 1,08 pontos e um erro percentual médio de 24%, com um erro típico de 1,37 pontos (RMSE). Esses resultados indicam uma capacidade preditiva moderada, sinalizando que tais variáveis possuem relação com a avaliação do outro site, mas não são suficientes para explicar toda a variabilidade. Diante disso, propõe-se a inclusão de variáveis categóricas e o teste de modelos não lineares, como o LightGBM, para potencial melhoria no desempenho preditivo.<br>

A aplicação do modelo **LightGBM** utilizando apenas variáveis numéricas resultou em um erro médio absoluto (MAE) de 1,02 pontos e um erro percentual médio absoluto (MAPE) de 22%, com um erro típico (RMSE) de aproximadamente 1,38 pontos. Em comparação com a regressão linear, o LightGBM apresentou uma leve redução no MAE e MAPE, indicando uma melhora modesta na capacidade preditiva. Esses resultados sugerem que as variáveis numéricas relacionadas à atuação parlamentar possuem uma relação relevante com a nota atribuída pelo segundo site, mas ainda insuficiente para explicar plenamente a variabilidade observada. Diante disso, recomenda-se a inclusão de variáveis categóricas e a exploração de interações mais complexas para aprimorar a performance do modelo.<br>

- **Modelo II** - Adicionando as features categóricas presentes nos dados<br>

Na segunda etapa da modelagem, foram incorporadas **variáveis categóricas** ('estado', 'partido' e 'região') além das variáveis numéricas previamente utilizadas. A inclusão dessas informações demonstrou um impacto positivo na capacidade preditiva do modelo de **regressão linear**, que apresentou uma redução expressiva no erro médio absoluto (MAE), passando de 1,08 para 0,65 pontos, e no erro percentual médio absoluto (MAPE), que caiu de 24% para 13%. O erro quadrático médio (MSE) também reduziu significativamente para 0,81, com um RMSE de aproximadamente 0,90 pontos.<br>

Por outro lado, o modelo **LightGBM**, embora tenha apresentado redução no MAPE (de 22% para 18%) e também no MAE, não superou a regressão linear em termos de erro absoluto ou quadrático, sugerindo que, nesta configuração específica, a regressão linear foi mais eficiente para captar os padrões presentes nos dados.<br>

Esses resultados indicam que as variáveis categóricas possuem uma contribuição relevante na explicação da variabilidade da 'nota_site2', reforçando a importância de sua consideração na modelagem. A próxima etapa consistirá em avaliar o acréscimo da variável 'ideologia' e investigar se sua inclusão resulta em uma melhora adicional do desempenho preditivo.<br>

- **Modelo III** - Modelagem com todas as variáveis<br>

Na fase final da modelagem, a variável categórica adicional 'ideologia' foi incorporada ao conjunto de preditores, junto com as variáveis 'estado' e 'partido', além das variáveis numéricas previamente utilizadas. Os modelos de regressão linear e LightGBM foram treinados utilizando validação cruzada com 3 folds, e apresentaram resultados muito próximos, indicando convergência no desempenho.<br>

Na **regressão linear** o modelos obteve MAE de aproximadamente 0,65 e MAPE de 13%, refletindo um erro médio percentual absoluto relativamente baixo, e um RMSE em torno de 0,90 pontos. O MSE também se manteve reduzido, em torno de 0,81, indicando que a variabilidade residual do modelo é pequena.<br>

Já o **LightGBM** mostrou uma leve melhora em comparação com a regressão linear, MAE reduziu para 0,63 e MAPE para 12%. RMSE = 0,84 e MSE de 0,7, também indicando a baixa variabilidade do modelo.<br>

Esses resultados sugerem que a inclusão da variável 'ideologia' contribuiu para melhorar a precisão preditiva do modelo, embora a diferença entre os modelos lineares e baseados em árvores tenha se reduzido nessa configuração. A similaridade de desempenho aponta que, para este conjunto de dados e variáveis, um modelo mais simples como a regressão linear pode ser tão eficiente quanto modelos mais complexos como o LightGBM.<br>

- **Validando o modelo nos dados de teste**<br>

Após o treinamento do modelo LightGBM (modelLGBM) utilizando as variáveis numéricas e categóricas previamente selecionadas, o pipeline foi aplicado aos dados de teste para validar sua capacidade preditiva fora da amostra utilizada na etapa de treinamento.<br>

As métricas obtidas foram as seguintes:<br>

Mean Absolute Error (MAE): 0.59<br>
Mean Squared Error (MSE): 0.63<br>
Root Mean Squared Error (RMSE): 0.79<br>
Mean Absolute Percentage Error (MAPE): 0.10<br>
R-squared (R²): 0.58<br>

Esses resultados indicam que, em média, o modelo apresentou um erro absoluto de 0.61 na predição das notas do Politicos.org, com um erro percentual médio de aproximadamente 11%. O RMSE de 0.78 reforça a boa capacidade do modelo em generalizar para novos dados, mantendo o erro médio relativamente baixo.<br>

O coeficiente de determinação (R²) de 0.60 sugere que o modelo é capaz de explicar cerca de 60% da variabilidade das notas do Politicos.org a partir das informações disponíveis no LegislaBr, incluindo variáveis categóricas como estado, partido e ideologia.<br>

De modo geral, os resultados são satisfatórios, indicando que a abordagem de incluir variáveis categóricas relevantes contribuiu para melhorar a precisão do modelo, reduzindo os erros de predição e aumentando sua capacidade explicativa.<br>

### **5.0 Conclusão**<br>
Dos três modelos avaliados, o modelo que incorporou variáveis categóricas — incluindo estado, partido e ideologia (Modelo III) — apresentou o melhor desempenho, com menores valores de erro nas métricas avaliadas, indicando maior precisão na predição das notas do Politicos.org a partir dos dados do LegislaBr.<br>

O modelo baseado somente nas quatro variáveis numéricas do LegislaBr (Modelo I) apresentou desempenho inferior, o que sugere que essas variáveis isoladas não são suficientes para prever adequadamente as notas do Politicos.org. Isso se explica, em parte, pela diferença nas metodologias de avaliação dos dois sites e pela influência das características políticas dos deputados.<br>

De fato, ao longo da análise exploratória foi identificada uma relação inversa entre as notas atribuídas pelos dois sites: o LegislaBr tende a conceder notas mais altas a políticos de esquerda, enquanto o Politicos.org favorece políticos de direita ou centro. Esse padrão reforça que, para ambos, a ideologia política exerce forte influência sobre as avaliações.<br>

Esse viés levanta um ponto crítico sobre a fidelidade das notas enquanto medidas objetivas da atuação parlamentar. Se cada site favorece determinados espectros ideológicos, a nota final pode refletir mais uma posição política do avaliador do que uma avaliação imparcial da atuação do político. Assim, quem consulta essas notas como ferramenta para entender o desempenho de um parlamentar corre o risco de ser induzido por avaliações enviesadas, que não necessariamente correspondem à real atuação do político, mas sim ao alinhamento entre as práticas desse parlamentar e a linha política do site avaliador.<br>

Esse fenômeno pode ser prejudicial para a sociedade, pois dificulta a obtenção de informações confiáveis e imparciais sobre a atuação dos representantes eleitos. Caso essa seletividade seja intencional, é importante que os sites sejam transparentes, assumindo claramente que suas avaliações são contextualizadas segundo determinadas orientações ideológicas. Assim, quem avalia a atuação de um deputado sob uma perspectiva específica pode entender que a nota atribuída reflete tal viés: se a pessoa adota uma visão mais próxima da esquerda, pode priorizar as notas do LegislaBr; se se identifica mais com a direita ou centro, pode considerar mais adequadas as notas do Politicos.org.<br>

Portanto, esta análise não só evidencia a importância de incorporar variáveis categóricas relacionadas à ideologia e ao partido para melhorar o desempenho preditivo dos modelos, como também chama a atenção para a necessidade de cautela na interpretação dessas notas, reconhecendo que elas podem refletir vieses políticos e não necessariamente a complexidade real da atuação parlamentar.<br>

Na etapa de teste do modelo final (Modelo III), os resultados foram:<br>

Mean Absolute Error (MAE): 0.59<br>
Mean Squared Error (MSE): 0.63<br>
Root Mean Squared Error (RMSE): 0.79<br>
Mean Absolute Percentage Error (MAPE): 0.10<br>
R-squared (R²): 0.58<br>

Esses resultados mostram que, embora seja possível modelar a relação entre as notas de forma razoavelmente precisa quando se conhece o contexto político, as avaliações de cada site seguem padrões diferentes e, portanto, devem ser interpretadas dentro desse escopo ideológico.

#### O problema que o autor enfrentou nesse projeto é semelhante a muitos problemas reais que as empresas enfrentam na previsão de vendas, otimização de processos e tomada de decisão baseada em dados. Assim como o autor precisou identificar outros fatores além das notas dos deputados, as empresas muitas vezes precisam considerar uma variedade de fatores, como preço, demanda do mercado, posicionamento de concorrentes, entre outros, para prever com precisão as vendas ou tomar decisões informadas.
#### As habilidades que o autor usou em seu projeto, como raspagem de dados, EDA e modelagem de machine learning, são altamente valiosas para as empresas que desejam extrair insights significativos de seus dados e tomar decisões mais informadas.

Para mais detalhes vale a leitura dos notebooks aqui listados, quaisquer dúvidas e/ou sugestões são muito bem-vindas, é só entrar em contato pelo e-mail: almsm88@gmail.com.

