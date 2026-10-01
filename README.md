# Checkpoint 02 - SERS - APIs de Energia Renovável e Aprendizado de Máquina

## Objetivo

Consumir duas APIs públicas (ANEEL/SIGA e Open-Meteo), organizar os dados e resolver duas tarefas de aprendizado de máquina: classificar a fonte de geração de empreendimentos cadastrados na ANEEL e estimar a radiação solar horária em Petrolina (PE). Em cada tarefa, três algoritmos diferentes foram treinados e comparados na mesma divisão de treino/teste. A atividade inclui ainda uma parte complementar no Orange Data Mining, reproduzindo as duas tarefas sobre os mesmos dados.

## Dados

- **`aneel_classificacao_orange.csv`** — 3.876 empreendimentos de geração cadastrados no [SIGA/ANEEL](https://dadosabertos.aneel.gov.br/dataset/siga-sistema-de-informacoes-de-geracao-da-aneel): potência outorgada (kW), latitude e longitude aproximadas, e a fonte (Solar, Eólica ou Hidráulica).
- **`meteo_regressao_orange.csv`** — 1.001 registros horários de Petrolina (PE), coordenadas aproximadas −9,39/−40,50, no período de 01/04/2025 a 30/06/2025 (fuso `America/Recife`), entre 7h e 17h, obtidos da [API histórica Open-Meteo](https://open-meteo.com/en/docs/historical-weather-api): temperatura, umidade, cobertura de nuvens, velocidade do vento, hora do dia e radiação solar global horizontal.

Ambos os arquivos já estão neste repositório, gerados pelas células iniciais do notebook a partir das APIs públicas (nenhuma delas exige token).

## Como executar

1. Abrir `CP2_SERS_2SEM.ipynb` no Google Colab ou Jupyter.
2. Executar as células em ordem, a partir de um ambiente limpo (**Ambiente de execução → Executar tudo**, no Colab). As duas primeiras seções refazem a consulta às APIs e regeneram os CSVs; as seções seguintes carregam esses CSVs e fazem a análise e os modelos.

## Resultados e conclusões

### Tarefa 1 — Classificação da fonte (ANEEL)

Três classificadores (KNN, Regressão Logística, Random Forest) foram treinados na mesma divisão estratificada (80%/20%, `random_state=17`), com métricas em média macro:

| Modelo | Accuracy | Precision (macro) | Recall (macro) | F1 (macro) |
|---|---|---|---|---|
| KNN | 0.9626 | 0.9626 | 0.9610 | 0.9617 |
| Regressão Logística | 0.8170 | 0.8235 | 0.8120 | 0.8110 |
| Random Forest | 0.9781 | 0.9783 | 0.9767 | 0.9773 |

O **Random Forest** teve o melhor desempenho (F1 macro 0,977). Em todos os modelos, o par de classes mais confundido é **Solar × Eólica**, enquanto a classe **Hidráulica** é a mais bem separada — provavelmente porque potência outorgada e localização não diferenciam a tecnologia de geração em si, e parques solares e eólicos de porte semelhante costumam estar instalados em regiões próximas no Brasil.

### Tarefa 2 — Regressão da radiação solar (Open-Meteo)

Três regressores (Regressão Linear, Random Forest Regressor, Decision Tree Regressor) foram treinados preservando a ordem temporal (80% das primeiras horas para treino, 20% finais para teste):

| Modelo | MAE (W/m²) | MSE ((W/m²)²) | R² |
|---|---|---|---|
| Regressão Linear | 145.20 | 30034.20 | 0.3598 |
| Random Forest Regressor | 66.10 | 7211.02 | 0.8463 |
| Decision Tree Regressor | 86.29 | 14262.67 | 0.6960 |

O **Random Forest Regressor** teve o melhor desempenho (R² 0,846). A diferença grande em relação à Regressão Linear (R² 0,360) indica que a relação entre a hora do dia e a radiação não é linear (a radiação sobe e desce em arco ao longo do dia). Estimar radiação solar (W/m²) não equivale a prever geração elétrica: faltam variáveis como área e eficiência dos painéis, ângulo de instalação, perda de eficiência por temperatura do módulo e perdas do inversor.

## Parte complementar — Orange Data Mining

Fluxos de classificação e regressão equivalentes aos do notebook, usando o workflow `Fluxos_para_Classificacao_e_Regressao.ows` sobre os mesmos dois CSVs. Análise completa com prints dos resultados em [`Orange_Analise.md`](./Orange_Analise.md).

## Integrantes

| Nome | RM |
|---|---|
| Gabriel Botelho Romão | 570589 |
| Thor Ferreira Camargo | 569543 |
| Léo Moreno Sambo | 569556 |
| Fernando Hideki Rosa Oda | 571408 |

## Fontes

- [ANEEL/SIGA — Sistema de Informações de Geração](https://dadosabertos.aneel.gov.br/dataset/siga-sistema-de-informacoes-de-geracao-da-aneel)
- [Open-Meteo — Historical Weather API](https://open-meteo.com/en/docs/historical-weather-api)
