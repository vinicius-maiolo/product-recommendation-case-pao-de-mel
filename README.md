# product-recommendation-case-pao-de-mel

[Notion Link](https://bitter-fedora-901.notion.site/Product-Recommendation-Analysis-Case-Pao-De-Mel-1d62330fc8a0808a89d0c8b9081b40af?pvs=73)

**General Definitions of The Project:**

| **What**  | Support the Marketing team of the fictitious e-commerce *Pão de Mel* in **understanding customer purchase** **behavior** and **providing relevant product recommendations**.|
| --- | --- |
| **Why**  | This will help optimize campaign strategies and improve both profitability and user experience. |
| **Who** | Data Analytics Engineering Team + Support from Sales/Marketing Team |
| **How**  | Discovering patterns through EDA (Exploratory Data Analysis) and Building a Recommendation Data Model based on Apriori Algorythm.  |

### **Executive Summary – Key Findings (results)**

### **Sales Performance & Customer Behavior**

- **Snacks lead sales**, with 866k units sold — the top-selling department.
- **Snacks, Beverages, Frozen Products, and Pantry** combined account for **55% of total sales**.
- **Monday and Tuesday concentrate 35%** of weekly sales — with volumes **40% above the average** of other days.
- **82% of purchases happen between 8 AM and 6 PM**, highlighting prime store hours.
- **Alcohol sales peak on Thursdays and Fridays (35%)**, a distinct pattern compared to other categories.

### **Product Affinity & Cross-Sell Opportunities (Apriori Algorithm)**

- **Sparkling Water** shoppers often buy multiple flavors — up to **32% chance** of a second flavor purchase. *→ Suggested action: Multi-buy promotions.*
- **Bean buyers** show a **20% affinity** with garbanzo beans and hummus. *→ Suggested action: Legume bundle offers.*
- **Lean Ground Beef** and **Boneless Chicken Breasts** are often bought together (**30% confidence**) — likely by fitness-focused customers. *→ Suggested action: “High Protein” combos.*
- Strong link between **Zero Calorie Soda** and **healthy snacks**:
    - **52%** also buy **Fruit & Nut Bars**
    - **37%** of **Trail Mix** buyers also purchase soda

# Project Step-by-Step - **CRISP-DM** Methodology

---

- ## 🧠 1. Business Understanding
    - **1st Phase – Exploratory Data Analysis**
        - What is the sales distribution by:
            - Product department
            - Day of the week
            - Time of day
        - What are the Top 5 most sold products by department?
        - Are there time-based consumption patterns? (e.g. pizza in the evening, coffee in the morning)
    - **2nd Phase – Product Recommendation**
        - Can we identify frequently co-purchased items using Association Rules?
        - Can we generate a product-level recommendation table with confidence scores and department data?
    - **Success Criteria for both phases:**
        - Usability and clarity of final data product for the Marketing team
        - Key insights from the exploratory data analysis

---

- ## ☁️ 2. Data Understanding & Technological Stack Definitions
    - **Stack Definition**
        
        <p align="center">
  <img src="https://file.notion.so/f/f/25d1ad8a-548b-4fb9-8ddd-53498eff1b84/cf698acd-6427-48e0-897d-b9a84654f229/image.png?table=block&id=1d72330f-c8a0-8046-899c-f21d96b8ec1e&spaceId=25d1ad8a-548b-4fb9-8ddd-53498eff1b84&expirationTimestamp=1744999200000&signature=6AH-Lw28YULJkSs1DGKZncH7x2vJC8vJsksXcZKhugc&downloadName=image.png" alt="Fluxo de Trabalho Projetado" width="500">
      </p>
        
  **Analytical Environment:**
        
  Due to the large size of the sales dataset (over 5 million rows), we will use **Google BigQuery** as our cloud-based data warehouse for storage and analytics.
        
    - **Data Stack:**
        
      - **Google BigQuery (GCP):** data processing and storage
      - **SQL:** querying and data manipulation
      - **Excel:** final delivery format for product recommendations + data validation analysis
      - **Python + Jupyter:** for data exploration + additional analysis
      - **KNIME:** for implementation of association rules (Apriori Algorithm)
  
    - **Data Mapping, Coding & Description [ONGOING]**
        - **raw .csv Files (Raw Layer)**
            
            **base_sales**:
            
            | **product_id** | **user_id** | **order_dow** | **order_hour_of_day** |
            | --- | --- | --- | --- |
            | ID of the register of the product in the source system. | ID of the register of the user in the source system. | Day of the week the product was buyed | Hour of the day the product was buyed |
            
            **base_departments**:
            
            | **department_id** | **department** |
            | --- | --- |
            | ID of the register of the product in the source system. | Name of the department |
            
            **base_products:**
            
            | **product_id** | **product_name** | **aisle_id** | **department_id** |
            | --- | --- | --- | --- |
            | ID of the register of the product in the source system. | Name of the product | ID of the register of the category in the source system | ID of the register of the department in the source system |
            
            **base_product_type:**
            
            | **aisle_id** | **aisle** |
            | --- | --- |
            | ID of the register of the category in the source system | Name of the product’s category |
            
        - **Curated Layer**
            - **sales**:
            
            | **product_id** | **user_id** | **day_of_week** | **hour_of_day** |
            | --- | --- | --- | --- |
            | ID of the register of the product in the source system. | ID of the register of the user in the source system. | Day of the week the product was buyed | Hour of the day the product was buyed |
            
            ```sql
            CREATE TABLE `curated_sales.sales` AS
            SELECT
                CAST(user_id AS STRING) as user_id
              , CAST(product_id AS STRING) as product_id
              , CAST(order_dow AS INT64) as day_of_week
              , CAST(order_hour_of_day AS INT64) as hour_of_day
            FROM  `raw_sales.sales`
            ```
            
            - **departments**:
            
            | **department_id** | **department** |
            | --- | --- |
            | ID of the register of the product in the source system. | Name of the department |
            
            ```sql
            CREATE TABLE `curated_sales.departments` AS
            SELECT
                CAST(department_id AS STRING) as department_id
              , CAST(department AS STRING) as department
            FROM  `raw_sales.departments`
            ```
            
            - **products**
            
            | **product_id** | **product_name** | **category_id** | **department_id** |
            | --- | --- | --- | --- |
            | ID of the register of the product in the source system. | Name of the product | ID of the register of the category in the source system | ID of the register of the department in the source system |
            
            ```sql
            CREATE TABLE `curated_sales.products` AS
            SELECT
                CAST(product_id AS STRING) as product_id
              , CAST(product_name AS STRING) as product_name
              , CAST(aisle_id AS STRING) as category_id
              , CAST(department_id AS STRING) as department_id
            FROM  `raw_sales.products`;
            ```
            
            - **category**
            
            | **category_id** | **category_name** |
            | --- | --- |
            | ID of the register of the category in the source system | Name of the product’s category |
            
            ```sql
            CREATE TABLE `curated_sales.product_categories` AS
            SELECT
                CAST(aisle_id AS STRING) as category_id
              , CAST(aisle AS STRING) as category_name
            FROM  `raw_sales.product_types`;
            ```
            
        - **Data Warehouse**
            - **ft_sales:**
            
            | **product_id** | **user_id** | **day_of_week** | **hour_of_day** | **department_id** | **category_id** |
            | --- | --- | --- | --- | --- | --- |
            | ID of the register of the product in the source system. | ID of the register of the user in the source system. | Day of the week the product was buyed | Hour of the day the product was buyed | ID of the register of the department in the source system | ID of the register of the category in the source system |
            
            ```sql
            CREATE TABLE `dw_pao_de_mel.ft_sales` AS
            SELECT
                s.product_id
                , s.user_id
                , s.day_of_week
                , s.hour_of_day
                , p.category_id
                , p.department_id
            
            FROM  `curated_sales.sales` s
            
            LEFT JOIN `curated_sales.products` p
              ON s.product_id = p.product_id
              
            ```
            
            - dts_product_performance
            
            ```sql
            CREATE TABLE `dw_pao_de_mel.dts_product_performance` AS
            SELECT
               p.product_name
                , fs.day_of_week
                , fs.hour_of_day
                , pc.category_name
                , d.department
                , count(fs.user_id) as total_sales
                , count(distinct fs.user_id) as total_distinct_customers
            
            , FROM  `dw_pao_de_mel.ft_sales` fs
            
            LEFT JOIN `curated_sales.products` p
              ON fs.product_id = p.product_id
            
            LEFT JOIN `curated_sales.product_categories` pc
              ON fs.category_id = pc.category_id
            
            LEFT JOIN `curated_sales.departments` d
              ON fs.department_id = d.department_id
            
            GROUP BY 1,2,3,4,5
            ```
            
            - dts_product_recommendation_input
            
            | **user_id** | **user_product_list** |
            | --- | --- |
            | ID of the register of the user in the source system. | List of all products purchased by each user across the entire analysis period |
            
            ```sql
            CREATE TABLE `dw_pao_de_mel.dts_product_recommendation_input` AS
            
            with base_user_product as(
              SELECT DISTINCT 
              user_id
              , product_id
            FROM `dw-pao-de-mel.dw_pao_de_mel.ft_sales`
            )
            
            , base_products as (
              SELECT
              product_id,
              product_name
            
            FROM `curated_sales.products`
            )
            
            , base_user_product_name as(
              SELECT 
              bup.user_id
              , REGEXP_REPLACE(bp.product_name, r',\s*', ' & ') AS product_name
              FROM base_user_product bup
              JOIN base_products bp
              ON bup.product_id = bp.product_id
              
            )
            
              SELECT 
              user_id, 
              STRING_AGG(CAST(product_name AS STRING), ', ') AS user_product_list
              
              FROM base_user_product_name
              GROUP BY 1
              ORDER BY user_id asc
            
            ```
            

---

- ## 🔧 3. Data Pipeline Infrastructure (ETL)
    
    ### 🔄 ETL Steps
    
    1. **Extract**
        - Source: Local CSV files provided by the business team
        - Transfer: Files will be manually uploaded directly into BigQuery
    2. **Transform**
        - **Raw Layer:** Ingestion of raw files into BigQuery (`raw_sales` dataset) with no changes to structure
        - **Curated Layer:** Data type normalization, field renaming for clarity, and semantic alignment (e.g. `order_dow` → `day_of_week`)
        - **Warehouse Layer (`ft_sales`):** Denormalized fact table combining key fields for downstream analysis and model development
    3. **Load**
        - All intermediate and final datasets are stored in **BigQuery** for high-performance querying.
        - Final outputs (e.g. recommendation table) will be exported to Excel and Knime for business delivery.
    
    ---
    
    ### 📁 Dataset Summary
    
    | Layer | Dataset/Table Example | Purpose |
    | --- | --- | --- |
    | Raw Layer | `raw_sales.sales` | Original structure from source |
    | Curated Layer | `curated_sales.products` | Cleaned, renamed, type-cast data |
    | Data Warehouse | `dw_pao_de_mel.ft_sales` | Denormalized for final analysis/model input |
    
    ---
    
    ### 🔧 Tools Used
    
    - **Google BigQuery:** Cloud data warehouse for storage and transformation
    - **SQL:** Transformation logic (manual scripts)

---

- ## 📈 4. Data Modeling and Result Analysis & Insights
    - **1st Phase – Exploratory Data Analysis Results and Insights**
        
        ### Results
        
        Infrastructure
        
        <p align="center">
  <img src="https://file.notion.so/f/f/25d1ad8a-548b-4fb9-8ddd-53498eff1b84/40f8f977-f48e-4f07-8a62-19724182cc82/image.png?table=block&id=1d82330f-c8a0-80c6-ac43-ebefd8aa59af&spaceId=25d1ad8a-548b-4fb9-8ddd-53498eff1b84&expirationTimestamp=1745006400000&signature=Xf4sw9PmP9M_qPvq4vBsHJ2I3DnXsihupPnuCC8Yecg&downloadName=image.png" alt="Cloud Infrastructure Developed" width="500">
      </p>
        
    Data Available at [Consumer Behaviour - Analysis](https://docs.google.com/spreadsheets/d/1c7izA-L0si7hTepHywMNrGhG23hGJ2lHqjtJ5hIs_Uk/edit?usp=sharing)
        
    ### **Insights**
        
    - **Snacks** is the Top 1 department in total sales, with 866k products sold in the period of the analysis.
    - **Snacks, Beverages, Frozen Products** and **Pantry** represent, together, over 55% of total sales.
        
        <p align="center">
  <img src="https://file.notion.so/f/f/25d1ad8a-548b-4fb9-8ddd-53498eff1b84/207c7e6f-e99d-420b-a863-5b129748b2fd/image.png?table=block&id=1d82330f-c8a0-8033-808a-fc2bfbff1eae&spaceId=25d1ad8a-548b-4fb9-8ddd-53498eff1b84&expirationTimestamp=1745006400000&signature=CElsDPvYcX8I0KqWGjTNmFpTt3NgVYJbRZ_QLBmo5QU&downloadName=image.png" alt="Top Departments" width="500">
      </p>
        
    - For Top Departments, the **Top 5 Products** by Department are:
        
        <p align="center">
  <img src="https://file.notion.so/f/f/25d1ad8a-548b-4fb9-8ddd-53498eff1b84/450e3063-2657-448e-84f3-924279a7fb53/image.png?table=block&id=1d82330f-c8a0-8038-9593-c392d59a7089&spaceId=25d1ad8a-548b-4fb9-8ddd-53498eff1b84&expirationTimestamp=1745006400000&signature=xaGJIfjTf9WJlKJo8X0AhNTVAwOKwtEzZEkKdfGOAuA&downloadName=image.png" alt="Top Departments" width="500">
      </p>
        
    - **Sales Distribution**
        
    The first two days of the week account for 35% of total weekly sales, with **daily sales on these days being 40% higher than the average of other days**. 
        
  
  <img src="https://file.notion.so/f/f/25d1ad8a-548b-4fb9-8ddd-53498eff1b84/7888adff-8e8e-4c4d-a1f2-2b27690d3f07/image.png?table=block&id=1d82330f-c8a0-809e-abc5-dc854bace589&spaceId=25d1ad8a-548b-4fb9-8ddd-53498eff1b84&expirationTimestamp=1745006400000&signature=AGLJaJ_FqNHSiEuoDk66B9EHeG5bl7Hr2cAe0exxf0I&downloadName=image.png" alt="Top Departments" width="500">
      </p>
        
    Intraday analysis: 82% of total daily sales occur between 8:00 AM and 6:00 PM
        
  <img src="https://file.notion.so/f/f/25d1ad8a-548b-4fb9-8ddd-53498eff1b84/16c54f2b-9ac1-424c-8f73-9547b6cec173/image.png?table=block&id=1d82330f-c8a0-809c-a3bc-ca0d560e7635&spaceId=25d1ad8a-548b-4fb9-8ddd-53498eff1b84&expirationTimestamp=1745006400000&signature=o5VCqc0qw63IFqzK4xMWpIjpyjm2iXW--N5uY-gvNPc&downloadName=image.png" alt="Top Departments" width="500">
      </p>
        
    The **Alcohol department** (1st row) shows a unique sales pattern, with 35% of its sales occurring **on days 4 and 5 of the week**—higher than any other department.
        
  <img src="https://file.notion.so/f/f/25d1ad8a-548b-4fb9-8ddd-53498eff1b84/e04a5115-8ace-44ed-ae63-5e3fab35dc89/image.png?table=block&id=1d82330f-c8a0-8083-a517-c9c82c7b0b5a&spaceId=25d1ad8a-548b-4fb9-8ddd-53498eff1b84&expirationTimestamp=1745006400000&signature=DqekFgxiPWlTVDup4IvYriJ7Vp4KLpy-1Sg_PZrieM4&downloadName=image.png" alt="Top Departments" width="500">
      </p>
        
    - **2nd Phase – Product Recommendation - Apriori Algorythm (Association Rule)**
        - Knime Flow Developed and SQL Query for obtaining databases:
        
        ```sql
        -- Base User-Product
        SELECT DISTINCT
            product_id
            , product_name
        
        FROM `curated_sales.products`;
        
        -- Base Products
        SELECT DISTINCT
            user_id
          , product_id
        
        FROM `dw_pao_de_mel.ft_sales`
        ```
        
        <img src="https://file.notion.so/f/f/25d1ad8a-548b-4fb9-8ddd-53498eff1b84/a3c853db-0d1f-44cf-9e18-266f688b8d59/image.png?table=block&id=1d92330f-c8a0-807e-b098-d74b5751e1c8&spaceId=25d1ad8a-548b-4fb9-8ddd-53498eff1b84&expirationTimestamp=1745006400000&signature=IEDqZA0hUxFGKgaUud-OTUl8ThX8JBYLWFqC09Ordy4&downloadName=image.png" alt="Knime Flow" width="650">
      </p>
        
        - **Results (sample)**
        
        <img src="https://file.notion.so/f/f/25d1ad8a-548b-4fb9-8ddd-53498eff1b84/582974d0-b326-426b-85fe-0bacdb4fd890/image.png?table=block&id=1d92330f-c8a0-8048-b501-d98a5bdd22e0&spaceId=25d1ad8a-548b-4fb9-8ddd-53498eff1b84&expirationTimestamp=1745006400000&signature=0Npbli2A0p57rbM1GC9RCY2SoxxquIcEtViuH2pi2xA&downloadName=image.png" alt="Association Results" width="650">
      </p>
        
        - **Result Analysis & Insights**
            - **Sparkling Water** buyers tend to experiment with multiple flavors of the product. Many customers purchase two or more varieties, with up to a 32% probability of buying a second flavor after purchasing the first. We could try making promotional offers like "Buy 2, Get 1 Free" or "50% off second item."
            - Analysis shows that **bean buyer**s often purchase multiple types of legumes. Specifically, customers who buy beans have a 20% probability of also purchasing garbanzo beans or hummus. This presents an opportunity to create bundle promotions for these products.
            - Analysis revealed a 30% confidence (probability) that customers who purchase Lean Ground Beef also buy Boneless Skinless Chicken Breasts. This pattern suggests these customers may follow specific diets for muscle gain and fat loss. We could create targeted offers for this health-conscious segment.
            - A relationship between low-calorie beverages and healthy snacks has been identified. Specifically, **52% of customers who purchased Zero Calorie Soda also bought Fruit & Nut Food Bars**, while **37% of Trail Mix buyers also purchased Soda**.
