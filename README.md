# Acidentes em Rodovias Federais (2025)

Projeto acadêmico da disciplina **CCD210 – Fundamentos de Ciência e Visualização de Dados** (FEI), desenvolvido seguindo as etapas do modelo **CRISP-DM**.

## Sobre o projeto

Análise dos acidentes registrados pela Polícia Rodoviária Federal (PRF) em 2025, relacionada à **ODS 3 (Saúde e Bem-Estar)**, com foco na redução de mortes e lesões no trânsito.

**Pergunta central:** quais combinações de fatores aumentam a chance de um acidente ser grave ou fatal?

Um acidente é considerado **grave ou fatal** quando tem pelo menos uma vítima fatal (`mortos > 0`) ou um ferido grave (`feridos_graves > 0`).

## Principais resultados

- A taxa geral de acidentes graves ou fatais em 2025 foi de **28,25%**.
- O tipo de acidente foi o fator que mais separou a gravidade: **Atropelamento de Pedestre (67,2%)** e **Colisão frontal (63,3%)** ficaram mais de duas vezes acima da média.
- O efeito do horário depende do tipo de acidente: no atropelamento de pedestre, a taxa foi de 72,6% em plena noite contra 58,7% em pleno dia, enquanto na colisão frontal a diferença foi pequena.
- Foram testados quatro modelos de classificação (RandomForest, Regressão Logística, Árvore de Decisão e Gradient Boosting). O modelo escolhido foi a **Regressão Logística com ajuste de peso das classes** (AUC de 0,68).

Os detalhes completos estão no arquivo `relatorio.md` (relatório completo do projeto).

## Estrutura do repositório

| Arquivo / pasta | Descrição |
|---|---|
| `imagens/` | Contém todas as imagens usadas nos relatórios do projeto |
| `acidentes.ipynb` | Contém todos os códigos usados no Google Colab, desde a primeira etapa(Entendimento do Negócio) até a última(Modelagem) |
| `dataset.md` | Relatório com a descrição dos atributos do dataset |
| `datatran2025.csv` | Dataset usado no projeto |
| `Dicionário de Variáveis_ocorrencia_2017.pdf` | Dicionário das variáveis do dataset |
| `relatorio.md` | Relatório completo do projeto |
| `README.md` | Este arquivo |

## Fontes

O dataset e o dicionário de variáveis foram disponibilizados pela Polícia Rodoviária Federal (PRF) no site do GOV:
https://www.gov.br/prf/pt-br/acesso-a-informacao/dados-abertos/dados-abertos-da-prf