Catálogo de Dados

MVP – Pipeline GA4 E-commerce no Databricks


---

1. Visão Geral

Este catálogo descreve os conjuntos de dados utilizados no MVP, incluindo:

Estrutura das tabelas

Descrição dos atributos

Tipos de dados

Domínios esperados

Regras de qualidade

Linhagem dos dados


O objetivo do catálogo é garantir governança, entendimento e reprodutibilidade do pipeline analítico.


---

2. Linhagem dos Dados (Data Lineage)

Origem:
Google Analytics 4 – Obfuscated Sample E-commerce Dataset
(BigQuery Public Dataset – Google Cloud Marketplace)

Fluxo de dados:

BigQuery (GA4)
   ↓
Databricks (Ingestão PySpark)
   ↓
Delta Lake (Bronze → Silver → Gold)
   ↓
Tabelas analíticas (Modelo Estrela)

Ferramentas utilizadas:

BigQuery

Databricks Community Edition

Apache Spark (PySpark)

Delta Lake



---

3. Camadas de Dados

3.1 Bronze

Dados brutos ingeridos do BigQuery

Estrutura original do GA4

Sem transformações de negócio


3.2 Silver

Dados tratados

Arrays explodidos

Tipos padronizados

Eventos normalizados


3.3 Gold

Modelo dimensional

Tabelas fato e dimensões

Dados prontos para análise



---

4. Tabela Fato

📊 fact_events

Tabela central do modelo estrela, representando eventos de navegação e conversão.

Descrição

Cada registro representa um evento executado por um usuário, possivelmente associado a um produto.

Estrutura

Campo	Tipo	Descrição

event_timestamp	TIMESTAMP	Data e hora do evento
event_date	DATE	Data do evento
event_name	STRING	Tipo do evento
user_sk	BIGINT	Chave substituta do usuário
product_sk	BIGINT	Chave substituta do produto
session_id	STRING	Identificador da sessão
price	DOUBLE	Preço do produto
quantity	INT	Quantidade do item
revenue	DOUBLE	Receita gerada
is_conversion	BOOLEAN	Indicador de compra
traffic_sk	BIGINT	Chave do canal de tráfego
time_sk	BIGINT	Chave da dimensão tempo


Domínios Esperados

event_name:

view_item

add_to_cart

begin_checkout

purchase


price:

≥ 0


quantity:

≥ 1


is_conversion:

true / false




---

5. Tabelas Dimensão


---
 dim_users

Descrição

Contém informações do perfil e comportamento do usuário.

Campo	Tipo	Descrição

user_sk	BIGINT	Chave substituta
user_id	STRING	Identificador anonimizado
user_type	STRING	Novo ou Recorrente
first_touch_date	DATE	Primeira interação
device_category	STRING	Tipo de dispositivo
country	STRING	País do usuário


Domínios Esperados

user_type: new / returning

device_category: mobile / desktop / tablet



---

 dim_products

Descrição

Informações dos produtos visualizados ou comprados.

Campo	Tipo	Descrição

product_sk	BIGINT	Chave substituta
product_id	STRING	ID do produto
product_name	STRING	Nome do produto
category	STRING	Categoria
brand	STRING	Marca
price	DOUBLE	Preço do produto


Domínios Esperados

price: ≥ 0

category: conforme catálogo GA4



---
 dim_time

Descrição

Dimensão temporal para análises por data e horário.

Campo	Tipo	Descrição

time_sk	BIGINT	Chave substituta
date	DATE	Data
year	INT	Ano
month	INT	Mês
day	INT	Dia
day_of_week	STRING	Dia da semana
hour	INT	Hora do dia


Domínios Esperados

hour: 0–23

month: 1–12



---

 dim_traffic

Descrição

Origem e canal de tráfego da sessão.

Campo	Tipo	Descrição

traffic_sk	BIGINT	Chave substituta
source	STRING	Origem
medium	STRING	Meio
channel	STRING	Canal agrupado


Domínios Esperados

channel:

Organic Search

Paid Search

Direct

Referral

Social




---

6. Regras de Qualidade de Dados

Validações Aplicadas

Verificação de valores nulos em chaves

Consistência entre eventos e produtos

Preços não negativos

Duplicidade de eventos

Cardinalidade das dimensões


Observações

Eventos sem associação a produtos são esperados (ex.: page_view)

Dados são anonimizados por padrão (GA4)



---

7. Considerações Finais

Este catálogo garante:

Clareza semântica

Governança mínima

Base para evolução do pipeline

Integração com ferramentas de BI

Facilidade de auditoria e manutenção



---

Se quiser, posso:

📊 gerar o diagrama visual do modelo estrela

🧪 criar testes automáticos de qualidade

🏷️ adaptar o catálogo ao padrão Data Catalog / DataHub

🖊️ ajustar linguagem para norma ABNT


É só dizer 👍
