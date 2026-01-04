# AnaliseDescritivaPython
# Análise Descritiva da Qualidade do Ar por Hora do Dia

Este documento descreve e justifica, passo a passo, o código utilizado para realizar a **análise descritiva de dados de qualidade do ar**, considerando **médias horárias** ao longo do dia. O objetivo é explicar como os dados foram tratados, analisados e visualizados, bem como como essas etapas sustentam as conclusões obtidas.

---

## 1. Importação das bibliotecas

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
```

* **pandas**: utilizado para leitura, manipulação e agregação dos dados.
* **matplotlib.pyplot**: responsável pela criação dos gráficos de linha.
* **seaborn**: biblioteca estatística auxiliar (importada para possíveis extensões visuais, embora não utilizada diretamente no gráfico final).

---

## 2. Leitura do conjunto de dados

```python
qualidade_ar = pd.read_csv('Brasilia_Air_Quality.csv')
qualidade_ar.head()
```

Nesta etapa, o arquivo CSV contendo os dados de qualidade do ar de Brasília é carregado em um DataFrame. A função `head()` é utilizada para uma inspeção inicial da estrutura e dos valores.

---

## 3. Tratamento e padronização dos dados

### 3.1 Renomeação das colunas

```python
qualidade_ar.rename(columns={
    'Date':'Data_Hora',
    'City':'Cidade',
    'CO':'Conc_Monoxido_Carbono',
    'NO2':'Conc_Nitrogenio',
    'SO2':'Conc_Sulfúrico',
    'O3':'Conc_Ozone',
    'AQI':'Indice_Qualidade_Ar'
}, inplace=True)
```

A renomeação das colunas tem dois objetivos principais:

* Tornar os nomes mais **descritivos e legíveis**
* Padronizar a nomenclatura para facilitar a interpretação e a análise estatística

---

### 3.2 Conversão para tipo datetime

```python
qualidade_ar['Data_Hora'] = pd.to_datetime(qualidade_ar['Data_Hora'])
qualidade_ar['Data_Hora'].dt.normalize()
```

A conversão da coluna de data e hora para o tipo `datetime` é essencial para:

* Extração de componentes temporais (hora, dia, mês)
* Análises de séries temporais

A normalização remove a parte de hora caso fosse necessário trabalhar apenas com datas (embora, neste caso, a hora seja utilizada posteriormente).

---

### 3.3 Extração da hora do dia

```python
qualidade_ar['Hora'] = qualidade_ar['Data_Hora'].dt.hour
```

Aqui é criada a variável **Hora**, que representa o horário do dia (0 a 23). Essa coluna é fundamental para a agregação dos dados por hora e para a análise do comportamento médio diário dos poluentes.

---

### 3.4 Verificação de valores ausentes

```python
qualidade_ar.isnull().sum()
```

Esta etapa permite identificar possíveis valores ausentes que poderiam impactar cálculos estatísticos ou visualizações.

---

## 4. Estatística descritiva básica

```python
qualidade_ar.describe()
```

O método `describe()` fornece um resumo estatístico das colunas numéricas, incluindo:

* Média
* Desvio padrão
* Valores mínimos e máximos
* Quartis

Essa etapa oferece uma visão inicial da distribuição e magnitude dos dados.

---

## 5. Mensuração da variabilidade dos dados

### 5.1 Identificação de colunas numéricas do tipo float

```python
col_mudar = []

for col in qualidade_ar.columns:
    if pd.api.types.is_float_dtype(qualidade_ar[col]):
        col_mudar.append(col)
```

Este bloco identifica automaticamente as colunas numéricas contínuas (`float`), que são adequadas para cálculos de variabilidade.

---

### 5.2 Cálculo de métricas de dispersão

```python
for col in col_mudar:
    print(f"\nAtributo: {col}")
    print("Desvio Padrão:", qualidade_ar[col].std())
    print("Variância:", qualidade_ar[col].var())
    print("Range:", qualidade_ar[col].max() - qualidade_ar[col].min())
```

Essas métricas permitem avaliar:

* **Desvio padrão**: o quanto os valores se afastam da média
* **Variância**: medida quadrática da dispersão
* **Range**: amplitude total dos dados

No contexto ambiental, alta variabilidade pode indicar picos de poluição em determinados períodos.

---

## 6. Análise descritiva por hora do dia

### 6.1 Cálculo das médias horárias

```python
media_por_hora = qualidade_ar.groupby('Hora')['Indice_Qualidade_Ar'].mean()
media_por_hora_carbono = qualidade_ar.groupby('Hora')['Conc_Monoxido_Carbono'].mean()
media_por_hora_nitro = qualidade_ar.groupby('Hora')['Conc_Nitrogenio'].mean()
media_por_hora_sulf = qualidade_ar.groupby('Hora')['Conc_Sulfúrico'].mean()
media_por_hora_oz = qualidade_ar.groupby('Hora')['Conc_Ozone'].mean()
media_por_hora_PM25 = qualidade_ar.groupby('Hora')['PM2.5'].mean()
```

A agregação por hora do dia elimina variações pontuais diárias e revela o **comportamento médio diário** dos poluentes e do índice de qualidade do ar.

---

## 7. Visualização dos dados

```python
plt.plot(media_por_hora.index, media_por_hora.values, label='Qualidade do Ar')
plt.plot(media_por_hora_carbono.index, media_por_hora_carbono.values, label='CO')
plt.plot(media_por_hora_nitro.index, media_por_hora_nitro.values, label='NO2')
plt.plot(media_por_hora_sulf.index, media_por_hora_sulf.values, label='SO2')
plt.plot(media_por_hora_oz.index, media_por_hora_oz.values, label='O3')
plt.plot(media_por_hora_PM25.index, media_por_hora_PM25.values, label='PM2.5')
plt.legend()
plt.show()
```

O gráfico de linhas permite:

* Comparar o comportamento horário médio de cada poluente
* Identificar padrões típicos, como picos matinais e vespertinos
* Analisar a relação entre poluentes primários (CO, NO₂) e secundários (O₃)

---

## 8. Conclusão metodológica

O código apresentado sustenta a análise descritiva ao:

* Tratar e padronizar corretamente os dados
* Utilizar estatísticas de tendência central e dispersão
* Explorar a dimensão temporal por meio de médias horárias
* Visualizar simultaneamente múltiplos poluentes e o índice de qualidade do ar

Essas etapas permitem identificar padrões estruturais diários e fundamentar interpretações sobre a dinâmica da poluição atmosférica urbana.
