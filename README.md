# Relatório de Desempenho - Vendas e KPIs no Mercado de Jardinagem

![Plant Company Performance Report.png](Plant_Company_Performance_Report.png)

# Contexto

Este projeto simula a análise de desempenho de uma empresa fictícia de plantas e jardinagem entre 2022 e 2024.

O objetivo foi estruturar os dados em modelo dimensional (tabelas fato e dimensão), criar medidas em DAX no Power BI (Sales, Quantity, COGS, Gross Profit, YTD e PYTD) e desenvolver um dashboard interativo que transforme esses cálculos em **insights acionáveis** para a gestão.

---

# Objetivo

Fornecer uma visão clara dos principais KPIs de vendas e rentabilidade, permitindo que diretores:

- Acompanhem o desempenho anual (YTD);
- Comparem resultados com o ano anterior (PYTD);
- Identifiquem padrões e variações relevantes por país, mês e cliente.

Além da aplicação de conceitos de **Business Intelligence**, este projeto também serve como **demonstração prática de minhas habilidades em modelagem DAX, storytelling com dados e design de dashboards executivos**.

---

# Fonte de Dados

> **Período:** 2022 a 2024
> 
> 
> **Entidade de análise:** contas/clientes vinculados a produtos (via `Account_id`)
> 
> **Tabelas utilizadas:**
> 
> - **`Fact Table` (Sales/Invoice):** transações de vendas (produto, preço, data, identificador).
> - **`Account Table` (Unique Accounts):** dados dos clientes (identificação e localização).
> - **`Plant Table` (Product Data):** catálogo de produtos (família, grupo, tamanho, tipo: indoor/outdoor).

**Principais atributos das tabelas:**

- `Product_id` Identificador único do produto
- `Sales_USD` Valor de venda
- `quantity` Quantidade vendida
- `Price_USD` Preço unitário
- `COGS_USD` Custo dos produtos vendidos
- `Date_Time` Data da transação
- `Account_id` Identificador do cliente

<aside>
💡

### **Variáveis derivadas criadas para análise:**

</aside>

- Vendas (`Sales`)
- Quantidade (`Quantity`)
- `Gross Profit` (Lucro Bruto)
- `COGs` (Cost of Goods Sold / Custo dos Produtos Vendidos)
- `PYTD` (Prior Year to Date/Acumulado do mesmo período do ano passado)
    - Sales
    - Quantity
    - Gross Profit
- `YTD` (Year to Date/Acumulado do Ano)
    - Sales
    - Quantity
    - Gross Profit

---

# Modeling em DAX

### Indicadores básicos

```jsx
Sales = SUM(Fact_sales[Sales_USD])
Quantity = SUM(Fact_sales[Quantity])
COGS = SUM(Fact_sales[COGS_USD])
Gross Profit = [Sales] - [COGS]
```

### Tabela de datas

```jsx
Dim_Date = 
CALENDAR (
    DATE ( 2022, 1, 1 ),
    DATE ( 2024, 12, 31 )
)

Inpast = 
VAR lastsalesdate = MAX(Fact_Sales[Date_Time])
VAR lastsalesdatePY = EDATE(lastsalesdate,-12)
RETURN
Dim_Date[Date]<= lastsalesdatePY
```

![image.png](image.png)

### Medidas comparativas (YTD e PYTD)

Essas medidas permitem comparar o desempenho atual com o mesmo período do ano anterior.

```jsx
PYTD_Sales =
CALCULATE (
[Sales],
SAMEPERIODLASTYEAR ( Dim_Date[Date] ),
Dim_Date[Inpast] = TRUE()
)

PYTD_Quantity = 
CALCULATE (
    [Quantity],
    SAMEPERIODLASTYEAR ( Dim_Date[Date] ),
    Dim_Date[Inpast] = TRUE()
)

PYTD_Gross Profit = 
CALCULATE (
    [Gross Profit],
    SAMEPERIODLASTYEAR ( Dim_Date[Date] ),
    Dim_Date[Inpast] = TRUE()
)
```

![image.png](image%201.png)

