# Relatório do Projeto - Acidentes em rodovias federais

## Entendimento do Negócio

### Definição do Problema e do Projeto
O projeto tem como objetivo investigar e responder a seguinte pergunta: quais combinações de fatores aumentam a chance de um acidente ser grave ou fatal?

Para isso, o problema será tratado como um problema de classificação, e para fazer essa investigação, será utilizado conceitos de Machine Learning para identificar quais atributos mais alteram o resultado na variável-alvo `classificação de um acidente`.

A ODS relacionada a este projeto é a ODS 3 (Saúde e Bem-Estar), com o objetivo de reduzir mortes e lesões no trânsito.

## Entendimento dos Dados
Dataset utilizado: [Acidentes 2025(Agrupados por ocorrência)](https://www.gov.br/prf/pt-br/acesso-a-informacao/dados-abertos/dados-abertos-da-prf), disponibilizado pela Polícia Rodoviária Federal no site do GOV, reunindo informações sobre acidentes em rodovias federais em 2025.

O dataset possui 72.529 linhas e 30 colunas (sem alterações), e foram identificados alguns valores nulos nas colunas `classificacao_acidente`, `regional`, `delegacia` e `uop`, porém em quantidade extremamente baixa, oque não afetará a análise. E por fim, não foi identificado nenhuma linha/valores duplicados.

A variável `classificacao_acidente` foi definida como variável-alvo, contendo:
-   Acidentes Com Vítimas Feridas: 56.181 (77,46%)
-   Acidentes Com Vítimas Fatais: 11.138 (15,36%)
-   Acidentes Sem Vítimas: 5.209 (7,18%)

A descrição detalhada dos atributos, da distribuição das classes e dos valores ausentes encontra-se no arquivo `descdataset.md`.

## Preparação dos Dados
1-
Filtrei as colunas `pessoas`, `mortos`, `feridos_leves`, `feridos_graves`, `ilesos`, `ignorados` e `feridos` da linha de índice 1 (linha que possuía valor nulo na coluna `classificacao_acidente`) e, de acordo com o resultado das colunas filtradas, classifiquei o acidente.

![alt text](/imagens/image4.png)

Como houve pessoas mortas no acidente, alterei a coluna `classificacao_acidente` de nulo para "Com Vítimas Fatais".

2-
(implementar coluna criada - variavel preditora)