
# Bridging Salesforce OLTP to Star Schema for BI

This document describes how to bridge a normalized Salesforce OLTP system with a dimensional star schema used for analytics and business intelligence.

---

## 📊 Key Metrics Enabled (Categorized)

### 🔹 Sales Performance Metrics
- Total Pipeline Value (by Stage, Region, Product, Rep)
- Win Rate (% of Opportunities Closed as Won)
- Number of Closed Deals
- Average Deal Size
- Average Sales Cycle Duration
- Sales Rep Quota Attainment

### 🔹 Forecasting & Revenue Metrics
- Expected Revenue Forecast (Weighted by Probability)
- Revenue Realization vs Forecast
- Stage-to-Stage Conversion Rates
- Forecast Accuracy Over Time

### 🔹 Lead & Campaign Attribution Metrics
- Lead-to-Opportunity Conversion Rate
- Opportunity-to-Close Rate by Lead Source
- Campaign Influence on Revenue
- Marketing ROI per Campaign

### 🔹 Product & Customer Metrics
- Product Penetration by Account
- Cross-Sell & Up-Sell Rate
- Customer Lifetime Value Proxy (by Opportunity Total)
- Opportunity Aging and Stale Pipeline Analysis

### 🔹 Time-Based Metrics
- Opportunities Created vs Closed over Time
- Pipeline Changes by Quarter/Month
- Daily Snapshot of Sales Funnel by Stage

---

## 🧩 Detailed Star Schema (Mermaid)

```mermaid
erDiagram

    FactOpportunity ||--o{ DimAccount : "account_id"
    FactOpportunity ||--o{ DimContact : "primary_contact_id"
    FactOpportunity ||--o{ DimUser : "owner_user_id"
    FactOpportunity ||--o{ DimOpportunityStage : "stage_id"
    FactOpportunity ||--o{ DimLeadSource : "lead_source_id"
    FactOpportunity ||--o{ DimCampaign : "campaign_id"
    FactOpportunity ||--o{ DimTime : "created_date_id"
    FactOpportunity ||--o{ DimTime : "close_date_id"

    BridgeOpportunityProduct }o--|| FactOpportunity : "opportunity_id"
    BridgeOpportunityProduct }o--|| DimProduct : "product_id"

    DimAccount {
        string account_id PK
        string account_name
        string industry
        string region
        string account_type
        date created_date
    }

    DimContact {
        string contact_id PK
        string first_name
        string last_name
        string email
        string phone
        string account_id FK
    }

    DimUser {
        string user_id PK
        string full_name
        string role
        string region
        string team
    }

    DimOpportunityStage {
        string stage_id PK
        string stage_name
        int probability
        string forecast_category
    }

    DimLeadSource {
        string lead_source_id PK
        string lead_source_name
        string category
    }

    DimCampaign {
        string campaign_id PK
        string campaign_name
        string type
        string status
        date start_date
        date end_date
    }

    DimTime {
        string date_id PK
        date full_date
        string day_of_week
        int day
        int month
        int year
        string quarter
    }

    DimProduct {
        string product_id PK
        string product_name
        string product_family
        decimal list_price
        string category
    }

    BridgeOpportunityProduct {
        string opportunity_id FK
        string product_id FK
        int quantity
        decimal total_price
    }

    FactOpportunity {
        string opportunity_id PK
        string account_id FK
        string primary_contact_id FK
        string owner_user_id FK
        string stage_id FK
        string lead_source_id FK
        string campaign_id FK
        string created_date_id FK
        string close_date_id FK
        decimal amount
        decimal expected_revenue
        int is_closed
        int is_won
        string currency
    }
```

---

## 🛠 Tooling Stack

| Component         | Tool Example                      |
|-------------------|-----------------------------------|
| Extraction        | Salesforce Bulk API, Fivetran     |
| Staging           | S3, Azure Blob, Raw DB tables     |
| Data Warehouse    | Snowflake, BigQuery, Synapse      |
| Transformation    | dbt, ADF Data Flow, Databricks    |
| Orchestration     | Airflow, ADF Pipelines            |
| BI & Reporting    | Power BI, Tableau, Looker         |

---

## 🧠 Best Practices

- Use a **date spine** for reliable time-series reporting.
- Apply **SCD Type 2** logic for historical tracking on dimensions.
- Snapshot `Opportunity` records daily or at key milestones.
- Ensure surrogate keys are used in the warehouse for dimensional joins.
- Regularly validate row counts and data freshness between OLTP and warehouse layers.

---

This model supports scalable, trustworthy analytics for forecasting, sales optimization, marketing attribution, and customer behavior tracking.
