### Exploração Inicial dos Dados
 
 ```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.preprocessing import StandardScaler
from sklearn.cluster import KMeans
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split
from sklearn.metrics import r2_score, mean_squared_error
import plotly.express as px
import plotly.graph_objects as go
from plotly.subplots import make_subplots
import dash
from dash import dcc, html
from dash.dependencies import Input, Output
import warnings
warnings.filterwarnings("ignore")

#Carregando os dados
df = pd.read_csv('dados/world_tourism_economy_data.csv')

#Visualização inicial
print("Primeiras linhas do dataset:")
print(df.head())
print("\nInformações sobre o dataset:")
print(df.info())
print("\nEstatísticas descritivas:")
print(df.describe())

#Verificando se contém valores nulos
print("\nValores nulos por coluna:")
print(df.isnull().sum())

#Verificando se há dados duplicados
print("\nNúmero de linhas duplicadas:", df.duplicated().sum())
```

![01](https://github.com/user-attachments/assets/08986e35-fb9d-4f4f-ae5b-57c4291d3879)

<hr>
<br>

### Análise Exploratória

```python
#Configurando os gráficos
plt.figure(figsize=(15, 10))

#Distribuição das receitas do turismo
plt.subplot(2, 2, 1)
sns.histplot(np.log1p(df['tourism_receipts']), kde=True)
plt.title('Distribuição das Receitas do Turismo (log)')

#Distribuição do PIB
plt.subplot(2, 2, 2)
sns.histplot(np.log1p(df['gdp']), kde=True)
plt.title('Distribuição do PIB (log)')

#Relação entre receitas do turismo e PIB
plt.subplot(2, 2, 3)
sns.scatterplot(x=np.log1p(df['gdp']), y=np.log1p(df['tourism_receipts']))
plt.title('Receitas do Turismo vs PIB (log)')
plt.xlabel('Log PIB')
plt.ylabel('Log Receitas do Turismo')

#Correlação entre variáveis do tipo numérico
plt.subplot(2, 2, 4)
corr_matrix = df[numeric_cols].corr()
sns.heatmap(corr_matrix, annot=True, cmap='coolwarm')
plt.title('Matriz de Correlação')

plt.tight_layout()
plt.show()
```

![02](https://github.com/user-attachments/assets/8d970eb2-4fe9-4eb5-ad70-fcd1be1d87e6)

<hr>
<br>

### Análise Temporal

```python
    #Agregando os dados por ano
annual_data = df.groupby('year').agg({
    'tourism_receipts': 'sum',
    'tourism_arrivals': 'sum',
    'gdp': 'sum',
    'inflation': 'mean',
    'unemployment': 'mean'
}).reset_index()

#Visualizando as tendências
fig, axes = plt.subplots(2, 2, figsize=(15, 10))

#Receitas do turismo
axes[0, 0].plot(annual_data['year'], annual_data['tourism_receipts']/1e9)
axes[0, 0].set_title('Receitas do Turismo Global (em bilhões)')
axes[0, 0].set_ylabel('Bilhões USD')

#Chegadas de turistas
axes[0, 1].plot(annual_data['year'], annual_data['tourism_arrivals']/1e6)
axes[0, 1].set_title('Chegadas de Turistas Globais (em milhões)')
axes[0, 1].set_ylabel('Milhões')

#Inflação média
axes[1, 0].plot(annual_data['year'], annual_data['inflation'])
axes[1, 0].set_title('Inflação Média Global')
axes[1, 0].set_ylabel('Percentual')

#Desemprego médio
axes[1, 1].plot(annual_data['year'], annual_data['unemployment'])
axes[1, 1].set_title('Desemprego Médio Global')
axes[1, 1].set_ylabel('Percentual')

plt.tight_layout()
plt.show()
```

![03](https://github.com/user-attachments/assets/475d6cc8-2abc-45f6-9987-147b422ebe7e)

<hr>
<br>

















