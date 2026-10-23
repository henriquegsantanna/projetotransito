# Acidentes em rodovias federais (Polícia Rodoviária Federal)

## Dataset

Neste projeto, foi utilizado o dataset [Acidentes 2025(Agrupados por ocorrência)](https://www.gov.br/prf/pt-br/acesso-a-informacao/dados-abertos/dados-abertos-da-prf), disponibilizado no site do GOV. A base de dados reúne informações de acidentes ocorridos em rodovias federais em 2025, contendo 72.529 linhas e 30 colunas inicialmente.

### Objetivo do projeto

-   Descobrir quais combinações de fatores (horário, tipo de pista, causa, etc) aumentam a chance de um acidente ser grave ou fatal.
-   Tipo de problema: Classificação (grave ou fatal)

## Descrição dos Atributos
Abaixo está o [Dicionário de Dados - Infrações](https://www.gov.br/prf/pt-br/acesso-a-informacao/dados-abertos/dicionario-infracoes) disponibilizado pela PRF no site do GOV, que descreve cada atributo do dataset.

![alt text](/imagens/Captura%20de%20tela%202026-10-23%20092336.png)
![alt text](/imagens/Captura%20de%20tela%202026-10-23%20092400.png)

## Distribuição das classes
O atributo `classificacao_acidente` classifica os acidentes em 3 situações:
-   Sem Vítimas
-   Com Vítimas Feridas
-   Com Vítimas Fatais

![alt text](/imagens/image.png)

## Valores faltantes
Foram identificados poucos valores nulos/faltantes no dataset e que não irão comprometer a análise.

![alt text](/imagens/image2.png)

-   1 valor nulo na coluna `classificacao_acidente`
-   2 valores nulos na coluna `regional` (não será utilizado na análise)
-   22 valores nulos na coluna `delegacia` (não será utilizado na análise)
-   38 valores nulos na coluna `uop` (não será utilizado na análise)
*números extremamente baixos, levando em consideração que o dataset possui 72.529 linhas e que 3 das 4 colunas com valores nulos não serão usadas na análise.

## Valores duplicados
Foi verificado se havia alguma linha ou id de acidente duplicado, e como resultado, nenhum valor duplicado.
![alt text](/imagens/image3.png)