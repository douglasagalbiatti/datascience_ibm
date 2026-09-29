# 📘 Apostila Completa — Projeto Final: Extração e Visualização de Dados de Ações com Python

> **Tesla (TSLA) • Amazon (AMZN) • AMD (AMD) • GameStop (GME)**
> Material de estudo e guia de execução passo a passo para o projeto de *Data Science / Data Analysis* (Python Project for Data Science — IBM / Skills Network).

---

## 📑 Sumário

0. [Como usar esta apostila](#0-como-usar-esta-apostila)
1. [Contexto do projeto e o que será avaliado](#1-contexto-do-projeto-e-o-que-será-avaliado)
2. [Preparando o ambiente](#2-preparando-o-ambiente)
3. [Fundamentos teóricos que você PRECISA dominar](#3-fundamentos-teóricos-que-você-precisa-dominar)
4. [Lab 1 — Extraindo dados de ações com a biblioteca `yfinance`](#4-lab-1--extraindo-dados-de-ações-com-a-biblioteca-yfinance)
5. [Lab 2 — Extraindo dados de ações com Web Scraping](#5-lab-2--extraindo-dados-de-ações-com-web-scraping)
6. [Preparação para os Quizzes](#6-preparação-para-os-quizzes)
7. [Trabalho Final — Passo a passo (Questões 1 a 6)](#7-trabalho-final--passo-a-passo-questões-1-a-6)
8. [Indo além: dashboard das 4 empresas (Tesla, Amazon, AMD, GameStop)](#8-indo-além-dashboard-das-4-empresas)
9. [Analisando os dados e extraindo insights](#9-analisando-os-dados-e-extraindo-insights)
10. [Usando prompts (IA) de forma eficaz no projeto](#10-usando-prompts-ia-de-forma-eficaz-no-projeto)
11. [Solução de problemas (Troubleshooting)](#11-solução-de-problemas-troubleshooting)
12. [Checklist de entrega e screenshots](#12-checklist-de-entrega-e-screenshots)
13. [Código completo consolidado](#13-código-completo-consolidado)
14. [Glossário](#14-glossário)
15. [Exercícios extras para fixação](#15-exercícios-extras-para-fixação)

---

## 0. Como usar esta apostila

Esta apostila foi escrita para ser lida **na ordem**. Cada capítulo prepara o seguinte:

| Etapa | Capítulo | Tempo estimado |
|---|---|---|
| Entender o problema | 1 | 15 min |
| Configurar o ambiente | 2 | 20 min |
| Estudar a teoria | 3 | 1h30 |
| Fazer o Lab 1 | 4 | 45 min |
| Fazer o Lab 2 | 5 | 1h |
| Revisar para os quizzes | 6 | 30 min |
| Fazer o trabalho final | 7 | 2h |
| (Opcional) Ir além | 8–9 | 1h+ |
| Entregar | 12 | 30 min |

**Convenções usadas:**

- 💡 **Dica** — atalho ou boa prática.
- ⚠️ **Atenção** — erro comum ou ponto que costuma derrubar a nota.
- 🧠 **Conceito** — teoria que vale a pena memorizar.
- ✅ **Checkpoint** — o que você deve ver na tela antes de seguir.

> 💡 **Recomendação de estudo:** digite o código em vez de copiar e colar. A memória muscular ajuda muito a fixar sintaxe de `pandas` e `BeautifulSoup`.

---

## 1. Contexto do projeto e o que será avaliado

### 1.1 O cenário

Você é um(a) **Cientista/Analista de Dados** em uma startup de investimentos. A empresa ajuda clientes a tomarem decisões informadas sobre compra de ações. Sua missão:

1. **Extrair** dados financeiros de múltiplas fontes:
   - **Preço histórico das ações** → via biblioteca Python (`yfinance`).
   - **Receita trimestral (quarterly revenue)** → via **web scraping** (`requests` + `BeautifulSoup` / `pandas.read_html`).
2. **Tratar/limpar** esses dados (tipos, vírgulas, símbolos `$`, valores vazios).
3. **Visualizar** em um **dashboard** com **Plotly** (preço × receita ao longo do tempo).
4. **Interpretar** padrões e tendências.

### 1.2 As empresas

| Empresa | Ticker | Onde aparece no curso |
|---|---|---|
| Tesla | `TSLA` | Trabalho final (Q1, Q2, Q5) |
| Amazon | `AMZN` | Lab 2 (web scraping) |
| AMD | `AMD` | Lab 1 (yfinance) — perguntas do quiz |
| GameStop | `GME` | Trabalho final (Q3, Q4, Q6) |

> 🧠 **Ticker** é o código de negociação de uma ação na bolsa. É o que você passa para o `yfinance`.

### 1.3 Critérios de avaliação

1. ✅ **Dois labs práticos** sobre extração de dados de ações:
   - Lab 1: *Extracting Stock Data Using a Python Library* (yfinance).
   - Lab 2: *Extracting Stock Data Using Web Scraping* (requests + BeautifulSoup).
2. ✅ **Dois quizzes** sobre os labs.
3. ✅ **Trabalho final** avaliado por IA (*AI-graded assignment*), com **screenshots** e resultados.

### 1.4 Estrutura típica do trabalho final (rubrica)

A rubrica tradicional deste projeto tem **6 questões** (total ≈ 10 pontos):

| Questão | Tarefa | O que mostrar | Pontos (típico) |
|---|---|---|---|
| Q1 | Extrair dados de ações da **Tesla** com `yfinance` | `tesla_data.head()` | 2 |
| Q2 | Extrair **receita da Tesla** via web scraping | `tesla_revenue.tail()` | 1 |
| Q3 | Extrair dados de ações da **GameStop** com `yfinance` | `gme_data.head()` | 2 |
| Q4 | Extrair **receita da GameStop** via web scraping | `gme_revenue.tail()` | 1 |
| Q5 | **Dashboard Tesla** (preço × receita) | Gráfico `make_graph` | 2 |
| Q6 | **Dashboard GameStop** (preço × receita) | Gráfico `make_graph` | 2 |

> ⚠️ **Atenção:** a avaliação é por IA a partir das suas **capturas de tela e respostas**. Se o screenshot não mostrar claramente o **código** E a **saída**, você perde ponto. Veja o capítulo 12.

---

## 2. Preparando o ambiente

### 2.1 Onde rodar

Você tem três opções:

| Opção | Prós | Contras |
|---|---|---|
| **Skills Network Labs / JupyterLab do curso** | Já vem configurado, é o esperado pelo curso | Sessões expiram; salve com frequência |
| **Jupyter local (Anaconda)** | Controle total | Precisa instalar pacotes |
| **Google Colab** | Grátis, sem instalação | Alguns pacotes precisam `pip install` a cada sessão |

> 💡 Se o `yfinance` der erro de *rate limit* no ambiente do curso, tente no Colab ou local (ver cap. 11).

### 2.2 Instalação das bibliotecas

Na primeira célula do notebook:

```python
!pip install yfinance --upgrade
!pip install bs4
!pip install nbformat
!pip install plotly
!pip install lxml html5lib
```

> 🧠 `nbformat` é necessário para o Plotly renderizar figuras dentro do Jupyter.
> 🧠 `lxml` / `html5lib` são *parsers* usados por `pd.read_html`.

### 2.3 Imports padrão do projeto

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
```

✅ **Checkpoint:** a célula roda sem `ModuleNotFoundError`.

### 2.4 Verificando versões (bom para depuração)

```python
import yfinance, plotly, bs4
print("pandas  :", pd.__version__)
print("yfinance:", yfinance.__version__)
print("plotly  :", plotly.__version__)
print("bs4     :", bs4.__version__)
```

---

## 3. Fundamentos teóricos que você PRECISA dominar

### 3.1 Pandas em 10 minutos

🧠 **DataFrame** = tabela (linhas × colunas). **Series** = uma coluna.

```python
df = pd.DataFrame({"Date": ["2021-01-01", "2021-04-01"], "Revenue": ["$10,389", "$11,958"]})

df.head()           # primeiras 5 linhas
df.tail()           # últimas 5 linhas
df.shape            # (linhas, colunas)
df.columns          # nomes das colunas
df.dtypes           # tipo de cada coluna
df.info()           # resumo geral
df.describe()       # estatísticas descritivas
df["Revenue"]       # uma coluna (Series)
df[df["Date"] > "2021-02-01"]   # filtro booleano
df.reset_index(inplace=True)    # transforma o índice em coluna
df.dropna(inplace=True)         # remove linhas com NaN
```

Métodos importantes para limpeza:

| Método | Uso |
|---|---|
| `.str.replace(padrão, novo, regex=True)` | Remover `$` e `,` |
| `.astype(float)` | Converter texto → número |
| `pd.to_datetime(col)` | Converter texto → data |
| `pd.to_numeric(col, errors="coerce")` | Converter com segurança (erros viram NaN) |
| `pd.concat([df1, df2], ignore_index=True)` | Empilhar DataFrames (substitui o antigo `append`) |

> ⚠️ **`DataFrame.append` foi removido no pandas 2.0.** Muitos notebooks antigos usam `df = df.append(...)`. Use `pd.concat`.

### 3.2 A biblioteca `yfinance`

🧠 O `yfinance` acessa dados públicos do Yahoo Finance. O objeto central é o **`Ticker`**.

```python
tsla = yf.Ticker("TSLA")

tsla.info              # dicionário com informações da empresa (setor, país, etc.)
tsla.history(period="max")   # DataFrame de preços históricos
tsla.dividends         # Series de dividendos
tsla.splits            # Series de desdobramentos (splits)
tsla.quarterly_income_stmt   # DRE trimestral (limitada aos últimos trimestres)
```

Parâmetros do `history()`:

| Parâmetro | Valores | Exemplo |
|---|---|---|
| `period` | `1d, 5d, 1mo, 3mo, 6mo, 1y, 2y, 5y, 10y, ytd, max` | `period="max"` |
| `interval` | `1m, 5m, 1h, 1d, 1wk, 1mo` | `interval="1d"` |
| `start`, `end` | datas `"AAAA-MM-DD"` | `start="2020-01-01"` |

Colunas retornadas pelo `history()`:

| Coluna | Significado |
|---|---|
| `Open` | Preço de abertura do dia |
| `High` | Máxima do dia |
| `Low` | Mínima do dia |
| `Close` | Preço de fechamento (ajustado por splits/dividendos por padrão) |
| `Volume` | Quantidade de ações negociadas |
| `Dividends` | Dividendos pagos naquele dia |
| `Stock Splits` | Fator de desdobramento naquele dia |

> 🧠 O **índice** do DataFrame retornado é a data (`Date`). Por isso usamos `reset_index(inplace=True)` para transformá-lo em coluna — a função `make_graph` espera uma coluna `Date`.

> ⚠️ Nas versões recentes, `Date` vem com **fuso horário** (ex.: `2010-06-29 00:00:00-04:00`). Isso pode quebrar comparações com strings. Solução no cap. 11.

### 3.3 HTML básico (para web scraping)

🧠 Página web = árvore de **tags**. Tabelas têm esta estrutura:

```html
<table>
  <thead>
    <tr><th>Date</th><th>Revenue</th></tr>
  </thead>
  <tbody>
    <tr><td>2021-03-31</td><td>$10,389</td></tr>
    <tr><td>2020-12-31</td><td>$10,744</td></tr>
  </tbody>
</table>
```

| Tag | Significado |
|---|---|
| `<table>` | Tabela |
| `<thead>` | Cabeçalho da tabela |
| `<tbody>` | Corpo da tabela |
| `<tr>` | *Table row* — linha |
| `<th>` | *Table header* — célula de cabeçalho |
| `<td>` | *Table data* — célula de dado |

> 💡 No navegador, clique com o botão direito → **Inspecionar** para ver o HTML e descobrir qual tabela contém os dados.

### 3.4 `requests` — baixando a página

```python
url = "https://exemplo.com/pagina.html"
headers = {"User-Agent": "Mozilla/5.0"}
response = requests.get(url, headers=headers)
response.status_code   # 200 = OK
html_data = response.text   # HTML como string
```

| Status | Significado |
|---|---|
| 200 | OK |
| 403 | Proibido (site bloqueou — tente `User-Agent`) |
| 404 | Página não encontrada |
| 429 | Muitas requisições (*rate limit*) |

### 3.5 `BeautifulSoup` — navegando no HTML

```python
soup = BeautifulSoup(html_data, "html.parser")   # ou "html5lib"

soup.title               # <title>...</title>
soup.title.string        # texto do título
soup.find("table")       # PRIMEIRA tabela
soup.find_all("table")   # LISTA de todas as tabelas
soup.find("tbody").find_all("tr")   # linhas do corpo
tag.text / tag.get_text(strip=True) # texto de uma tag
tag["href"]              # atributo
```

Padrão clássico de extração de tabela:

```python
rows = []
for row in soup.find("tbody").find_all("tr"):
    cols = row.find_all("td")
    rows.append({"Date": cols[0].text, "Revenue": cols[1].text})
df = pd.DataFrame(rows)
```

### 3.6 `pd.read_html` — o atalho

🧠 `pd.read_html` lê **todas** as tabelas de um HTML e devolve uma **lista de DataFrames**.

```python
tables = pd.read_html(StringIO(html_data))
len(tables)         # quantas tabelas existem
tables[0].head()    # primeira tabela
tables[1].head()    # segunda tabela
```

> ⚠️ Nas versões novas do pandas, passar a string HTML diretamente gera `FutureWarning`. Envolva com `StringIO(...)`.

### 3.7 Expressões regulares mínimas (limpeza de moeda)

```python
df["Revenue"] = df["Revenue"].str.replace(r",|\$", "", regex=True)
```

- `,` → vírgula
- `|` → "OU"
- `\$` → o caractere `$` (escapado, pois `$` sozinho significa "fim da string" em regex)

Resultado: `"$10,389"` → `"10389"`.

### 3.8 Plotly — visualização interativa

🧠 Dois "sabores":
- **`plotly.express` (px)** — alto nível, rápido.
- **`plotly.graph_objects` (go)** — baixo nível, controle total (usado no projeto).

```python
import plotly.graph_objects as go
from plotly.subplots import make_subplots

fig = make_subplots(rows=2, cols=1, shared_xaxes=True)
fig.add_trace(go.Scatter(x=[1,2,3], y=[3,1,2], name="A"), row=1, col=1)
fig.add_trace(go.Scatter(x=[1,2,3], y=[2,4,1], name="B"), row=2, col=1)
fig.update_layout(title="Exemplo", height=600)
fig.show()
```

| Elemento | Função |
|---|---|
| `make_subplots` | Grade de gráficos |
| `shared_xaxes=True` | Eixo X compartilhado (zoom sincronizado) |
| `go.Scatter` | Linha/pontos |
| `go.Candlestick` | Gráfico de velas (OHLC) |
| `go.Bar` | Barras |
| `xaxis_rangeslider_visible=True` | Barra deslizante de intervalo de datas |

---

## 4. Lab 1 — Extraindo dados de ações com a biblioteca `yfinance`

**Objetivo:** usar `yfinance` para obter informações da empresa, preço histórico e dividendos. O lab usa **Apple (AAPL)** como exemplo guiado e **AMD** como exercício.

### 4.1 Passo 1 — Criar o objeto Ticker

```python
import yfinance as yf
import pandas as pd

apple = yf.Ticker("AAPL")
```

### 4.2 Passo 2 — Informações da empresa (`info`)

No lab, como o `info` do Yahoo pode ser instável, o curso fornece um JSON pronto:

```python
!wget https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/data/apple.json
```

```python
import json
with open("apple.json") as f:
    apple_info = json.load(f)

apple_info["country"]
apple_info["sector"]
```

> 💡 Sem o JSON, `apple.info["country"]` faz o mesmo direto da API.

### 4.3 Passo 3 — Preço histórico

```python
apple_share_price_data = apple.history(period="max")
apple_share_price_data.head()
```

Transformar o índice em coluna:

```python
apple_share_price_data.reset_index(inplace=True)
```

Plotar o preço de abertura:

```python
apple_share_price_data.plot(x="Date", y="Open")
```

### 4.4 Passo 4 — Dividendos

```python
apple.dividends
apple.dividends.plot()
```

### 4.5 Exercício — AMD

```python
!wget https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/data/amd.json
```

```python
amd = yf.Ticker("AMD")

with open("amd.json") as f:
    amd_info = json.load(f)
```

**Pergunta 1:** Em qual país está a sede da AMD?

```python
amd_info["country"]
```

**Pergunta 2:** A qual setor a AMD pertence?

```python
amd_info["sector"]
```

**Pergunta 3:** Qual o **volume** negociado no **primeiro dia** (primeira linha)?

```python
amd_history = amd.history(period="max")
amd_history.head(1)["Volume"]
# ou
amd_history.iloc[0]["Volume"]
```

> 🧠 Anote as respostas que você obtiver — elas caem no **quiz**. (Referência comum: país = *United States*, setor = *Technology*, volume do 1º dia ≈ *219600*. Confirme sempre com a sua própria saída.)

✅ **Checkpoint:** você sabe criar um `Ticker`, ler `info`, obter `history(period="max")`, usar `reset_index` e plotar.

---

## 5. Lab 2 — Extraindo dados de ações com Web Scraping

**Objetivo:** baixar uma página HTML com dados históricos de preço, parsear com BeautifulSoup e montar um DataFrame. O lab usa **Netflix** como exemplo guiado e **Amazon** como exercício.

### 5.1 Passo 1 — Baixar a página (Netflix)

```python
import requests
import pandas as pd
from bs4 import BeautifulSoup
from io import StringIO

url = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/netflix_data_webpage.html"
data = requests.get(url).text
```

### 5.2 Passo 2 — Parsear

```python
soup = BeautifulSoup(data, "html.parser")
```

### 5.3 Passo 3 — Extrair a tabela manualmente

```python
netflix_data = pd.DataFrame(columns=["Date", "Open", "High", "Low", "Close", "Volume"])

for row in soup.find("tbody").find_all("tr"):
    col = row.find_all("td")
    date = col[0].text
    Open = col[1].text
    high = col[2].text
    low = col[3].text
    close = col[4].text
    adj_close = col[5].text
    volume = col[6].text

    netflix_data = pd.concat([netflix_data, pd.DataFrame({
        "Date": [date], "Open": [Open], "High": [high],
        "Low": [low], "Close": [close], "Adj Close": [adj_close],
        "Volume": [volume]})], ignore_index=True)

netflix_data.head()
```

> 💡 **Versão mais eficiente** (monta lista e cria o DataFrame uma vez só):
>
> ```python
> registros = []
> for row in soup.find("tbody").find_all("tr"):
>     c = [td.text for td in row.find_all("td")]
>     if len(c) == 7:   # ignora linhas de dividendos/splits
>         registros.append(c)
> netflix_data = pd.DataFrame(registros, columns=["Date","Open","High","Low","Close","Adj Close","Volume"])
> ```

### 5.4 Passo 4 — O mesmo com `read_html`

```python
read_html_pandas_data = pd.read_html(StringIO(str(soup)))
netflix_dataframe = read_html_pandas_data[0]
netflix_dataframe.head()
```

### 5.5 Exercício — Amazon

```python
url = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/amazon_data_webpage.html"
data = requests.get(url).text
soup = BeautifulSoup(data, "html.parser")
```

**Pergunta 1:** Qual é o conteúdo da tag `<title>`?

```python
soup.title
# ou apenas o texto
soup.title.string
```

**Pergunta 2:** Extraia a tabela para o DataFrame `amazon_data`:

```python
amazon_data = pd.DataFrame(columns=["Date", "Open", "High", "Low", "Close", "Volume"])

for row in soup.find("tbody").find_all("tr"):
    col = row.find_all("td")
    if len(col) < 7:
        continue
    date = col[0].text
    Open = col[1].text
    high = col[2].text
    low = col[3].text
    close = col[4].text
    adj_close = col[5].text
    volume = col[6].text
    amazon_data = pd.concat([amazon_data, pd.DataFrame({
        "Date": [date], "Open": [Open], "High": [high], "Low": [low],
        "Close": [close], "Adj Close": [adj_close], "Volume": [volume]})],
        ignore_index=True)

amazon_data.head()
```

**Pergunta 3:** Quais os nomes das colunas?

```python
amazon_data.columns
```

**Pergunta 4:** Qual o valor de `Open` na **última linha**?

```python
amazon_data.tail(1)["Open"]
# ou
amazon_data.iloc[-1]["Open"]
```

> 🧠 Anote as respostas — elas caem no **quiz 2**.

✅ **Checkpoint:** você sabe usar `requests.get().text`, criar `BeautifulSoup`, percorrer `tbody → tr → td`, usar `pd.concat` e `pd.read_html`.

---

## 6. Preparação para os Quizzes

### 6.1 Tópicos cobrados

**Quiz 1 (yfinance):**
- Como criar um objeto `Ticker`.
- Que método retorna o histórico (`history`).
- O que `period="max"` significa.
- Para que serve o atributo `info`, `dividends`.
- Leitura de valores específicos (país, setor, volume do 1º dia da AMD).

**Quiz 2 (web scraping):**
- Que biblioteca baixa o HTML (`requests`) e qual parseia (`BeautifulSoup`).
- Significado de `<tr>`, `<td>`, `<th>`, `<tbody>`.
- Diferença entre `find` e `find_all`.
- O que `pd.read_html` retorna (**lista** de DataFrames).
- Conteúdo da tag `title` da página da Amazon; colunas; `Open` da última linha.

### 6.2 Perguntas de revisão (responda sem olhar)

1. Qual o comando para obter o histórico completo de preços da Tesla?
2. Por que usamos `reset_index(inplace=True)` após `history()`?
3. O que `soup.find_all("tbody")[1]` retorna?
4. O que acontece se você tentar `astype(float)` em `"$1,234"`?
5. Qual a diferença entre `head()` e `tail()`?
6. `pd.read_html` retorna um DataFrame ou uma lista?
7. Qual tag representa uma célula de dados?
8. Em regex, por que escrevemos `\$` e não `$`?
9. Qual o equivalente moderno de `df.append()`?
10. Qual parâmetro do Plotly ativa a barra deslizante de datas?

<details>
<summary><b>Gabarito</b></summary>

1. `yf.Ticker("TSLA").history(period="max")`
2. Porque a data vem como índice; precisamos dela como coluna `Date`.
3. O **segundo** `<tbody>` da página (índice começa em 0).
4. `ValueError` — é preciso remover `$` e `,` antes.
5. `head()` mostra as primeiras linhas; `tail()` as últimas.
6. Uma **lista** de DataFrames.
7. `<td>`.
8. Porque `$` é um metacaractere (fim da linha); `\$` é o caractere literal.
9. `pd.concat([...], ignore_index=True)`.
10. `xaxis_rangeslider_visible=True`.

</details>

---

## 7. Trabalho Final — Passo a passo (Questões 1 a 6)

### 7.0 Setup do notebook final

```python
!pip install yfinance --upgrade
!pip install bs4 nbformat plotly lxml html5lib
```

```python
import yfinance as yf
import pandas as pd
import requests
from bs4 import BeautifulSoup
from io import StringIO
import plotly.graph_objects as go
from plotly.subplots import make_subplots
import plotly.io as pio

import warnings
warnings.filterwarnings("ignore", category=FutureWarning)

pio.renderers.default = "iframe"   # ajuda a renderizar no JupyterLab; no Colab use "colab"
```

### 7.0.1 A função `make_graph` (fornecida pelo curso)

Essa função recebe o DataFrame de preços, o de receita e o nome da ação; plota dois gráficos empilhados: **preço** (em cima) e **receita** (embaixo).

```python
def make_graph(stock_data, revenue_data, stock):
    fig = make_subplots(rows=2, cols=1, shared_xaxes=True,
                        subplot_titles=("Historical Share Price", "Historical Revenue"),
                        vertical_spacing=.3)
    stock_data_specific = stock_data[stock_data.Date <= '2021-06-14']
    revenue_data_specific = revenue_data[revenue_data.Date <= '2021-04-30']
    fig.add_trace(go.Scatter(x=pd.to_datetime(stock_data_specific.Date),
                             y=stock_data_specific.Close.astype("float"),
                             name="Share Price"), row=1, col=1)
    fig.add_trace(go.Scatter(x=pd.to_datetime(revenue_data_specific.Date),
                             y=revenue_data_specific.Revenue.astype("float"),
                             name="Revenue"), row=2, col=1)
    fig.update_xaxes(title_text="Date", row=1, col=1)
    fig.update_xaxes(title_text="Date", row=2, col=1)
    fig.update_yaxes(title_text="Price ($US)", row=1, col=1)
    fig.update_yaxes(title_text="Revenue ($US Millions)", row=2, col=1)
    fig.update_layout(showlegend=False,
                      height=900,
                      title=stock,
                      xaxis_rangeslider_visible=True)
    fig.show()
```

🧠 **Entenda cada linha:**

| Trecho | O que faz |
|---|---|
| `make_subplots(rows=2, cols=1, shared_xaxes=True)` | Dois gráficos, um em cima do outro, com zoom de data sincronizado |
| `stock_data[stock_data.Date <= '2021-06-14']` | Filtra preços até 14/06/2021 (padroniza a janela de análise) |
| `revenue_data[revenue_data.Date <= '2021-04-30']` | Filtra receitas até 30/04/2021 |
| `.Close.astype("float")` | Garante que o preço é numérico |
| `.Revenue.astype("float")` | **Exige** que a receita já esteja limpa (sem `$` e `,`) |
| `xaxis_rangeslider_visible=True` | Barra de rolagem de datas |

> ⚠️ **Pré-requisitos para `make_graph` funcionar:**
> 1. Os dois DataFrames têm coluna **`Date`** (não índice!).
> 2. A coluna `Revenue` está **limpa** (só dígitos).
> 3. A coluna `Date` do preço **não tem fuso horário** conflitante (ver 11.2).
> 4. A coluna `Date` da receita é comparável com string (texto `AAAA-MM-DD` ou datetime).

> 💡 **Versão robusta da função** (use se a original der erro de fuso/tipo):
>
> ```python
> def make_graph(stock_data, revenue_data, stock):
>     stock_data = stock_data.copy()
>     revenue_data = revenue_data.copy()
>     stock_data["Date"] = pd.to_datetime(stock_data["Date"], utc=True).dt.tz_localize(None)
>     revenue_data["Date"] = pd.to_datetime(revenue_data["Date"])
>     s = stock_data[stock_data.Date <= "2021-06-14"]
>     r = revenue_data[revenue_data.Date <= "2021-04-30"]
>     fig = make_subplots(rows=2, cols=1, shared_xaxes=True,
>                         subplot_titles=("Historical Share Price", "Historical Revenue"),
>                         vertical_spacing=.3)
>     fig.add_trace(go.Scatter(x=s.Date, y=s.Close.astype(float), name="Share Price"), row=1, col=1)
>     fig.add_trace(go.Scatter(x=r.Date, y=r.Revenue.astype(float), name="Revenue"), row=2, col=1)
>     fig.update_xaxes(title_text="Date", row=1, col=1)
>     fig.update_xaxes(title_text="Date", row=2, col=1)
>     fig.update_yaxes(title_text="Price ($US)", row=1, col=1)
>     fig.update_yaxes(title_text="Revenue ($US Millions)", row=2, col=1)
>     fig.update_layout(showlegend=False, height=900, title=stock,
>                       xaxis_rangeslider_visible=True)
>     fig.show()
> ```

---

### 7.1 Questão 1 — Dados de ações da Tesla com `yfinance`

**Tarefa:**
1. Criar o objeto `Ticker` da Tesla (`TSLA`).
2. Extrair o histórico com `period="max"` e salvar em `tesla_data`.
3. `reset_index(inplace=True)`.
4. Mostrar as 5 primeiras linhas com `head()`.

```python
tesla = yf.Ticker("TSLA")

tesla_data = tesla.history(period="max")

tesla_data.reset_index(inplace=True)

tesla_data.head()
```

✅ **Checkpoint — saída esperada (aproximada):**

| | Date | Open | High | Low | Close | Volume | Dividends | Stock Splits |
|---|---|---|---|---|---|---|---|---|
| 0 | 2010-06-29 | 1.27 | 1.67 | 1.17 | 1.59 | 281494500 | 0.0 | 0.0 |
| 1 | 2010-06-30 | 1.72 | 2.03 | 1.55 | 1.59 | 257806500 | 0.0 | 0.0 |
| ... | ... | ... | ... | ... | ... | ... | ... | ... |

> 🧠 Os preços de 2010 aparecem em torno de US$ 1 porque o `Close` é **ajustado pelos splits** (5:1 em 2020 e 3:1 em 2022). O IPO foi a US$ 17 nominais.

📸 **Screenshot Q1:** código + tabela `tesla_data.head()`.

---

### 7.2 Questão 2 — Receita da Tesla via Web Scraping

**Tarefa:**
1. Baixar a página de receita da Tesla com `requests` → `html_data`.
2. Parsear com `BeautifulSoup`.
3. Extrair a tabela **"Tesla Quarterly Revenue"** para `tesla_revenue` com colunas `Date` e `Revenue`.
4. Limpar a coluna `Revenue` (remover `$` e `,`).
5. Remover linhas vazias/NaN.
6. Mostrar as 5 últimas linhas com `tail()`.

**Passo 2.1 — Baixar a página**

```python
url = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/revenue.htm"
html_data = requests.get(url).text
```

> 💡 A versão original do projeto usava o site macrotrends.net, que bloqueia scraping com frequência. A IBM hospeda uma cópia estática (`revenue.htm`) — **use esta**.

**Passo 2.2 — Parsear**

```python
soup = BeautifulSoup(html_data, "html5lib")   # ou "html.parser"
```

**Passo 2.3 — Descobrir qual tabela é a trimestral**

A página tem (pelo menos) duas tabelas: **Annual Revenue** (anual) e **Quarterly Revenue** (trimestral). Vamos inspecionar:

```python
tables = soup.find_all("table")
for i, t in enumerate(tables):
    print(i, t.find("th").get_text(strip=True) if t.find("th") else "sem cabeçalho")
```

Você verá algo como:
```
0 Tesla Annual Revenue(Millions of US $)
1 Tesla Quarterly Revenue(Millions of US $)
```

➡️ Queremos o índice **1**.

**Passo 2.4a — Extração com BeautifulSoup (método "raiz", o que o curso ensina)**

```python
tesla_revenue = pd.DataFrame(columns=["Date", "Revenue"])

for table in soup.find_all("table"):
    if "Tesla Quarterly Revenue" in table.get_text():
        for row in table.find("tbody").find_all("tr"):
            col = row.find_all("td")
            if len(col) == 2:
                date = col[0].text.strip()
                revenue = col[1].text.strip()
                tesla_revenue = pd.concat(
                    [tesla_revenue, pd.DataFrame({"Date": [date], "Revenue": [revenue]})],
                    ignore_index=True)
```

**Passo 2.4b — Extração com `read_html` (alternativa curta)**

```python
tesla_revenue = pd.read_html(StringIO(html_data))[1]
tesla_revenue.columns = ["Date", "Revenue"]
```

> 💡 Use **um** dos dois métodos. O 2.4a demonstra melhor domínio de BeautifulSoup (o avaliador costuma pedir "using BeautifulSoup or read_html" — ambos são aceitos).

**Passo 2.5 — Limpar a coluna Revenue**

```python
tesla_revenue["Revenue"] = tesla_revenue["Revenue"].str.replace(r",|\$", "", regex=True)
```

**Passo 2.6 — Remover nulos e strings vazias**

```python
tesla_revenue.dropna(inplace=True)
tesla_revenue = tesla_revenue[tesla_revenue["Revenue"] != ""]
```

> 🧠 Algumas linhas antigas da tabela vêm com receita vazia. Se ficarem lá, o `astype("float")` dentro de `make_graph` quebra.

**Passo 2.7 — Exibir as últimas 5 linhas**

```python
tesla_revenue.tail()
```

✅ **Checkpoint — saída esperada (aproximada):** as linhas finais correspondem aos trimestres mais antigos (2010), p.ex.:

| | Date | Revenue |
|---|---|---|
| 48 | 2010-09-30 | 31 |
| 49 | 2010-06-30 | 28 |
| 50 | 2010-03-31 | 21 |
| 52 | 2009-09-30 | 46 |
| 53 | 2009-06-30 | 27 |

(Os números exatos e índices podem variar — o importante é: **sem `$`, sem `,`, sem vazios**.)

> 💡 **Opcional, mas recomendado:** converter tipos agora para análises posteriores.
> ```python
> tesla_revenue["Revenue"] = tesla_revenue["Revenue"].astype(float)
> tesla_revenue["Date"] = pd.to_datetime(tesla_revenue["Date"])
> ```

📸 **Screenshot Q2:** código + `tesla_revenue.tail()`.

---

### 7.3 Questão 3 — Dados de ações da GameStop com `yfinance`

Mesma lógica da Q1, trocando o ticker:

```python
gamestop = yf.Ticker("GME")

gme_data = gamestop.history(period="max")

gme_data.reset_index(inplace=True)

gme_data.head()
```

✅ **Checkpoint — saída esperada (aproximada):**

| | Date | Open | High | Low | Close | Volume | Dividends | Stock Splits |
|---|---|---|---|---|---|---|---|---|
| 0 | 2002-02-13 | 1.62 | 1.69 | 1.60 | 1.69 | 76216000 | 0.0 | 0.0 |
| 1 | 2002-02-14 | 1.71 | 1.72 | 1.67 | 1.68 | 11021600 | 0.0 | 0.0 |

> 🧠 Os preços baixos refletem o split 4:1 de julho/2022 (ajuste retroativo).

📸 **Screenshot Q3:** código + `gme_data.head()`.

---

### 7.4 Questão 4 — Receita da GameStop via Web Scraping

```python
url = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/stock.html"
html_data_2 = requests.get(url).text

soup = BeautifulSoup(html_data_2, "html.parser")
```

Inspecionar as tabelas (igual à Tesla):

```python
for i, t in enumerate(soup.find_all("table")):
    th = t.find("th")
    print(i, th.get_text(strip=True) if th else "sem cabeçalho")
```

➡️ A tabela trimestral ("GameStop Quarterly Revenue") costuma ser a de índice **1**.

**Extração com BeautifulSoup:**

```python
gme_revenue = pd.DataFrame(columns=["Date", "Revenue"])

for table in soup.find_all("table"):
    if "GameStop Quarterly Revenue" in table.get_text():
        for row in table.find("tbody").find_all("tr"):
            col = row.find_all("td")
            if len(col) == 2:
                gme_revenue = pd.concat(
                    [gme_revenue, pd.DataFrame({"Date": [col[0].text.strip()],
                                                "Revenue": [col[1].text.strip()]})],
                    ignore_index=True)
```

**(Alternativa) com `read_html`:**

```python
gme_revenue = pd.read_html(StringIO(html_data_2))[1]
gme_revenue.columns = ["Date", "Revenue"]
```

**Limpeza:**

```python
gme_revenue["Revenue"] = gme_revenue["Revenue"].str.replace(r",|\$", "", regex=True)
gme_revenue.dropna(inplace=True)
gme_revenue = gme_revenue[gme_revenue["Revenue"] != ""]

gme_revenue.tail()
```

✅ **Checkpoint — saída esperada (aproximada):** últimos trimestres (mais antigos, ~2005–2006) com valores numéricos puros, ex. `1667`, `534`, `416`, `475`, `709`.

📸 **Screenshot Q4:** código + `gme_revenue.tail()`.

---

### 7.5 Questão 5 — Dashboard da Tesla

```python
make_graph(tesla_data, tesla_revenue, "Tesla")
```

✅ **Checkpoint:** duas áreas de gráfico:
- **Topo:** preço da ação (Share Price) de 2010 a jun/2021 — praticamente plano até 2019 e **explosão** em 2020–2021.
- **Base:** receita trimestral de 2009/2010 a 2021 — crescimento acentuado a partir de 2017–2018 (Model 3).
- Barra de rolagem (*range slider*) visível.

📸 **Screenshot Q5:** a célula com `make_graph(...)` **e** o gráfico renderizado, incluindo o título "Tesla".

---

### 7.6 Questão 6 — Dashboard da GameStop

```python
make_graph(gme_data, gme_revenue, "GameStop")
```

✅ **Checkpoint:**
- **Topo:** preço baixo e estável/declinante por anos e o **pico histórico de janeiro/2021** (*short squeeze* impulsionado pelo Reddit/WallStreetBets).
- **Base:** receita com **sazonalidade forte** (picos no trimestre de fim de ano — Q4 fiscal) e tendência de queda a partir de ~2012–2016.

📸 **Screenshot Q6:** célula + gráfico com título "GameStop".

---

### 7.7 Se o gráfico não aparecer

Renderização Plotly varia com o ambiente. Tente, em ordem:

```python
import plotly.io as pio
pio.renderers.default = "iframe"          # JupyterLab do curso
# pio.renderers.default = "notebook"      # Jupyter clássico
# pio.renderers.default = "colab"         # Google Colab
```

Ou substitua `fig.show()` por:

```python
from IPython.display import display, HTML
fig_html = fig.to_html()
display(HTML(fig_html))
```

Ou salve em arquivo e abra no navegador:

```python
fig.write_html("tesla_dashboard.html")
```

---

## 8. Indo além: dashboard das 4 empresas

O enunciado menciona **Tesla, Amazon, AMD e GameStop**. O trabalho avaliado foca em Tesla e GameStop, mas um dashboard com as quatro demonstra domínio e é ótimo para portfólio.

### 8.1 Baixar os preços das quatro

```python
tickers = {"Tesla": "TSLA", "Amazon": "AMZN", "AMD": "AMD", "GameStop": "GME"}

precos = {}
for nome, tk in tickers.items():
    df = yf.Ticker(tk).history(period="max").reset_index()
    df["Date"] = pd.to_datetime(df["Date"], utc=True).dt.tz_localize(None)
    precos[nome] = df
    print(nome, df.shape, df["Date"].min().date(), "→", df["Date"].max().date())
```

### 8.2 Comparação normalizada (base 100)

Como os preços têm escalas muito diferentes, normalize para comparar **desempenho relativo**:

```python
inicio = "2016-01-01"
fig = go.Figure()
for nome, df in precos.items():
    d = df[df["Date"] >= inicio].copy()
    d["Base100"] = d["Close"] / d["Close"].iloc[0] * 100
    fig.add_trace(go.Scatter(x=d["Date"], y=d["Base100"], name=nome))
fig.update_layout(title="Desempenho relativo (base 100 = jan/2016)",
                  yaxis_type="log", yaxis_title="Índice (escala log)",
                  height=600, xaxis_rangeslider_visible=True)
fig.show()
```

> 🧠 **Escala logarítmica** é essencial aqui: um ganho de 100 → 200 e de 1000 → 2000 aparecem com a mesma altura (ambos +100%).

### 8.3 Candlestick (velas) + volume

```python
def candle(nome, inicio="2021-01-01", fim="2021-03-31"):
    d = precos[nome]
    d = d[(d["Date"] >= inicio) & (d["Date"] <= fim)]
    fig = make_subplots(rows=2, cols=1, shared_xaxes=True,
                        row_heights=[0.7, 0.3], vertical_spacing=0.05)
    fig.add_trace(go.Candlestick(x=d["Date"], open=d["Open"], high=d["High"],
                                 low=d["Low"], close=d["Close"], name="Preço"), row=1, col=1)
    fig.add_trace(go.Bar(x=d["Date"], y=d["Volume"], name="Volume"), row=2, col=1)
    fig.update_layout(title=f"{nome}: candlestick e volume", height=700,
                      xaxis_rangeslider_visible=False, showlegend=False)
    fig.show()

candle("GameStop")   # o short squeeze de jan/2021
```

### 8.4 Retornos e volatilidade

```python
import numpy as np

resumo = []
for nome, df in precos.items():
    d = df.set_index("Date")["Close"]
    ret = d.pct_change().dropna()
    resumo.append({
        "Empresa": nome,
        "Retorno diário médio (%)": ret.mean() * 100,
        "Volatilidade anual (%)": ret.std() * np.sqrt(252) * 100,
        "Maior alta diária (%)": ret.max() * 100,
        "Maior queda diária (%)": ret.min() * 100,
    })
pd.DataFrame(resumo).round(2)
```

> 🧠 **Volatilidade anualizada** = desvio-padrão dos retornos diários × √252 (≈ dias úteis/ano). Mede risco.

### 8.5 Médias móveis (tendência)

```python
d = precos["AMD"].copy()
d["MM50"] = d["Close"].rolling(50).mean()
d["MM200"] = d["Close"].rolling(200).mean()
d = d[d["Date"] >= "2018-01-01"]

fig = go.Figure()
fig.add_trace(go.Scatter(x=d["Date"], y=d["Close"], name="Fechamento"))
fig.add_trace(go.Scatter(x=d["Date"], y=d["MM50"], name="Média 50d"))
fig.add_trace(go.Scatter(x=d["Date"], y=d["MM200"], name="Média 200d"))
fig.update_layout(title="AMD — preço e médias móveis", height=550)
fig.show()
```

> 🧠 Quando a MM50 cruza **acima** da MM200 → "golden cross" (sinal de alta). Abaixo → "death cross".

### 8.6 Receita da Amazon e AMD

O curso não fornece páginas de receita para Amazon/AMD. Opções:

```python
amzn_is = yf.Ticker("AMZN").quarterly_income_stmt
amzn_is.loc["Total Revenue"]    # últimos ~5 trimestres
```

> ⚠️ O Yahoo retorna poucos trimestres. Para séries longas, use relatórios 10-Q/10-K (SEC EDGAR) — fora do escopo exigido.

### 8.7 (Opcional) Dashboard interativo com Dash

```python
!pip install dash
```

```python
from dash import Dash, dcc, html, Input, Output

app = Dash(__name__)
app.layout = html.Div([
    html.H2("Dashboard de Ações"),
    dcc.Dropdown(id="empresa", options=list(precos.keys()), value="Tesla"),
    dcc.Graph(id="grafico"),
])

@app.callback(Output("grafico", "figure"), Input("empresa", "value"))
def atualizar(nome):
    d = precos[nome]
    fig = go.Figure(go.Scatter(x=d["Date"], y=d["Close"], name=nome))
    fig.update_layout(title=f"{nome} — preço de fechamento", xaxis_rangeslider_visible=True)
    return fig

app.run(debug=True, jupyter_mode="inline")   # em versões recentes do Dash
```

---

## 9. Analisando os dados e extraindo insights

Um dashboard só tem valor com **interpretação**. Use este roteiro para escrever suas conclusões.

### 9.1 Perguntas que um analista faz

1. **Tendência:** o preço/receita sobe, desce ou fica lateral no longo prazo?
2. **Relação preço × fundamento:** o preço acompanha a receita? Há descolamento?
3. **Sazonalidade:** existem padrões que se repetem em determinados trimestres?
4. **Eventos:** quais picos/quedas bruscas existem e o que os explica?
5. **Risco:** quão volátil é a ação?

### 9.2 Leitura esperada — Tesla

- Receita cresce de forma consistente, com aceleração forte a partir de 2017–2018 (lançamento e escala do Model 3) e mais ainda em 2020–2021.
- O preço permanece relativamente estável até 2019 e **dispara em 2020** (lucros consecutivos, inclusão no S&P 500 em dez/2020, entusiasmo com veículos elétricos).
- **Insight:** o preço cresceu **muito mais rápido** que a receita — o mercado precificou **expectativas de crescimento futuro** (múltiplos elevados). Isso indica potencial, mas também **risco de valuation**.

### 9.3 Leitura esperada — GameStop

- Receita com **sazonalidade clara**: picos no trimestre de festas (novembro–janeiro; ano fiscal termina em janeiro).
- Tendência de **queda da receita** a partir de meados da década de 2010 (migração para downloads digitais).
- Preço em queda por anos, até o **pico explosivo de jan/2021**: *short squeeze* provocado por investidores de varejo (r/WallStreetBets), não por melhora nos fundamentos.
- **Insight:** o preço **descolou totalmente** da receita — movimento especulativo, altíssima volatilidade. Alerta de risco para clientes.

### 9.4 Leitura esperada — Amazon e AMD (se fizer o cap. 8)

- **Amazon:** crescimento de longo prazo sólido (e-commerce + AWS); forte valorização em 2020 (pandemia).
- **AMD:** recuperação impressionante a partir de 2016 (arquitetura Zen / Ryzen / EPYC), saindo de ~US$ 2 para mais de US$ 100.

### 9.5 Modelo de parágrafo de conclusão

> "A análise do dashboard da Tesla mostra que a receita trimestral cresceu de forma contínua desde 2010, com aceleração após 2018. O preço das ações, porém, cresceu de forma desproporcional em 2020–2021, indicando que o mercado precifica expectativas de crescimento futuro. Já a GameStop apresenta receita sazonal e em declínio, enquanto o preço sofreu um pico especulativo em janeiro de 2021, descolado dos fundamentos. Recomenda-se cautela a investidores com baixa tolerância a risco em ambos os casos, com a GameStop apresentando o maior risco especulativo."

---

## 10. Usando prompts (IA) de forma eficaz no projeto

O enunciado cita "usar prompts para acessar e processar dados". Assistentes de IA (ChatGPT, watsonx, Copilot, etc.) aceleram muito — **se você souber perguntar**.

### 10.1 Estrutura de um bom prompt

**Contexto + Tarefa + Restrições + Formato de saída.**

### 10.2 Prompts úteis (copie e adapte)

**Extração:**
> "Tenho um HTML com várias tabelas. Escreva um código Python usando requests e BeautifulSoup que encontre a tabela cujo texto contenha 'Tesla Quarterly Revenue' e retorne um DataFrame pandas com as colunas Date e Revenue. Use pd.concat, não append."

**Limpeza:**
> "Minha coluna 'Revenue' em pandas tem valores como '$10,389' e algumas strings vazias. Escreva o código para remover '$' e ',' com regex, remover vazios e NaN, e converter para float."

**Depuração:**
> "Recebi este erro ao comparar datas no pandas: `TypeError: Invalid comparison between dtype=datetime64[ns, America/New_York] and str`. Explique a causa e mostre como corrigir."

**Visualização:**
> "Com Plotly graph_objects, crie um subplot 2x1 com eixo X compartilhado: em cima o preço de fechamento (coluna Close), embaixo a receita (coluna Revenue), com range slider."

**Interpretação:**
> "Dado que a receita da GameStop caiu entre 2015 e 2020 mas o preço subiu mais de 1000% em janeiro de 2021, explique em linguagem de analista financeiro o que isso sugere."

### 10.3 Boas práticas

- ✅ Sempre **rode e verifique** o código gerado.
- ✅ Peça **explicações linha a linha** — é assim que você aprende.
- ⚠️ IA pode inventar URLs, métodos inexistentes ou versões antigas (ex.: `df.append`). Desconfie.
- ⚠️ Não peça para a IA "fazer o trabalho inteiro": você precisa entender para os quizzes.

---

## 11. Solução de problemas (Troubleshooting)

### 11.1 `yfinance` retorna DataFrame vazio ou erro de rate limit

Sintomas: `YFRateLimitError`, `No data found`, `Too Many Requests`.

Soluções:
1. Atualize: `!pip install yfinance --upgrade` e **reinicie o kernel**.
2. Aguarde alguns minutos e tente de novo.
3. Rode em outro ambiente (Colab/local).
4. Evite chamar `.info` repetidamente (é a chamada mais sujeita a bloqueio).

### 11.2 Erro de fuso horário ao filtrar datas

```
TypeError: Invalid comparison between dtype=datetime64[ns, America/New_York] and str
```

Correção — remova o fuso antes de chamar `make_graph`:

```python
tesla_data["Date"] = pd.to_datetime(tesla_data["Date"]).dt.tz_localize(None)
gme_data["Date"] = pd.to_datetime(gme_data["Date"]).dt.tz_localize(None)
```

### 11.3 `ValueError: could not convert string to float: ''`

Sobraram strings vazias em `Revenue`:

```python
tesla_revenue = tesla_revenue[tesla_revenue["Revenue"] != ""]
```

### 11.4 `ValueError: could not convert string to float: '$1,234'`

Limpeza não aplicada (ou aplicada sem `regex=True`):

```python
tesla_revenue["Revenue"] = tesla_revenue["Revenue"].str.replace(r",|\$", "", regex=True)
```

### 11.5 `AttributeError: 'DataFrame' object has no attribute 'append'`

pandas ≥ 2.0. Troque por `pd.concat([...], ignore_index=True)`.

### 11.6 `AttributeError: 'NoneType' object has no attribute 'find_all'`

O `find("tbody")` não encontrou nada. Causas:
- Página errada/bloqueada → verifique `requests.get(url).status_code` e `print(html_data[:500])`.
- O parser `html.parser` às vezes não cria `<tbody>`; teste `html5lib` ou use `table.find_all("tr")` direto.

### 11.7 `KeyError: 'Date'`

Você esqueceu `reset_index(inplace=True)`. Confira com `tesla_data.columns`.

### 11.8 `ImportError: lxml not found` / `html5lib not found`

```python
!pip install lxml html5lib
```
Reinicie o kernel.

### 11.9 Gráfico Plotly não aparece (célula vazia)

Ver seção 7.7. Instale `nbformat>=4.2.0` e reinicie o kernel.

### 11.10 `KeyError` em `pd.read_html(...)[1]` / tabela errada

Liste todas as tabelas e inspecione:

```python
tabs = pd.read_html(StringIO(html_data))
for i, t in enumerate(tabs):
    print(i, t.columns.tolist(), t.shape)
```

---

## 12. Checklist de entrega e screenshots

### 12.1 Checklist antes de enviar

- [ ] Lab 1 concluído (yfinance — Apple/AMD).
- [ ] Lab 2 concluído (web scraping — Netflix/Amazon).
- [ ] Quiz 1 feito.
- [ ] Quiz 2 feito.
- [ ] Notebook final roda **do início ao fim** (*Kernel → Restart & Run All*) sem erros.
- [ ] Q1: `tesla_data.head()` visível.
- [ ] Q2: `tesla_revenue.tail()` visível e **limpo**.
- [ ] Q3: `gme_data.head()` visível.
- [ ] Q4: `gme_revenue.tail()` visível e **limpo**.
- [ ] Q5: dashboard Tesla renderizado.
- [ ] Q6: dashboard GameStop renderizado.
- [ ] Screenshots salvos com nomes claros.
- [ ] Notebook salvo (`.ipynb`) e, se pedido, compartilhado/exportado.

### 12.2 Como tirar bons screenshots

| Boa prática | Por quê |
|---|---|
| Mostrar **célula de código + saída** na mesma imagem | O avaliador (IA) precisa ver ambos |
| Zoom legível (100–125%) | Texto pequeno pode não ser lido |
| Incluir o título/número da questão | Facilita a correspondência com a rubrica |
| Evitar cortar colunas da tabela | Parte da nota é "as colunas corretas aparecem" |
| No gráfico, incluir título, eixos e range slider | Evidencia uso correto de `make_graph` |

**Atalhos:**
- Windows: `Win + Shift + S`
- macOS: `Cmd + Shift + 4`
- Linux: `PrtSc` / `Shift + PrtSc`

**Nomes sugeridos:** `Q1_tesla_data.png`, `Q2_tesla_revenue.png`, `Q3_gme_data.png`, `Q4_gme_revenue.png`, `Q5_tesla_dashboard.png`, `Q6_gme_dashboard.png`.

### 12.3 Dicas para a avaliação por IA

- Responda o que foi **exatamente** pedido: nomes de variáveis como no enunciado (`tesla_data`, `tesla_revenue`, `gme_data`, `gme_revenue`).
- Use os métodos citados (`head()`, `tail()`, `make_graph`).
- Se houver campo de texto, **descreva brevemente** o que o código faz e o que o resultado mostra (use o cap. 9).
- Não deixe células com erros vermelhos no notebook final.

---

## 13. Código completo consolidado

> Cole em um notebook novo e rode de uma vez (após instalar as bibliotecas).

```python
# ==============================================================
# 0. INSTALAÇÃO (rodar uma vez; depois reinicie o kernel)
# ==============================================================
# !pip install yfinance --upgrade
# !pip install bs4 nbformat plotly lxml html5lib

# ==============================================================
# 1. IMPORTS
# ==============================================================
import yfinance as yf
import pandas as pd
import requests
from bs4 import BeautifulSoup
from io import StringIO
import plotly.graph_objects as go
from plotly.subplots import make_subplots
import warnings
warnings.filterwarnings("ignore", category=FutureWarning)

# ==============================================================
# 2. FUNÇÃO DE GRÁFICO
# ==============================================================
def make_graph(stock_data, revenue_data, stock):
    stock_data = stock_data.copy()
    revenue_data = revenue_data.copy()
    stock_data["Date"] = pd.to_datetime(stock_data["Date"], utc=True).dt.tz_localize(None)
    revenue_data["Date"] = pd.to_datetime(revenue_data["Date"])
    s = stock_data[stock_data.Date <= "2021-06-14"]
    r = revenue_data[revenue_data.Date <= "2021-04-30"]
    fig = make_subplots(rows=2, cols=1, shared_xaxes=True,
                        subplot_titles=("Historical Share Price", "Historical Revenue"),
                        vertical_spacing=.3)
    fig.add_trace(go.Scatter(x=s.Date, y=s.Close.astype(float), name="Share Price"), row=1, col=1)
    fig.add_trace(go.Scatter(x=r.Date, y=r.Revenue.astype(float), name="Revenue"), row=2, col=1)
    fig.update_xaxes(title_text="Date", row=1, col=1)
    fig.update_xaxes(title_text="Date", row=2, col=1)
    fig.update_yaxes(title_text="Price ($US)", row=1, col=1)
    fig.update_yaxes(title_text="Revenue ($US Millions)", row=2, col=1)
    fig.update_layout(showlegend=False, height=900, title=stock,
                      xaxis_rangeslider_visible=True)
    fig.show()

# ==============================================================
# 3. FUNÇÃO AUXILIAR DE SCRAPING DE RECEITA
# ==============================================================
def scrape_quarterly_revenue(url, keyword):
    html = requests.get(url, headers={"User-Agent": "Mozilla/5.0"}).text
    soup = BeautifulSoup(html, "html.parser")
    df = pd.DataFrame(columns=["Date", "Revenue"])
    for table in soup.find_all("table"):
        if keyword in table.get_text():
            for row in table.find_all("tr"):
                col = row.find_all("td")
                if len(col) == 2:
                    df = pd.concat([df, pd.DataFrame({"Date": [col[0].text.strip()],
                                                      "Revenue": [col[1].text.strip()]})],
                                   ignore_index=True)
            break
    df["Revenue"] = df["Revenue"].str.replace(r",|\$", "", regex=True)
    df.dropna(inplace=True)
    df = df[df["Revenue"] != ""]
    return df

# ==============================================================
# Q1 — TESLA (yfinance)
# ==============================================================
tesla = yf.Ticker("TSLA")
tesla_data = tesla.history(period="max")
tesla_data.reset_index(inplace=True)
display(tesla_data.head())

# ==============================================================
# Q2 — TESLA REVENUE (web scraping)
# ==============================================================
url_tsla = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/revenue.htm"
tesla_revenue = scrape_quarterly_revenue(url_tsla, "Tesla Quarterly Revenue")
display(tesla_revenue.tail())

# ==============================================================
# Q3 — GAMESTOP (yfinance)
# ==============================================================
gamestop = yf.Ticker("GME")
gme_data = gamestop.history(period="max")
gme_data.reset_index(inplace=True)
display(gme_data.head())

# ==============================================================
# Q4 — GAMESTOP REVENUE (web scraping)
# ==============================================================
url_gme = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/stock.html"
gme_revenue = scrape_quarterly_revenue(url_gme, "GameStop Quarterly Revenue")
display(gme_revenue.tail())

# ==============================================================
# Q5 — DASHBOARD TESLA
# ==============================================================
make_graph(tesla_data, tesla_revenue, "Tesla")

# ==============================================================
# Q6 — DASHBOARD GAMESTOP
# ==============================================================
make_graph(gme_data, gme_revenue, "GameStop")
```

> ⚠️ Mesmo usando a função auxiliar, **no notebook de entrega** é recomendável deixar o passo a passo explícito de cada questão (como no cap. 7) — o avaliador procura ver `requests.get`, `BeautifulSoup`, `str.replace`, etc. em cada questão.

---

## 14. Glossário

| Termo | Definição |
|---|---|
| **Ticker** | Código da ação na bolsa (TSLA, AMZN, AMD, GME) |
| **OHLC** | Open, High, Low, Close — abertura, máxima, mínima, fechamento |
| **Volume** | Quantidade de ações negociadas no período |
| **Dividendos** | Parte do lucro distribuída aos acionistas |
| **Split (desdobramento)** | Divisão de cada ação em várias (ex.: 5:1); o preço é ajustado retroativamente |
| **Receita (Revenue)** | Total faturado com vendas no período, antes de custos |
| **Trimestre (Quarter)** | Período de 3 meses; empresas americanas publicam resultados trimestrais (10-Q) |
| **Ano fiscal** | Ano contábil da empresa (a GameStop fecha em janeiro) |
| **Short squeeze** | Alta brusca forçada pela recompra de ações por vendedores a descoberto |
| **Volatilidade** | Grau de oscilação dos preços; medida de risco |
| **Média móvel** | Média dos últimos N preços; suaviza a série e mostra tendência |
| **Web scraping** | Extração automatizada de dados de páginas web |
| **Parser** | Programa que interpreta o HTML e cria uma árvore navegável |
| **DataFrame** | Estrutura tabular do pandas |
| **Dashboard** | Painel visual que consolida indicadores-chave |
| **Range slider** | Barra de seleção de intervalo no eixo X (Plotly) |
| **Rate limit** | Limite de requisições imposto por um servidor |
| **Regex** | Expressão regular — linguagem de padrões de texto |

---

## 15. Exercícios extras para fixação

1. **Amazon com yfinance:** baixe o histórico de `AMZN`, faça `reset_index` e plote o `Close` com Plotly Express (`px.line`).
2. **Dividendos:** quais das 4 empresas pagam dividendos? (`yf.Ticker(tk).dividends`). Dica: praticamente nenhuma paga regularmente — por quê?
3. **Splits:** liste os desdobramentos da Tesla, Amazon e GameStop com `.splits`.
4. **Receita anual:** extraia a tabela **anual** da Tesla (índice 0) e faça um gráfico de barras.
5. **Crescimento YoY:** com `tesla_revenue` convertida para float, calcule o crescimento ano contra ano de cada trimestre (`pct_change(-4)` com os dados ordenados do mais recente para o mais antigo, ou ordene crescente e use `pct_change(4)`).
6. **Sazonalidade GME:** crie uma coluna `Mes = pd.to_datetime(Date).dt.month` e calcule a receita média por mês de fechamento do trimestre. Qual trimestre é o mais forte?
7. **Correlação:** reamostre o preço da Tesla para trimestral (`resample("QE").last()`), junte com a receita (`merge` por data) e calcule `corr()`.
8. **Janeiro/2021:** calcule a variação percentual da GME entre 01/01/2021 e a máxima de janeiro.
9. **Dashboard 2x2:** crie um `make_subplots(rows=2, cols=2)` com o preço das 4 empresas.
10. **Exportar:** salve os dashboards com `fig.write_html(...)` e abra no navegador — ótimo para portfólio (GitHub Pages).

<details>
<summary><b>Solução do exercício 7 (correlação preço × receita)</b></summary>

```python
tr = tesla_revenue.copy()
tr["Date"] = pd.to_datetime(tr["Date"])
tr["Revenue"] = tr["Revenue"].astype(float)
tr = tr.sort_values("Date")

td = tesla_data.copy()
td["Date"] = pd.to_datetime(td["Date"], utc=True).dt.tz_localize(None)
tq = td.set_index("Date")["Close"].resample("QE").last().reset_index()

m = pd.merge_asof(tr, tq.sort_values("Date"), on="Date", direction="nearest")
print(m[["Revenue", "Close"]].corr())
```
</details>

<details>
<summary><b>Solução do exercício 9 (grade 2×2)</b></summary>

```python
fig = make_subplots(rows=2, cols=2, subplot_titles=list(precos.keys()))
pos = [(1,1), (1,2), (2,1), (2,2)]
for (nome, d), (r, c) in zip(precos.items(), pos):
    fig.add_trace(go.Scatter(x=d["Date"], y=d["Close"], name=nome), row=r, col=c)
fig.update_layout(height=800, title="Preço de fechamento — 4 empresas", showlegend=False)
fig.show()
```
</details>

---

## 🎯 Resumo final em uma página

```
1. yfinance     →  yf.Ticker("TSLA").history(period="max").reset_index()
2. requests     →  html = requests.get(url).text
3. BeautifulSoup→  soup = BeautifulSoup(html, "html.parser")
                    table → tbody → tr → td
   ou read_html →  pd.read_html(StringIO(html))[1]
4. Limpeza      →  str.replace(r",|\$", "", regex=True); dropna; remover ""
5. Plotly       →  make_graph(stock_df, revenue_df, "Nome")
6. Entrega      →  screenshots (código + saída) de Q1..Q6
7. Insight      →  Tesla: preço > receita (expectativa)
                   GameStop: preço descolado da receita (especulação)
```

**Bons estudos e boa entrega! 🚀**
