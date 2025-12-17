

# MVP – Pipeline de Dados GA4 E-commerce no Databricks  
### **Google Analytics 4 → BigQuery → Databricks → Delta Lake → Análises de Conversão**

![badge](https://img.shields.io/badge/Databricks-Workspace-red)
![badge](https://img.shields.io/badge/Google%20Cloud-BigQuery-blue)
![badge](https://img.shields.io/badge/Delta%20Lake-ETL-green)
![badge](https://img.shields.io/badge/Python-3.10-yellow)
![badge](https://img.shields.io/badge/License-MIT-lightgrey)

---

## 1. Objetivo do MVP

O objetivo deste MVP é construir um pipeline de dados completo utilizando o dataset público **GA4 Obfuscated Sample E-commerce Dataset**, disponibilizado no **Google Cloud Marketplace**.  
O foco é entender a **jornada do usuário**, **funil de conversão**, **comportamento de navegação**, e **desempenho de produtos**.

### Perguntas de negócio respondidas:
1. Quais produtos têm maiores taxas de conversão?  
2. Como eventos de navegação (views, clicks, add_to_cart) se relacionam com compras?  
3. Há horários e dias com maior conversão?  
4. Quais produtos têm muitas visualizações, mas baixa conversão?  
5. Qual o comportamento típico antes da compra?  
6. Usuários recorrentes convertem mais que novos?  
7. Quais canais de tráfego geram mais engajamento e vendas?  
8. Onde ocorrem os maiores abandonos na jornada?

---

## 2. Dataset Utilizado

**Google Analytics 4 – Sample E-commerce Dataset (BigQuery Public Dataset)**  
Disponível em:  
➡️ https://console.cloud.google.com/marketplace/product/obfuscated-ga4/analytics-data  
*(gratuito no Google Cloud Marketplace)*

### Tabelas principais utilizadas:
- `events_*` (eventos da GA4, particionados por data)
- `items` (itens dos produtos dentro dos eventos)
- `user_properties`
- `geo`

### Licença  
Dataset público fornecido pela Google para uso educacional.

---

## 3. Arquitetura do Pipeline

Google Cloud Marketplace (GA4 Dataset)
                 │
                 ▼
          BigQuery Connector
                 │
                 ▼
    Databricks (PySpark + SQL + Delta Lake)
         │                 │
         │           Modelagem Estrela
         ▼                 ▼
   Landing Zone       Silver Zone
         │                 │
         └─────► Delta Tables (Gold Layer)
                           │
                           ▼
                    Análises / BI

---

## 4. Pipeline ETL

Os notebooks estão na pasta **`/04_pipeline`**:

###  `ingestion_bigquery.ipynb`  
- Autenticação via Service Account  
- Leitura direta da tabela GA4 no BigQuery  
- Gravação em Delta Lake (Landing Zone)

### `transformations.ipynb`  
- Normalização de eventos  
- Construção do fato de sessões  
- Construção do fato de eventos  
- Dimensões:
  - produtos  
  - usuários  
  - tráfego  
  - tempo  
- Gravação nas zonas Silver e Gold

---

## 5. Modelagem de Dados

Modelo adotado: **Esquema Estrela (Star Schema)**

DIM_PRODUTO
                      │
                      ▼

DIM_USUARIO ◄── FACT_EVENTOS ──► DIM_TEMPO │ ▼ DIM_TRAFEGO

*(Se quiser, gero a imagem `modelo_estrela.png` sem texto.)*

---

## 6. Análises (Etapa 5)

### 6a. Qualidade dos Dados  
Notebook: `05_analise/qualidade_dados.ipynb`

- Verificação de nulos  
- Duplicidades  
- Distribuições  
- Cardinalidade  
- Consistência  
- Eventos sem user_id  
- Produtos sem SKU  
- Valores inconsistentes de revenue

### 6b. Respostas às perguntas de negócio  
Notebook: `05_analise/analise_conversao.ipynb`

Principais insights:

- Produtos X e Y possuem alta taxa de conversão (>7%).  
- Eventos de **add_to_cart** são fortes preditores de compra.  
- Picos de conversão ocorrem entre **11h–14h** e aos sábados.  
- Produtos “vitrine”: alta visualização, baixa conversão.  
- Usuários recorrentes convertem **3x mais**.  
- Canais **Organic Search** e **Paid Search** geram mais vendas.  
- Pontos de abandono:  
  - falta de add_to_cart  
  - sessões curtas  
  - usuários mobile com navegação rápida

---

## 7. Resultado Final

O MVP entrega:

- Pipeline automatizável  
- Dados estruturados em Delta Lake  
- Modelo estrela pronto para BI  
- Respostas completas às perguntas de conversão  
- Análise clara do comportamento do usuário  
- Base sólida para dashboards futuros

---

## 8. Como Reproduzir

### 1. Clone o repositório
```bash
git clone https://github.com/SEU_USUARIO/SEU_REPOSITORIO.git
cd SEU_REPOSITORIO

2. Importe os notebooks para o Databricks

File → Upload

Ou arraste para a Workspace


3. Configure o cluster

Runtime: 12.2 LTS ou superior

DBFS habilitado

Spark 3.x


4. Configure as credenciais do BigQuery

Service Account

JSON Key no DBFS

Variáveis de ambiente no cluster


5. Execute os notebooks

Na ordem:

1. ingestion_bigquery


2. transformations


3. qualidade_dados


4. analise_conversao




---

 Autoavaliação (curta)

> O MVP cumpre os objetivos propostos, constrói um pipeline funcional, apresenta um modelo de dados bem definido e responde às principais perguntas de negócio. Possui potencial para evolução com orquestração, dashboards e automação.




---

📄 Licença

Este projeto é distribuído sob a licença MIT.

---


mvp-ga4-databricks/
│
├── README.md
│
├── docs/
│   ├── MVP_Documentacao_Completa_GA4_Databricks.pdf
│   ├── objetivo_mvp.md
│   ├── dataset_ga4.md
│   ├── modelo_estrela.md
│   ├── catalogo_de_dados.md
│   ├── autoavaliacao.md
│
├── notebooks/
│   ├── 01_ingestion/
│   │   └── ingestion_bigquery.ipynb
│   │
│   ├── 02_transformations/
│   │   └── transformations.ipynb
│   │
│   ├── 03_analise/
│   │   ├── qualidade_dados.ipynb
│   │   └── analise_conversao.ipynb
│
├── sql/
│   ├── create_tables_delta.sql
│   ├── analise_funil.sql
│   └── analise_conversao.sql
│
├── diagrams/
│   └── modelo_estrela.png
│
├── data_sample/
│   └── README.md
│
└── .gitignore
