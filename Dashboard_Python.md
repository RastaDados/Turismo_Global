# Dashboard Utilizando a Biblioteca Dash do Python

<hr>
<br>

```python
#Criando o aplicativo Dash
app = dash.Dash(__name__)

#Layout do dashboard
#No título coloquei aqua, se o tema for dark ou light vai dar pra visualizar da mesma forma.
app.layout = html.Div([
    html.H1("Análise do Turismo Mundial (1999-2002)", style={'textAlign': 'center', 'color': 'aqua'}),
    
    html.Div([
        dcc.Dropdown(
            id='year-selector',
            options=[{'label': str(year), 'value': year} for year in df['year'].unique()],
            value=2002,
            style={'width': '50%'}
        )
    ], style={'padding': '20px'}),
    
    html.Div([
        dcc.Graph(id='receipts-map'),
        dcc.Graph(id='arrivals-scatter')
    ], style={'display': 'flex'}),
    
    html.Div([
        dcc.Graph(id='top-countries'),
        dcc.Graph(id='cluster-analysis')
    ], style={'display': 'flex'}),
    
    html.Div([
        dcc.Graph(id='time-series')
    ])
])

#Callbacks para filtros
@app.callback(
    [Output('receipts-map', 'figure'),
     Output('arrivals-scatter', 'figure'),
     Output('top-countries', 'figure'),
     Output('cluster-analysis', 'figure'),
     Output('time-series', 'figure')],
    [Input('year-selector', 'value')]
)
def update_dashboard(selected_year):
    #Filtrando os dados pelo ano selecionado
    year_data = df[df['year'] == selected_year]
```


### Receitas do Turismo por País

```python
 #Mapa de receitas do turismo
    receipts_map = px.choropleth(
        year_data,
        locations="country_code",
        color="tourism_receipts",
        hover_name="country",
        color_continuous_scale=px.colors.sequential.Plasma,
        title=f"Receitas do Turismo por País ({selected_year})"
    )
```

![01](https://github.com/user-attachments/assets/dbf75df0-56e5-4d23-a8e5-cef5658206bb)

<hr>
<br>

### Receitas vs Chegadas de Turistas

```python
 #Dispersão: Receitas vs Chegadas
    arrivals_scatter = px.scatter(
        year_data,
        x="tourism_arrivals",
        y="tourism_receipts",
        size="gdp",
        color="tourism_balance",
        hover_name="country",
        log_x=True,
        log_y=True,
        title=f"Receitas vs Chegadas de Turistas ({selected_year})"
    )
```

![02](https://github.com/user-attachments/assets/0fbc9c42-ec6d-4a17-9fcf-65df5068457a)

<hr>
<br>

### Top 10 Países Por Receita

```python
#Top 10 países por receitas
    top_countries = year_data.nlargest(10, 'tourism_receipts')
    top_chart = px.bar(
        top_countries,
        x="country",
        y="tourism_receipts",
        color="tourism_intensity",
        title=f"Top 10 Países por Receitas do Turismo ({selected_year})"
    )
```

![03](https://github.com/user-attachments/assets/3257c6d6-28fd-457b-b6a4-b90b9c36305a)

<hr>
<br>

### Análise dos Clusters

```python
cluster_fig = px.scatter(
        cluster_data,
        x="tourism_receipts",
        y="tourism_arrivals",
        color="cluster",
        size="gdp",
        hover_name=df[df['year'] == selected_year]['country'],
        log_x=True,
        log_y=True,
        title="Clusterização de Países por Perfil Turístico"
    )
```

![04](https://github.com/user-attachments/assets/036f16d4-e4f4-4295-a4eb-996793953c41)

<hr>
<br>

### Tendências Globais do Turismo

```python
    #Série temporal
    time_series = make_subplots(specs=[[{"secondary_y": True}]])
    time_series.add_trace(
        go.Scatter(x=annual_data['year'], y=annual_data['tourism_receipts']/1e9, name="Receitas (bilhões)"),
        secondary_y=False
    )
    time_series.add_trace(
        go.Scatter(x=annual_data['year'], y=annual_data['tourism_arrivals']/1e6, name="Chegadas (milhões)"),
        secondary_y=True
    )
    time_series.update_layout(title="Tendências Globais do Turismo")
    time_series.update_yaxes(title_text="Receitas (bilhões USD)", secondary_y=False)
    time_series.update_yaxes(title_text="Chegadas (milhões)", secondary_y=True)
    
    return receipts_map, arrivals_scatter, top_chart, cluster_fig, time_series
```

![05](https://github.com/user-attachments/assets/0cd2482b-467b-4392-9042-bdd72340a0d5)



