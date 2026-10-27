# Relatório do Projeto - Acidentes em rodovias federais

## Entendimento do Negócio

### Definição do Problema e do Projeto
O projeto tem como objetivo investigar e responder a seguinte pergunta: quais combinações de fatores aumentam a chance de um acidente ser grave ou fatal? (As combinações serão analisadas pela taxa de gravidade por cruzamento de variáveis, mostrando as variáveis mais relevantes).

Para isso, o problema será tratado como um problema de classificação binária, e para fazer essa investigação, serão utilizados conceitos de Machine Learning para identificar quais variáveis preditoras mais alteram o resultado na variável-alvo `grave_ou_fatal`, usando a coluna `gravidade` como auxiliar de três níveis.

A ODS relacionada a este projeto é a ODS 3 (Saúde e Bem-Estar), com o objetivo de reduzir mortes e lesões no trânsito.

## Entendimento dos Dados
Dataset utilizado: [Acidentes 2025 (Agrupados por ocorrência)](https://www.gov.br/prf/pt-br/acesso-a-informacao/dados-abertos/dados-abertos-da-prf), disponibilizado pela Polícia Rodoviária Federal no site do GOV, reunindo informações sobre acidentes em rodovias federais em 2025.

O dataset possui 72.529 linhas e 30 colunas (antes do tratamento), e foram identificados alguns valores nulos nas colunas `classificacao_acidente`, `regional`, `delegacia` e `uop`, porém em quantidade extremamente baixa, oque não afetará a análise(1 valor nulo em `classificacao_acidente`, 2 em `regional`, 22 em `delegacia` e 38 em `uop`, que não são usadas como variáveis preditoras). E por fim, não foi identificado nenhuma linha/valor duplicado.

A variável `classificacao_acidente` contém:
-   Acidentes Com Vítimas Feridas: 56.181 (77,46%)
-   Acidentes Com Vítimas Fatais: 11.138 (15,36%)
-   Acidentes Sem Vítimas: 5.209 (7,18%)
-   Valores nulo: 1 (0,001%)

A descrição detalhada dos atributos, da distribuição das classes e dos valores ausentes encontra-se no arquivo `descdataset.md`.

## Preparação dos Dados
1-
Filtrei as colunas `pessoas`, `mortos`, `feridos_leves`, `feridos_graves`, `ilesos`, `ignorados` e `feridos` da linha de índice 1 (linha que possuía valor nulo na coluna `classificacao_acidente`) e, de acordo com o resultado das colunas filtradas, classifiquei o acidente.

![alt text](/imagens/image4.png)

Como houve pessoas mortas no acidente, alterei a coluna `classificacao_acidente` de nulo para "Com Vítimas Fatais".

2-
Criei uma nova coluna chamada `gravidade`, onde atribui valores as linhas seguindo as seguintes condições:
-   Se teve mortos no acidente (`mortos` > 0): atribui valor "Acidente fatal" na linha da nova coluna.
-   Se não teve mortos no acidente mas teve feridos graves(`mortos` == 0 E `feridos_graves` > 0): atribui valor "Acidente grave" na linha da nova coluna.
-   Se não teve mortos no acidente e também não teve feridos graves(`mortos` == 0 E `feridos_graves` == 0): atribui valor "Acidente sem gravidade" na linha da nova coluna.

![alt text](/imagens/image5.png)

*Criei essa coluna para evitar qualquer inconsistência futura na análise.

3- 
Criei a variável-alvo `grave_ou_fatal` que atribui valor inicial 0, e, se a coluna `mortos` ou `feridos_graves` forem maiores que 0, atribui valor 1.

![alt text](/imagens/image6.png)

Para confirmar que estava tudo certo e que não havia dados inconsistentes, fiz uma crosstab com as 2 colunas novas (`gravidade` X `grave_ou_fatal`) e o resultado foi positivo.

![alt text](/imagens/image7.png)

4-
Criei a lista `colunas_clean` somente com as variáveis preditoras, e defini x = `colunas_clean` (variáveis preditoras) e y = `grave_ou_fatal` (variável-alvo).

![alt text](/imagens/image8.png)

Após isso, conferi os 10 valores mais frequentes de cada coluna dentro da lista

![alt text](/imagens/image9.png)

![alt text](/imagens/image10.png)

*De início, é possível notar que a maioria dos acidentes:
-   Ocorreram no final de semana (sábado, domingo e sexta-feira)
-   Ocorreram em pleno dia
-   Ocorreram sob o céu claro
-   Foram causados por ausência de reação do condutor ou reação tardia/ineficiente

5-
Quantidade e proporção da classe `grave_ou_fatal`:

![alt text](/imagens/image11.png)

6-
Treino e teste:

![alt text](/imagens/image12.png)

-   Separei 20% para teste e 80% para treino.
-   Usei stratify para evitar distorções e garantir consistência, já que as classes estavam divididas em quase 30/70.
-   Usei random_state para garantir que o experimento seja repetível.

Decidi utilizar OneHotEncoder para transformar colunas com palavras/textos em colunas binárias(0 ou 1), porém havia um problema: a coluna `causa_acidente` tinha 69 valores diferentes.

![alt text](/imagens/image13.png)

Então, filtrando os valores de menor frequência da coluna, decidi agrupar em "Outros" os valores que aparecerem menos que 300 vezes no conjunto de treino, aplicando a mesma regra de corte para o conjunto de teste.

![alt text](/imagens/image14.png)

Decidi fazer o mesmo com as colunas `tipo_acidente` e `condicao_metereologica`.

![alt text](/imagens/image15.png)

7-
OneHotEncoding: transformando colunas com palavras/textos em colunas binárias(0 ou 1).

![alt text](/imagens/image17.png)

## Modelagem

### Análise Exploratória

Identificando quais combinações de fatores estão associadas a uma proporção maior de acidentes graves ou fatais:
-   Taxa de gravidade = acidentes graves ou fatais ÷ total de acidentes da categoria ou combinação.
-   Referência: taxa geral de 28,25%, uma categoria “aumenta a chance” só se ficar acima dela.
-   Medidas de comparação: diferença em pontos percentuais (pp) e razão (taxa ÷ taxa geral)
-   Mínimo de 100 acidentes por combinação.

`tipo_acidente` × `fase_dia`:

![alt text](/imagens/image34.png)

-   O tipo de acidente foi o fator que mais separou a gravidade: Atropelamento de Pedestre e Colisão frontal ficaram mais de duas vezes acima da média, enquanto fatores de contexto, como dia da semana, fase do dia e clima, variaram pouco isoladamente.
-   Atropelamento de pedestre à noite chegou a 72% de acidentes graves ou fatais, contra 59% durante o dia, enquanto a colisão frontal quase não mudou com o horário.

`tipo_acidente` × `condicao_metereologica`:

![alt text](/imagens/image35.png)

-   O clima altera pouco a gravidade dentro de cada tipo: Atropelamento de Pedestre ficou entre cerca de 55% e 68% e Colisão frontal entre 56% e 66% em todas as condições, enquanto a diferença entre os tipos de acidente passou de 40 pontos percentuais.
-   Em chuva e garoa, a taxa ficou abaixo da registrada em céu claro na maioria dos tipos (ex: Capotamento 15% x 23%), padrão que se repete dentro de cada tipo.

`tipo_acidente` × `clima`:

![alt text](/imagens/image36.png)

-   O clima altera pouco a gravidade dentro de cada tipo: Atropelamento de Pedestre variou entre 55% e 68% e Colisão frontal entre 56% e 66%, enquanto a diferença entre os tipos passou de 40 pontos percentuais.
-   Em chuva e garoa, a taxa ficou abaixo da registrada em céu claro em vários tipos de grande volume, ex: Saída de leito carroçável (17% x 25%), Colisão com objeto (15% x 24%) e Colisão frontal (56% x 64%).

Combinações com maior taxa de gravidade:

![alt text](/imagens/image37.png)

-   8 das 10 primeiras envolveram Atropelamento de Pedestre ou Colisão frontal, com taxas entre 58,7% e 73,0%, mais que o dobro da taxa geral (28,25%).
-   As maiores taxas foram Atropelamento de Pedestre no amanhecer (73,0%, 141) e em plena noite (72,6%, 1.687), seguidas pela Colisão frontal no amanhecer (69,8%, 295) e em plena noite (63,9%, 2.067).

Excesso de acidentes:

![alt text](/imagens/image38.png)

-   Para medir o impacto de cada combinação, foi calculado o excesso de acidentes graves ou fatais em relação ao esperado pela taxa geral (graves ou fatais observados - total multiplicado por 28,25%).
-   Atropelamento de Pedestre em plena noite (excesso de 748), Colisão frontal em plena noite (737) e em pleno dia (712) e Atropelamento de Pedestre em pleno dia (316) se destacam.

### Modelos

1-
Modelo base: RandomForest

![alt text](/imagens/image18.png)

-   Acurácia global: 72%
-   Classe 0 (majoritária): precision de 0.76, recall de 0.90 e f1-score de 0.82.
-   Classe 1 (minoritária): precision de 0.52, recall de 0.26 e f1-score de 0.35.

*Devido ao desbalanceamento original das classes, a análise detalhada por métricas mostrou dificuldades na detecção da classe minoritária. Para melhorar isso, decidi ajustar os pesos das classes:

![alt text](/imagens/image19.png)

-   Acurácia global: caiu de 72% para 64%
-   Classe 0 (majoritária): recall caiu de 0.90 para 0.70 e f1-score caiu de 0.82 para 0.74.
-   Classe 1 (minoritária): recall subiu de 0.26 para 0.49 e f1-score subiu de 0.35 para 0.44.

*Quando forçamos o modelo a prestar atenção na classe menor, ele começa a errar um pouco mais a classe maior para compensar, essa queda na acurácia global é como se fosse o "preço" que pagamos para deixar o modelo melhor.

Analisando a importância das variáveis:

![alt text](/imagens/image20.png)

-   Colisão frontal e Atropelamento de Pedestre lideram com folga a importância em ambos os cenários, mostrando que a dinâmica e a gravidade do acidente são os principais direcionadores do modelo.

Analisando a Curva ROC:

![alt text](/imagens/image21.png)

-   Com valor 0.63 de AUC, o desempenho do modelo é considerado **fraco**.
-   A curva ROC está muito próxima da diagonal de referência, indicando que o modelo balanceado ainda encontra dificuldades para separar com clareza a classe minoritária da majoritária, gerando um número considerável de falsos positivos e falsos negativos.

2-
Novo modelo: LogisticRegression

![alt text](/imagens/image23.png)

-   Acurácia global: 75%
-   Classe 0 (majoritária): precision de 0.76, recall de 0.95 e f1-score de 0.84.
-   Classe 1 (minoritária): precision de 0.65, recall de 0.24 e f1-score de 0.35.

LogisticRegression com ajuste de peso:

![alt text](/imagens/image22.png)

-   Acurácia global: de 75% para 68%
-   Classe 0 (majoritária): recall caiu de 0.95 para 0.74 e f1-score caiu de 0.84 para 0.77.
-   Classe 1 (minoritária): recall subiu de 0.24 para 0.53 e f1-score subiu de 0.35 para 0.48.

Analisando a importância das variáveis:

![alt text](/imagens/image25.png)

-   Os valores mudam pouco de um cenário para o outro, demonstrando uma grande consistência nas principais variáveis identificadas pelo modelo linear (Colisão frontal e Atropelamento de Pedestre)

Analisando a Curva ROC:

![alt text](/imagens/image24.png)

-   AUC: 0.68, o desempenho do modelo é melhor que o modelo anterior.

3-
Novo modelo: DecisionTree

![alt text](/imagens/image26.png)

-   Acurácia global: 72%
-   Classe 0 (majoritária): precision de 0.75, recall de 0.91 e f1-score de 0.82.
-   Classe 1 (minoritária): precision de 0.52, recall de 0.24 e f1-score de 0.33.

DecisionTree com ajuste de peso:

![alt text](/imagens/image27.png)

-   Acurácia global: de 72% para 62%
-   Classe 0 (majoritária): recall caiu de 0.91 para 0.65 e f1-score caiu de 0.82 para 0.71.
-   Classe 1 (minoritária): recall subiu de 0.24 para 0.54 e f1-score subiu de 0.33 para 0.44.

Analisando a importância das variáveis:

![alt text](/imagens/image28.png)

-   Assim como nos outros modelos, Colisão frontal e Atropelamento de Pedestre dominam o topo da importância (somando mais de 24%).

Analisando a Curva ROC:

![alt text](/imagens/image29.png)

-   AUC: 0.62, o desempenho do modelo é o menor entre os testados, sendo considerado **fraco**.

4-
Novo modelo: GradientBoosting

![alt text](/imagens/image30.png)

-   Acurácia global: 75%
-   Classe 0 (majoritária): precision de 0.76, recall de 0.95 e f1-score de 0.85.
-   Classe 1 (minoritária): precision de 0.66, recall de 0.23 e f1-score de 0.34.

GradientBoosting com ajuste de peso:

![alt text](/imagens/image31.png)

-   Acurácia global: de 75% para 69%
-   Classe 0 (majoritária): recall caiu de 0.95 para 0.76 e f1-score caiu de 0.85 para 0.78.
-   Classe 1 (minoritária): recall subiu de 0.23 para 0.5 e f1-score subiu de 0.34 para 0.47.

Analisando a importância das variáveis:

![alt text](/imagens/image32.png)

-   Colisão frontal e Atropelamento de Pedestre continuam liderando com folga, respondendo por mais de 60% da decisão do modelo.
-   O modelo balanceado valorizou ocorrências como Incêndio.

Analisando a Curva ROC:

![alt text](/imagens/image33.png)

-   AUC: 0.68, modelo com melhor desempenho juntamente ao modelo LogisticRegression.

## Avaliação

O objetivo do projeto era identificar quais combinações de fatores aumentam a chance de um acidente ser grave ou fatal:

-   **Combinações de fatores (atingido):** o cruzamento de variáveis mostrou que a gravidade depende da combinação entre o tipo de acidente e o contexto. O efeito da fase do dia, por exemplo, varia conforme o tipo: no Atropelamento de Pedestre, a taxa foi de 72,6% em plena noite contra 58,7% em pleno dia (+14 pp), enquanto na Colisão frontal a diferença foi de 1,9 pp (63,9% contra 62,0%).
-   **Combinações de maior impacto (atingido):** ao ordenar pelo excesso de acidentes graves ou fatais em relação ao esperado pela taxa geral (28,25%), destacaram-se Atropelamento de Pedestre em plena noite (748), Colisão frontal em plena noite (737), Colisão frontal em pleno dia (712) e Atropelamento de Pedestre em pleno dia (316). Essas quatro combinações reúnem 6.904 acidentes (cerca de 9,5% da base) e 4.464 acidentes graves ou fatais (cerca de 22% do total).
-   **Fatores de contexto (atingido):** dia da semana, fase do dia e condição meteorológica variaram pouco quando analisados isoladamente. Chuva e garoa ficaram abaixo da taxa registrada em céu claro em vários tipos de acidente, padrão que a base não permite explicar.
-   **Classificação da gravidade (parcialmente atingido):** os modelos conseguiram separar as classes melhor que o acaso, mas com desempenho médio a fraco (AUC entre 0,62 e 0,68).

### Avaliação dos modelos

Os 4 algoritmos foram avaliados no conjunto de teste (14.506), com e sem ajuste de peso das classes. Como a classe 1 (`grave_ou_fatal`) representa cerca de 28% dos casos, a acurácia isolada não é suficiente: um modelo que previsse sempre "sem gravidade" acertaria cerca de 71,75%. Por isso, a comparação considera principalmente o recall e o f1-score da classe 1.

| Modelo | Acurácia | Recall (classe 1) | F1 (classe 1) | AUC |
|---|---|---|---|---|
| RandomForest | 72% | 0,26 | 0,35 | - |
| RandomForest com peso | 64% | 0,49 | 0,44 | 0,63 |
| LogisticRegression | 75% | 0,24 | 0,35 | - |
| LogisticRegression com peso | 68% | 0,53 | 0,48 | 0,68 |
| DecisionTree | 72% | 0,24 | 0,33 | - |
| DecisionTree com peso | 62% | 0,54 | 0,44 | 0,62 |
| GradientBoosting | 75% | 0,23 | 0,34 | - |
| GradientBoosting com peso | 69% | 0,50 | 0,47 | 0,68 |

Principais observações:

-   Sem ajuste de peso, os modelos acertam a classe majoritária, mas acertam apenas cerca de um quarto dos acidentes graves ou fatais (recall entre 0,23 e 0,26). RandomForest e DecisionTree (72%) ficaram praticamente iguais à linha de base em acurácia.
-   O ajuste de peso dobrou o recall da classe 1, porém com uma leve queda na acurácia. Esse é o "custo" de alertar mais casos graves, ao preço de mais alarmes falsos.
-   Os melhores resultados foram da LogisticRegression e do GradientBoosting com peso (AUC 0,68 e f1 de 0,48 e 0,47).
-   **Modelo escolhido: LogisticRegression com peso**, pelo melhor f1 da classe 1, pelo AUC equivalente ao do GradientBoosting e por ser o modelo mais simples e interpretável.
-   Comparação entre treino e teste para verificar overfitting:

![alt text](/imagens/image39.png)

### Consistência entre a análise exploratória e os modelos

-   Os quatro modelos apontaram as mesmas categorias de `tipo_acidente` como mais relevantes (Colisão frontal e Atropelamento de Pedestre), o que coincide com as maiores taxas de gravidade nos fatores isolados (67,2% e 63,3%).
-   Essa convergência entre duas abordagens independentes (cruzamento de variáveis e modelagem) aumenta a confiança no resultado. Já o efeito combinado com a fase do dia só aparece com clareza no cruzamento, que por isso é a evidência principal para responder à pergunta do projeto.