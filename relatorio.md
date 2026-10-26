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

![alt text](/imagens/image16.png)

7-
OneHotEncoding: transformando colunas com palavras/textos em colunas binárias(0 ou 1).

![alt text](/imagens/image17.png)

## Modelagem
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
-   Classe 0 (majoritária): recall caiu de 0.76 para 0.74 e f1-score caiu de 0.84 para 0.77.
-   Classe 1 (minoritária): recall caiu de 0.65 para 0.53 e f1-score subiu de 0.35 para 0.48.

Analisando a importância das variáveis:

![alt text](/imagens/image25.png)

-   Os valores mudam pouco de um cenário para o outro, demonstrando uma grande consistência nas principais variáveis identificadas pelo modelo linear (Colisão frontal e Atropelamento de Pedestre)

Analisando a Curva ROC:

![alt text](/imagens/image24.png)

-   AUC: 0.68, o desempenho do modelo é melhor que o modelo anterior, mas também é considerado **fraco**.

3-