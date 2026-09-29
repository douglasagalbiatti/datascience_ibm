# 📘 Apostila – Projeto: Extração e Visualização de Dados de Ações
### Tesla • Amazon • AMD • GameStop — com Python, Web Scraping e Plotly

> **Papel:** Cientista/Analista de Dados em uma startup de investimentos.
> **Objetivo:** Extrair preços históricos de ações e receitas trimestrais de várias fontes, tratar os dados e apresentá-los em um dashboard interativo para identificar padrões e tendências.

---

## 📑 Sumário

1. [Visão geral do projeto](#1-visão-geral-do-projeto)
2. [Pré-requisitos e preparação do ambiente](#2-pré-requisitos-e-preparação-do-ambiente)
3. [Conceitos fundamentais](#3-conceitos-fundamentais)
4. [Módulo 1 – Extraindo preços de ações com `yfinance`](#4-módulo-1--extraindo-preços-de-ações-com-yfinance)
5. [Módulo 2 – Web scraping de receitas com `requests` + `BeautifulSoup`](#5-módulo-2--web-scraping-de-receitas-com-requests--beautifulsoup)
6. [Módulo 3 – Limpeza e preparação dos dados](#6-módulo-3--limpeza-e-preparação-dos-dados)
7. [Módulo 4 – Visualização com Plotly](#7-módulo-4--visualização-com-plotly)
8. [Módulo 5 – Pipeline completo para as 4 empresas](#8-módulo-5--pipeline-completo-para-as-4-empresas)
9. [Módulo 6 – Análise e extração de insights](#9-módulo-6--análise-e-extração-de-insights)
10. [Módulo 7 – Dashboard interativo (Plotly Dash) – opcional](#10-módulo-7--dashboard-interativo-plotly-dash--opcional)
11. [Usando prompts (IA generativa) no projeto](#11-usando-prompts-ia-generativa-no-projeto)
12. [Checklist de entrega](#12-checklist-de-entrega)
13. [Solução de problemas (troubleshooting)](#13-solução-de-problemas-troubleshooting)
14. [Exercícios de fixação](#14-exercícios-de-fixação)
15. [Glossário](#15-glossário)

---

## 1. Visão geral do projeto

### 1.1 O problema de negócio
Investidores querem saber: **o preço da ação acompanha o crescimento da receita da empresa?** Para responder, precisamos cruzar duas fontes de dados:

| Tipo de dado | Fonte | Técnica |
|---|---|---|
| Preço histórico da ação (Open, High, Low, Close, Volume, Dividendos, Splits) | Yahoo Finance | Biblioteca `yfinance` (API) |
| Receita trimestral (Revenue) | Página HTML (ex.: Macrotrends ou páginas espelhadas do curso) | Web scraping (`requests` + `BeautifulSoup` / `pandas.read_html`) |

### 1.2 Ações analisadas

| Empresa | Ticker | Setor |
|---|---|---|
| Tesla | `TSLA` | Automotivo / Energia |
| Amazon | `AMZN` | E-commerce / Cloud |
| AMD | `AMD` | Semicondutores |
| GameStop | `GME` | Varejo de games |

### 1.3 Fluxo do projeto

```
 ┌──────────────┐    ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
 │  EXTRAÇÃO    │ -> │  LIMPEZA     │ -> │  ANÁLISE     │ -> │  DASHBOARD   │
 │ yfinance +   │    │ pandas       │    │ estatísticas │    │ Plotly /     │
 │ web scraping │    │ (tipos, NaN) │    │ tendências   │    │ Dash         │
 └──────────────┘    └──────────────┘    └──────────────┘    └──────────────┘
```

### 1.4 Entregáveis
- Notebook Jupyter (`.ipynb`) com todo o código executado e saídas visíveis.
- DataFrames de preço e receita para cada empresa.
- Gráficos Plotly (preço x receita) para cada empresa.
- Dashboard com os principais insights.
- (Opcional) Link público do notebook no GitHub.

---

## 2. Pré-requisitos e preparação do ambiente

### 2.1 Conhecimentos esperados
- Python básico (variáveis, funções, listas, dicionários, laços).
- Noções de `pandas` (DataFrame, `head()`, `tail()`, filtros).
- Noções de HTML (tags `<table>`, `<tr>`, `<td>`).

### 2.2 Ambiente
Você pode usar: **Jupyter Notebook/JupyterLab** local, **Google Colab**, ou o **Skills Network Labs** (IBM).

### 2.3 Instalação das bibliotecas

```bash
pip install yfinance pandas requests beautifulsoup4 lxml html5lib plotly nbformat dash
```

| Biblioteca | Função no projeto |
|---|---|
| `yfinance` | Baixar dados históricos de ações do Yahoo Finance |
| `pandas` | Manipulação de tabelas (DataFrames) |
| `requests` | Baixar o HTML de páginas web |
| `beautifulsoup4` | Interpretar (parse) o HTML e localizar tabelas |
| `lxml` / `html5lib` | Parsers usados pelo BeautifulSoup e pelo `pd.read_html` |
| `plotly` | Gráficos interativos |
| `nbformat` | Necessário para renderizar Plotly em notebooks |
| `dash` | (Opcional) Dashboard web interativo |

### 2.4 Imports padrão (primeira célula do notebook)

```python
import yfinance as yf
import pandas as pd
import requests
from bs4 import BeautifulSoup
from io import StringIO
import plotly.graph_objects as go
from plotly.subplots import make_subplots
import warnings

warnings.filterwarnings("ignore", category=FutureWarning)
pd.set_option("display.max_columns", None)
```

---

## 3. Conceitos fundamentais

### 3.1 Dados de ações (OHLCV)
| Coluna | Significado |
|---|---|
| **Open** | Preço de abertura do dia |
| **High** | Maior preço do dia |
| **Low** | Menor preço do dia |
| **Close** | Preço de fechamento (o mais usado em análises) |
| **Volume** | Quantidade de ações negociadas |
| **Dividends** | Dividendos pagos na data |
| **Stock Splits** | Desdobramentos (ex.: 3 para 1) |

### 3.2 Receita trimestral (Quarterly Revenue)
Total faturado pela empresa em cada trimestre fiscal, geralmente em **milhões de US$**. É um indicador de crescimento do negócio.

### 3.3 API x Web Scraping
- **API** (`yfinance`): dados estruturados, entregues prontos em formato de tabela.
- **Web scraping**: extrair dados de páginas HTML feitas para humanos. Exige localizar a tabela certa e limpar o texto (`$`, `,`, células vazias).

> ⚖️ **Ética e legalidade:** sempre verifique os *Termos de Uso* e o `robots.txt` do site, não sobrecarregue o servidor com muitas requisições e prefira APIs oficiais quando existirem.

---

## 4. Módulo 1 – Extraindo preços de ações com `yfinance`

### 4.1 Criando o objeto `Ticker`

```python
tesla = yf.Ticker("TSLA")
```

### 4.2 Informações gerais da empresa

```python
info = tesla.info            # dicionário com dezenas de campos
print(info.get("longName"), "|", info.get("sector"), "|", info.get("country"))
```

### 4.3 Histórico de preços

```python
tesla_data = tesla.history(period="max")   # todo o histórico disponível
tesla_data.head()
```

Valores aceitos em `period`: `1d, 5d, 1mo, 3mo, 6mo, 1y, 2y, 5y, 10y, ytd, max`.
Também é possível usar datas: `tesla.history(start="2015-01-01", end="2024-12-31")`.

### 4.4 Transformando o índice (Date) em coluna

```python
tesla_data.reset_index(inplace=True)
tesla_data.head()
```

> ✅ **Tarefa 1:** Extraia os dados da Tesla com `period="max"`, faça o `reset_index` e mostre as 5 primeiras linhas com `head()`.

### 4.5 Dividendos

```python
tesla.dividends                 # Série com os dividendos (Tesla praticamente não paga)
yf.Ticker("AMD").dividends
```

### 4.6 Repetindo para as outras empresas

```python
gme_data  = yf.Ticker("GME").history(period="max").reset_index()
amzn_data = yf.Ticker("AMZN").history(period="max").reset_index()
amd_data  = yf.Ticker("AMD").history(period="max").reset_index()
```

> ✅ **Tarefa 2:** Extraia os dados da GameStop e mostre `gme_data.head()`.

> 💡 **Perguntas típicas de avaliação:**
> - Em que país fica a sede da AMD? → `yf.Ticker("AMD").info["country"]`
> - Qual o setor da AMD? → `info["sector"]`
> - Qual o volume negociado no **primeiro dia** do histórico da AMD? → `amd_data["Volume"].iloc[0]`
> - Qual o preço de abertura da Amazon no primeiro dia? → `amzn_data["Open"].iloc[0]`

### 4.7 Atenção ao fuso horário
Versões recentes do `yfinance` retornam datas com fuso horário (`2021-06-14 00:00:00-04:00`). Para evitar erros em comparações e gráficos:

```python
tesla_data["Date"] = pd.to_datetime(tesla_data["Date"]).dt.tz_localize(None)
```

---

## 5. Módulo 2 – Web scraping de receitas com `requests` + `BeautifulSoup`

### 5.1 Fontes de dados

**Páginas estáticas oficiais do curso (IBM Skills Network) — recomendadas, pois não mudam:**

```python
url_tesla_rev = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/revenue.htm"
url_gme_rev   = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/stock.html"
```

**Fonte original (dados atualizados, mas pode bloquear robôs):**

```python
url_macro = {
    "TSLA": "https://www.macrotrends.net/stocks/charts/TSLA/tesla/revenue",
    "AMZN": "https://www.macrotrends.net/stocks/charts/AMZN/amazon/revenue",
    "AMD":  "https://www.macrotrends.net/stocks/charts/AMD/amd/revenue",
    "GME":  "https://www.macrotrends.net/stocks/charts/GME/gamestop/revenue",
}
```

> ⚠️ Os links podem mudar com o tempo. Se algum falhar, verifique a URL no navegador ou use a alternativa via `yfinance` (seção 5.6).

### 5.2 Baixando o HTML

```python
headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 "
                         "(KHTML, like Gecko) Chrome/120.0 Safari/537.36"}

html_data = requests.get(url_tesla_rev, headers=headers, timeout=30).text
print(len(html_data))
```

### 5.3 Fazendo o parse com BeautifulSoup

```python
soup = BeautifulSoup(html_data, "html.parser")   # ou "html5lib"
```

### 5.4 Inspecionando a página
No navegador: **botão direito → Inspecionar**. A página possui duas tabelas:
1. **Annual Revenue** (receita anual)
2. **Quarterly Revenue** (receita trimestral) ← é esta que queremos

```python
for i, table in enumerate(soup.find_all("table")):
    titulo = table.find("th")
    print(i, titulo.get_text(strip=True) if titulo else "sem título")
```

### 5.5 Método A – BeautifulSoup manual (o mais didático)

```python
tesla_revenue = pd.DataFrame(columns=["Date", "Revenue"])

for table in soup.find_all("table"):
    if "Quarterly Revenue" in table.get_text():
        linhas = table.find("tbody").find_all("tr")
        for row in linhas:
            col = row.find_all("td")
            if len(col) >= 2:
                date = col[0].get_text(strip=True)
                revenue = col[1].get_text(strip=True)
                tesla_revenue = pd.concat(
                    [tesla_revenue, pd.DataFrame({"Date": [date], "Revenue": [revenue]})],
                    ignore_index=True,
                )
        break

tesla_revenue.head()
```

> ⚠️ `DataFrame.append()` foi **removido** no pandas 2.x. Use `pd.concat` (como acima) ou monte uma lista e crie o DataFrame no final (mais rápido):

```python
registros = []
# ... dentro do laço: registros.append({"Date": date, "Revenue": revenue})
tesla_revenue = pd.DataFrame(registros)
```

### 5.6 Método B – `pandas.read_html` (o mais rápido)

```python
tabelas = pd.read_html(StringIO(html_data))
tesla_revenue = tabelas[1]                      # índice 1 = Quarterly Revenue
tesla_revenue.columns = ["Date", "Revenue"]
tesla_revenue.head()
```

### 5.7 Alternativa sem scraping – receita via `yfinance`

```python
q = yf.Ticker("TSLA").quarterly_income_stmt      # últimos ~5 trimestres
rev = q.loc["Total Revenue"].reset_index()
rev.columns = ["Date", "Revenue"]
```
> Útil como *plano B*, mas traz poucos trimestres. Para séries longas, o scraping continua sendo necessário.

---

## 6. Módulo 3 – Limpeza e preparação dos dados

### 6.1 Removendo `$` e `,` e convertendo para número

```python
tesla_revenue["Revenue"] = tesla_revenue["Revenue"].astype(str).str.replace(r"[\$,]", "", regex=True)
```

### 6.2 Removendo valores vazios ou nulos

```python
tesla_revenue.dropna(inplace=True)
tesla_revenue = tesla_revenue[tesla_revenue["Revenue"] != ""]
tesla_revenue["Revenue"] = pd.to_numeric(tesla_revenue["Revenue"], errors="coerce")
tesla_revenue.dropna(inplace=True)
tesla_revenue["Date"] = pd.to_datetime(tesla_revenue["Date"])
```

### 6.3 Conferindo o resultado

```python
tesla_revenue.tail()
tesla_revenue.info()
```

> ✅ **Tarefa 3:** Mostre as 5 últimas linhas de `tesla_revenue` com `tail()`.
> ✅ **Tarefa 4:** Repita o scraping e a limpeza para a GameStop e mostre `gme_revenue.tail()`.

### 6.4 Função reutilizável de limpeza

```python
def limpar_receita(df):
    df = df.copy()
    df.columns = ["Date", "Revenue"]
    df["Revenue"] = df["Revenue"].astype(str).str.replace(r"[\$,]", "", regex=True)
    df["Revenue"] = pd.to_numeric(df["Revenue"], errors="coerce")
    df["Date"] = pd.to_datetime(df["Date"], errors="coerce")
    return df.dropna().sort_values("Date").reset_index(drop=True)
```

---

## 7. Módulo 4 – Visualização com Plotly

### 7.1 Função `make_graph` (padrão do projeto)
Cria um gráfico com **dois painéis** que compartilham o eixo X: preço (em cima) e receita (embaixo).

```python
def make_graph(stock_data, revenue_data, stock, data_limite_preco=None, data_limite_receita=None):
    stock_data = stock_data.copy()
    revenue_data = revenue_data.copy()
    stock_data["Date"] = pd.to_datetime(stock_data["Date"]).dt.tz_localize(None)
    revenue_data["Date"] = pd.to_datetime(revenue_data["Date"])

    if data_limite_preco:
        stock_data = stock_data[stock_data["Date"] <= data_limite_preco]
    if data_limite_receita:
        revenue_data = revenue_data[revenue_data["Date"] <= data_limite_receita]

    fig = make_subplots(rows=2, cols=1, shared_xaxes=True,
                        subplot_titles=("Historical Share Price", "Historical Revenue"),
                        vertical_spacing=0.3)

    fig.add_trace(go.Scatter(x=stock_data["Date"], y=stock_data["Close"].astype(float),
                             name="Share Price"), row=1, col=1)
    fig.add_trace(go.Scatter(x=revenue_data["Date"], y=revenue_data["Revenue"].astype(float),
                             name="Revenue"), row=2, col=1)

    fig.update_xaxes(title_text="Date", row=1, col=1)
    fig.update_xaxes(title_text="Date", row=2, col=1)
    fig.update_yaxes(title_text="Price ($US)", row=1, col=1)
    fig.update_yaxes(title_text="Revenue ($US Millions)", row=2, col=1)
    fig.update_layout(showlegend=False, height=900, title=stock,
                      xaxis_rangeslider_visible=True)
    fig.show()
    return fig
```

### 7.2 Gráficos exigidos

```python
# Tarefa 5 – Tesla (dados até junho/2021, como no enunciado original)
make_graph(tesla_data, tesla_revenue, "Tesla", "2021-06-14", "2021-04-30")

# Tarefa 6 – GameStop
make_graph(gme_data, gme_revenue, "GameStop", "2021-06-14", "2021-04-30")
```

> 💡 Se o gráfico não aparecer no notebook: `import plotly.io as pio; pio.renderers.default = "notebook"` (Jupyter) ou `"colab"` (Google Colab).

### 7.3 Outros gráficos úteis para o dashboard

**Candlestick (velas):**
```python
d = amzn_data.tail(180)
fig = go.Figure(go.Candlestick(x=d["Date"], open=d["Open"], high=d["High"],
                               low=d["Low"], close=d["Close"]))
fig.update_layout(title="Amazon – últimos 180 pregões", xaxis_rangeslider_visible=False)
fig.show()
```

**Média móvel (tendência):**
```python
d = amd_data.copy()
d["MM50"] = d["Close"].rolling(50).mean()
d["MM200"] = d["Close"].rolling(200).mean()
fig = go.Figure()
for col in ["Close", "MM50", "MM200"]:
    fig.add_trace(go.Scatter(x=d["Date"], y=d[col], name=col))
fig.update_layout(title="AMD – Preço e médias móveis")
fig.show()
```

**Comparação normalizada (base 100) entre as 4 ações:**
```python
import plotly.express as px

precos = {"TSLA": tesla_data, "AMZN": amzn_data, "AMD": amd_data, "GME": gme_data}
frames = []
for tk, df in precos.items():
    d = df[["Date", "Close"]].copy()
    d["Date"] = pd.to_datetime(d["Date"]).dt.tz_localize(None)
    d = d[d["Date"] >= "2019-01-01"]
    d["Base100"] = d["Close"] / d["Close"].iloc[0] * 100
    d["Ticker"] = tk
    frames.append(d)
comp = pd.concat(frames)
px.line(comp, x="Date", y="Base100", color="Ticker",
        title="Desempenho relativo desde 2019 (base 100)").show()
```

---

## 8. Módulo 5 – Pipeline completo para as 4 empresas

```python
EMPRESAS = {
    "TSLA": {"nome": "Tesla",    "url": url_macro["TSLA"]},
    "AMZN": {"nome": "Amazon",   "url": url_macro["AMZN"]},
    "AMD":  {"nome": "AMD",      "url": url_macro["AMD"]},
    "GME":  {"nome": "GameStop", "url": url_macro["GME"]},
}
# Para reproduzir exatamente o lab da IBM, use as URLs estáticas:
EMPRESAS["TSLA"]["url"] = url_tesla_rev
EMPRESAS["GME"]["url"] = url_gme_rev


def obter_precos(ticker):
    df = yf.Ticker(ticker).history(period="max").reset_index()
    df["Date"] = pd.to_datetime(df["Date"]).dt.tz_localize(None)
    return df


def obter_receita(url):
    html = requests.get(url, headers=headers, timeout=30).text
    tabelas = pd.read_html(StringIO(html))
    tabela = next((t for t in tabelas if "Quarterly" in str(t.columns[0])), tabelas[1])
    return limpar_receita(tabela)


dados = {}
for tk, cfg in EMPRESAS.items():
    try:
        precos = obter_precos(tk)
        receita = obter_receita(cfg["url"])
        dados[tk] = {"precos": precos, "receita": receita}
        print(f"{tk}: {len(precos)} pregões | {len(receita)} trimestres")
    except Exception as e:
        print(f"{tk}: falha -> {type(e).__name__}: {e}")

for tk, d in dados.items():
    make_graph(d["precos"], d["receita"], EMPRESAS[tk]["nome"])
```

### 8.1 Salvando os dados tratados (boa prática)

```python
for tk, d in dados.items():
    d["precos"].to_csv(f"{tk}_precos.csv", index=False)
    d["receita"].to_csv(f"{tk}_receita.csv", index=False)
```

---

## 9. Módulo 6 – Análise e extração de insights

### 9.1 Métricas recomendadas

```python
def resumo(tk, precos, receita):
    p = precos.set_index("Date")["Close"]
    ret = p.pct_change().dropna()
    return {
        "Ticker": tk,
        "Preço atual": round(p.iloc[-1], 2),
        "Máxima histórica": round(p.max(), 2),
        "Retorno 1 ano (%)": round((p.iloc[-1] / p.iloc[-252] - 1) * 100, 1),
        "Volatilidade anual (%)": round(ret.tail(252).std() * (252 ** 0.5) * 100, 1),
        "Receita último tri (US$ mi)": receita["Revenue"].iloc[-1],
        "Cresc. receita YoY (%)": round((receita["Revenue"].iloc[-1] / receita["Revenue"].iloc[-5] - 1) * 100, 1),
    }

tabela_resumo = pd.DataFrame([resumo(tk, d["precos"], d["receita"]) for tk, d in dados.items()])
tabela_resumo
```

### 9.2 Correlação preço x receita (por trimestre)

```python
def correlacao_preco_receita(precos, receita):
    p = precos.set_index("Date")["Close"].resample("QE").last().to_frame("Close")
    r = receita.set_index("Date")["Revenue"].resample("QE").last()
    return p.join(r, how="inner").corr().iloc[0, 1]

for tk, d in dados.items():
    print(tk, round(correlacao_preco_receita(d["precos"], d["receita"]), 2))
```
> Em versões antigas do pandas, use `"Q"` no lugar de `"QE"`.

### 9.3 Perguntas-guia para os insights
1. **Crescimento:** a receita cresce de forma consistente? Há sazonalidade (ex.: 4º trimestre forte na Amazon e GameStop por causa das festas)?
2. **Descolamento:** o preço subiu muito mais que a receita? (ex.: **GameStop em jan/2021** – *short squeeze* impulsionado pelo Reddit, sem crescimento de receita correspondente).
3. **Eventos:** splits (Tesla 2020 e 2022; Amazon 2022), crise da COVID-19 (mar/2020), ciclo de chips (AMD).
4. **Risco:** qual ação tem maior volatilidade? Qual teve maior queda desde o topo (*drawdown*)?
5. **Tendência:** o preço está acima ou abaixo da média móvel de 200 dias?

### 9.4 Modelo para redigir um insight
> *"Entre 2019 e 2021, a receita trimestral da Tesla cresceu X%, enquanto o preço da ação subiu Y%. A correlação trimestral entre preço e receita foi de Z, sugerindo que o mercado precificou expectativas de crescimento futuro acima do desempenho corrente."*

---

## 10. Módulo 7 – Dashboard interativo (Plotly Dash) – opcional

Salve como `app.py` e execute `python app.py`; acesse `http://127.0.0.1:8050`.

```python
import pandas as pd
import plotly.graph_objects as go
from plotly.subplots import make_subplots
from dash import Dash, dcc, html, Input, Output

NOMES = {"TSLA": "Tesla", "AMZN": "Amazon", "AMD": "AMD", "GME": "GameStop"}
PRECOS = {tk: pd.read_csv(f"{tk}_precos.csv", parse_dates=["Date"]) for tk in NOMES}
RECEITAS = {tk: pd.read_csv(f"{tk}_receita.csv", parse_dates=["Date"]) for tk in NOMES}

app = Dash(__name__)
app.title = "Dashboard de Ações"

app.layout = html.Div([
    html.H1("📈 Dashboard – Preço x Receita", style={"textAlign": "center"}),
    html.Div([
        dcc.Dropdown(id="ticker", options=[{"label": v, "value": k} for k, v in NOMES.items()],
                     value="TSLA", clearable=False, style={"width": "300px"}),
        dcc.DatePickerRange(id="periodo", start_date="2015-01-01",
                            end_date=pd.Timestamp.today().date()),
    ], style={"display": "flex", "gap": "20px", "justifyContent": "center"}),
    html.Div(id="kpis", style={"display": "flex", "justifyContent": "space-around", "margin": "20px"}),
    dcc.Graph(id="grafico"),
])


def card(titulo, valor):
    return html.Div([html.P(titulo), html.H3(valor)],
                    style={"border": "1px solid #ddd", "borderRadius": "10px",
                           "padding": "10px 25px", "textAlign": "center"})


@app.callback(Output("grafico", "figure"), Output("kpis", "children"),
              Input("ticker", "value"), Input("periodo", "start_date"), Input("periodo", "end_date"))
def atualizar(tk, ini, fim):
    p = PRECOS[tk][(PRECOS[tk]["Date"] >= ini) & (PRECOS[tk]["Date"] <= fim)]
    r = RECEITAS[tk][(RECEITAS[tk]["Date"] >= ini) & (RECEITAS[tk]["Date"] <= fim)]

    fig = make_subplots(rows=2, cols=1, shared_xaxes=True, vertical_spacing=0.12,
                        subplot_titles=("Preço de fechamento (US$)", "Receita trimestral (US$ mi)"))
    fig.add_trace(go.Scatter(x=p["Date"], y=p["Close"], name="Preço"), row=1, col=1)
    fig.add_trace(go.Bar(x=r["Date"], y=r["Revenue"], name="Receita"), row=2, col=1)
    fig.update_layout(height=750, title=NOMES[tk], showlegend=False)

    var = (p["Close"].iloc[-1] / p["Close"].iloc[0] - 1) * 100 if len(p) > 1 else 0
    kpis = [
        card("Último preço", f"US$ {p['Close'].iloc[-1]:,.2f}" if len(p) else "-"),
        card("Variação no período", f"{var:,.1f}%"),
        card("Máxima no período", f"US$ {p['Close'].max():,.2f}" if len(p) else "-"),
        card("Última receita", f"US$ {r['Revenue'].iloc[-1]:,.0f} mi" if len(r) else "-"),
    ]
    return fig, kpis


if __name__ == "__main__":
    app.run(debug=True)
```

### 10.1 Boas práticas de dashboard
- **Hierarquia:** KPIs no topo → gráfico principal → detalhes.
- **Poucas cores**, títulos claros e unidades nos eixos (US$, milhões).
- **Interatividade útil:** filtro de empresa, período e *range slider*.
- **Contexto:** anote eventos relevantes (`fig.add_vline` / `fig.add_annotation`).
- **Uma pergunta por visual:** cada gráfico deve responder a uma questão de negócio.

---

## 11. Usando prompts (IA generativa) no projeto

A IA pode acelerar o trabalho, mas **sempre valide o código e os números**.

| Etapa | Exemplo de prompt |
|---|---|
| Extração | *"Escreva um código Python com yfinance que baixe o histórico máximo de preços de TSLA, AMZN, AMD e GME e retorne um dicionário de DataFrames com a coluna Date sem fuso horário."* |
| Scraping | *"Tenho este trecho HTML [cole o trecho]. Escreva código BeautifulSoup que extraia a tabela 'Quarterly Revenue' em um DataFrame com colunas Date e Revenue."* |
| Limpeza | *"Minha coluna Revenue tem valores como '$1,234' e strings vazias. Como convertê-la para float com pandas 2.x e remover linhas inválidas?"* |
| Visualização | *"Crie com Plotly um gráfico de 2 subplots com eixo X compartilhado: preço de fechamento em cima e receita trimestral em barras embaixo."* |
| Insights | *"Com base nesta tabela de resumo [cole], liste 5 insights para um investidor, destacando crescimento de receita, volatilidade e descolamento entre preço e fundamentos."* |
| Depuração | *"Recebi o erro `AttributeError: 'DataFrame' object has no attribute 'append'`. Como corrigir?"* |

**Dicas para bons prompts:** informe o contexto (bibliotecas e versões), mostre um exemplo dos dados, descreva o formato de saída esperado e peça explicações do código.

---

## 12. Checklist de entrega

| # | Tarefa | Evidência esperada | ✔ |
|---|---|---|---|
| 1 | Extrair dados da Tesla com `yfinance` | `tesla_data.head()` | ☐ |
| 2 | Web scraping da receita da Tesla | `tesla_revenue.tail()` | ☐ |
| 3 | Extrair dados da GameStop com `yfinance` | `gme_data.head()` | ☐ |
| 4 | Web scraping da receita da GameStop | `gme_revenue.tail()` | ☐ |
| 5 | Dashboard/gráfico da Tesla | `make_graph(... "Tesla")` | ☐ |
| 6 | Dashboard/gráfico da GameStop | `make_graph(... "GameStop")` | ☐ |
| 7 | Dados de Amazon e AMD (preço e receita) | `head()` / `tail()` | ☐ |
| 8 | Tabela-resumo e insights escritos | Células Markdown | ☐ |
| 9 | Notebook salvo com todas as saídas | `.ipynb` / link GitHub | ☐ |

**Publicando no GitHub:** crie um repositório → *Add file → Upload files* → envie o `.ipynb` → copie o link. Garanta que as saídas (tabelas e gráficos) estejam salvas no notebook antes do upload. Como gráficos Plotly às vezes não renderizam no GitHub, faça também um print (captura de tela) de cada gráfico.

---

## 13. Solução de problemas (troubleshooting)

| Problema | Causa provável | Solução |
|---|---|---|
| `yfinance` retorna DataFrame vazio | Versão antiga / limite de requisições | `pip install -U yfinance`; aguarde e tente novamente |
| `YFRateLimitError` / 429 | Muitas requisições | Espere alguns minutos; faça cache em CSV |
| `403 Forbidden` no scraping | Site bloqueia robôs | Use `headers` com `User-Agent`; use as URLs estáticas do curso |
| `ValueError: No tables found` | Tabela carregada via JavaScript ou URL errada | Confira o HTML baixado; use outra fonte |
| `'DataFrame' object has no attribute 'append'` | pandas 2.x | Troque por `pd.concat` ou lista de dicionários |
| `TypeError: Cannot compare tz-naive and tz-aware` | Datas com fuso horário | `.dt.tz_localize(None)` |
| `could not convert string to float: ''` | Células vazias | `pd.to_numeric(..., errors="coerce")` + `dropna()` |
| Gráfico Plotly não aparece | Renderizador | `pio.renderers.default = "notebook"` / `"colab"`; `pip install nbformat` |
| `FutureWarning` de `read_html` | Passar string literal | Envolva o HTML em `StringIO(html)` |

---

## 14. Exercícios de fixação

1. Qual foi o maior preço de fechamento da GameStop em janeiro de 2021? Em que dia?
2. Calcule o crescimento percentual da receita anual da Amazon nos últimos 5 anos.
3. Qual das quatro ações teve a maior volatilidade anualizada no último ano?
4. Crie um gráfico com a média móvel de 50 e 200 dias da Tesla e identifique os cruzamentos (*golden cross* / *death cross*).
5. Adicione ao dashboard um terceiro painel com o **volume** negociado.
6. Calcule o *drawdown* máximo de cada ação desde 2020.
7. Escreva um parágrafo comparando AMD e Amazon quanto à relação preço x receita.

<details>
<summary>💡 Dicas de solução</summary>

```python
# 1
g = gme_data[(gme_data["Date"] >= "2021-01-01") & (gme_data["Date"] <= "2021-01-31")]
g.loc[g["Close"].idxmax(), ["Date", "Close"]]

# 6
p = tesla_data.set_index("Date")["Close"]["2020":]
drawdown = (p / p.cummax() - 1).min() * 100
```
</details>

---

## 15. Glossário

| Termo | Definição |
|---|---|
| **Ticker** | Código de negociação da ação (ex.: TSLA) |
| **OHLCV** | Open, High, Low, Close, Volume |
| **Receita (Revenue)** | Faturamento bruto da empresa no período |
| **YoY** | *Year over Year* – comparação com o mesmo período do ano anterior |
| **Volatilidade** | Desvio-padrão dos retornos; medida de risco |
| **Drawdown** | Queda percentual a partir do pico anterior |
| **Média móvel** | Média dos preços em uma janela de dias; suaviza tendências |
| **Split** | Desdobramento de ações (ex.: 1 ação vira 3) |
| **Short squeeze** | Alta abrupta causada por vendidos sendo forçados a recomprar |
| **Web scraping** | Extração automatizada de dados de páginas web |
| **Parser** | Programa que interpreta a estrutura de um documento (HTML) |
| **Dashboard** | Painel visual que consolida indicadores-chave |

---

### 📚 Referências
- Documentação `yfinance`: https://github.com/ranaroussi/yfinance
- Beautiful Soup: https://www.crummy.com/software/BeautifulSoup/bs4/doc/
- pandas: https://pandas.pydata.org/docs/
- Plotly Python: https://plotly.com/python/
- Dash: https://dash.plotly.com/

> **Aviso:** este material tem fins educacionais e não constitui recomendação de investimento.

---
*Bom projeto! 🚀*