Desenvolvi tabelas auxiliares com valores essenciais para filtros, garantindo slicers mais limpos e intuitivos e demonstrando boas práticas de modelagem no Power BI.

![image.png](image%202.png)

![image.png](image%203.png)

---

# Dashboard

Após a modelagem, foi criado um dashboard no Power BI, com foco em clareza executiva e navegação intuitiva.

![image.png](image%204.png)

---

## Resumo Executivo Imediato (Cards Superiores)

> (YTD, Variação YTD vs PYTD, PYTD e GP%)
> 

![image.png](image%205.png)

<aside>
💡 **Objetivo:** mostrar os principais KPIs: desempenho acumulado no ano (YTD), comparação com o ano anterior (PYTD), variação e margem bruta (GP%).

</aside>

Ajudam o usuário a ter uma visão clara e rápida do estado geral antes de mergulhar nos detalhes

---

## Treemap

> (Bottom 10 YTD vs PYTD por País)
> 

![image.png](f0209e0b-c1ef-4e60-8e98-b088ecbfbc76.png)

<aside>
💡

**Objetivo:** destacar os países com **piores desempenhos relativos** em relação ao ano anterior.

</aside>

O treemap é útil para mostrar hierarquia e magnitude negativa em espaço compacto, permitindo identificar facilmente onde estão os maiores problemas.

---

## Waterfall Chart

> (YTD vs PYTD por Mês)
> 

![image.png](78fd473e-96b1-4bfb-8bfb-906fe62b4bab.png)

<aside>
💡

**Objetivo:** mostrar **como cada mês contribuiu** para o aumento ou diminuição no acumulado YTD em comparação ao PYTD.

</aside>

O gráfico waterfall é ideal para visualizar incrementos e reduções ao longo do tempo e entender a dinâmica mensal.

---

## Colunas com Linha

> (Value YTD por Mês + Linha Value PYTD)
> 

![image.png](d4e6db45-70b2-4c09-ba88-ff938994cc27.png)

<aside>
💡

**Objetivo:** comparar o **volume acumulado mês a mês** com o desempenho do ano anterior.

</aside>

As colunas permitem visualizar o YTD por mês, enquanto a linha adiciona a referência histórica (PYTD), facilitando a comparação temporal.

---

## Scatter Plot (Dispersão)

> (Value YTD vs GP% por Conta)
> 

![image.png](image%206.png)

<aside>
💡

**Objetivo:** analisar a **relação entre volume de vendas e margem bruta** para cada cliente/conta.

</aside>

O scatter plot revela padrões e concentrações, mostrando contas de alto valor e baixa margem, além de destacar oportunidades e riscos.

---

# Dashboard Interativo (Power BI)

![image.png](image%207.png)

[PlantCo_Dashboard.pbix](PlantCo_Dashboard.pbix)

---

# Conclusões

O dashboard consolidou informações de vendas em um painel único, possibilitando acompanhar o acumulado do ano (YTD), comparar com o mesmo período anterior (PYTD) e avaliar margem bruta em diferentes níveis de análise. Isso permitiu identificar padrões importantes, como oscilações sazonais, variações de desempenho entre países e clientes de alto volume com margens reduzidas.

<aside>
💡 Combinando diferentes tipos de visualização, o relatório oferece tanto uma leitura executiva imediata quanto a exploração detalhada dos dados, transformando métricas isoladas em uma visão clara e estratégica do negócio.

</aside>

---

Obrigado pela leitura!

# Sobre o autor

![123456789.png](123456789.png)

**Lucas Correa**

Data Storyteller | Dataviz | Analista de Dados & BI

Curioso por natureza e apaixonado por transformar dados em histórias visuais. Neste espaço, você encontrará meus projetos de visualização de dados, dashboards e análises.

Me acompanhe nas redes sociais:

[https://www.linkedin.com/in/correa-lucas/](https://www.linkedin.com/in/correa-lucas/)

[https://github.com/corlcs](https://github.com/corlcs)

[https://medium.com/@corlcs](https://medium.com/@corlcs)

[https://substack.com/@corlcs/posts](https://substack.com/@corlcs/posts)