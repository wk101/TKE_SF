# 📊 Metrics README

This document outlines the full set of **business and operational metrics** modeled across the enterprise Galaxy Schema for TKE.

Metrics are grouped by domain and tied to their respective **fact tables** (OLAP layer), enabling robust analytics and executive reporting. Each metric is classified by **type**:
- **Scalar** – A single aggregated value (e.g., total revenue)
- **Vector** – Aggregated by one dimension (e.g., margin by region)
- **Matrix** – Aggregated by two dimensions (e.g., cost by department and time)

These metrics serve multiple business functions:
- Sales performance tracking
- Marketing attribution and ROI
- Customer service optimization
- Financial reconciliation (AR, cost centers)
- Management accounting and allocation analysis
- Cross-system analytics (Salesforce + SAP)

This document complements the **OLAP diagram** and supports BI layer implementation (Power BI, Tableau, etc.).


## 1. Sales & Pipeline Metrics (from Salesforce FactOpportunity)
| Metric                        | Type   | Description                                   |
|-------------------------------|--------|-----------------------------------------------|
| Total Pipeline Value          | Scalar | Sum of open opportunity Amount               |
| Expected Revenue              | Vector | Weighted by probability                       |
| Win Rate by Region            | Vector | Won / (Won + Lost), grouped by Region         |
| Deal Conversion Matrix        | Matrix | Lead Source × Stage transition counts         |
| Sales Cycle Duration          | Scalar | Avg days from open to close                   |
| Avg Deal Size                 | Scalar | Avg Amount for closed-won                     |
| Pipeline Coverage Ratio       | Scalar | Pipeline / Quota                              |
| Opportunity Aging             | Vector | Days open per opportunity                     |
| Lost Deal Reasons             | Vector | Count by LostReason field                     |

## 2. Quote & Order Metrics (FactQuote, FactOrder)
| Metric                         | Type   | Description                                     |
|--------------------------------|--------|-------------------------------------------------|
| Average Discount               | Vector | % discount from list price                      |
| Quote-to-Order Conversion Rate | Scalar | Quotes converted to orders                      |
| Order Volume by Product        | Vector | Quantity or count by product                    |
| Fulfillment Lag                | Scalar | Days between order and delivery                 |

## 3. Marketing Attribution Metrics (FactCampaignResponse)
| Metric                     | Type   | Description                                          |
|----------------------------|--------|------------------------------------------------------|
| Campaign Conversion Rate   | Vector | Responses / Total Reach                              |
| Attributed Revenue         | Vector | SFDC opportunity revenue linked to campaign          |
| Channel Effectiveness      | Matrix | Revenue or leads per channel × region                |
| Cost per Lead              | Vector | Campaign Spend / Leads generated                     |
| Marketing ROI              | Scalar | (Revenue - Spend) / Spend                            |

## 4. Customer Service & Asset Metrics (FactCase, FactAsset)
| Metric                         | Type    | Description                                 |
|--------------------------------|---------|---------------------------------------------|
| Average Resolution Time        | Vector  | Case closure duration by region             |
| First Contact Resolution Rate  | Scalar  | % resolved on first contact                 |
| Active Installed Base          | Vector  | Count of current assets in field            |
| Maintenance Cost per Asset     | Vector  | Service cost aggregated by asset ID         |
| Case Volume by Type and Status | Matrix  | For backlog, SLA tracking                   |

## 5. Rep Activity & Engagement Metrics (FactActivity)
| Metric                        | Type   | Description                                |
|-------------------------------|--------|--------------------------------------------|
| Calls/Emails per Rep per Day  | Matrix | Activity type × day × rep                  |
| Follow-up Rate                | Scalar | % of leads followed up within X days       |
| Engagement Velocity           | Vector | Avg time between touches                   |
| Open Tasks Aging              | Vector | Days since assignment                      |

## 6. Finance & Billing Metrics (FactInvoice, FactPayment)
| Metric                     | Type   | Description                                  |
|----------------------------|--------|----------------------------------------------|
| Total Invoiced Amount      | Scalar | Sum of InvoiceAmount                         |
| AR Aging Buckets           | Vector | 0–30, 31–60, 61–90, >90 days                 |
| Collection Rate            | Vector | Paid / Invoiced by period                    |
| DSO (Days Sales Outstanding)| Scalar| AR ÷ Avg Daily Revenue                       |
| Invoice-to-Cash Cycle      | Scalar | Days from invoice to payment                 |

## 7. Management Accounting Metrics (FactCost, FactRevenue, FactAllocation)
| Metric                        | Type         | Description                                    |
|-------------------------------|--------------|------------------------------------------------|
| Gross Margin                  | Scalar/Vector| Revenue - Direct Cost (by Product/Region)      |
| Contribution Margin           | Vector       | Revenue - Variable Cost                        |
| Budget vs Actual              | Matrix       | Time × Account × Cost Center                   |
| Fixed vs Variable Cost Ratio  | Scalar       | % of each cost type                            |
| Cost per Unit Installed       | Scalar       | Total cost / Installations                     |
| Operating Expense by Department| Matrix      | Department × Period                            |
| Vendor Spend Analysis         | Vector       | Total spend by Vendor Category                 |
| Inter-Cost Center Allocations | Matrix       | From → To amount allocations                   |
| CAPEX by Region               | Vector       | Total capital expenditure per region           |

## 8. Cross-System Metrics (Salesforce + SAP)
| Metric                           | Type   | Description                                    |
|----------------------------------|--------|------------------------------------------------|
| Forecasted Revenue vs Actual Cost| Vector | SFDC pipeline vs SAP GL                        |
| Budget Variance                  | Matrix | Budgeted vs Actual by region/product           |
| Profitability by Customer/Region | Vector | SFDC Revenue - SAP Cost                        |
| YOY Growth (Revenue/Cost)        | Vector | Year-over-year trend line                      |

# 📈 OLAP Metrics Diagram – Galaxy Schema Overview

This diagram represents the OLAP layer for a **Galaxy Schema** architecture designed for TKE, integrating metrics across **Salesforce**, **SAP**, and other enterprise systems. 

Each fact table (e.g., `FactOpportunity`, `FactCost`, `FactInvoice`) is modeled as a **star schema**, and the constellation of these facts forms a **Galaxy Schema**.

---

## 🌀 Galaxy Architecture Notes

- **Fact Tables:** Represent core business processes (e.g., Sales Pipeline, Campaign Response, Invoicing, Cost Accounting).
- **Shared Dimensions:** `DimTime`, `DimRegion`, `DimProduct`, `DimAccount`, and `DimCurrency` serve as **wormholes**, enabling cross-fact analysis.
- **Metrics:** Grouped by business domain, classified as scalar, vector, or matrix.
- **Integration Points:**
  - Salesforce ↔ SAP revenue/cost alignment
  - Marketing ↔ Opportunity influence tracking
  - Finance ↔ Cost center reporting and allocations

---

## 🧭 Example Wormholes (Shared Dimensions)

| Wormhole Dimension | Used In                                      |
|---------------------|----------------------------------------------|
| `DimTime`           | All fact tables — time alignment              |
| `DimProduct`        | Opportunity, Quote, Order, Revenue, Cost      |
| `DimRegion`         | Opportunity, Campaign, Cost, Revenue          |
| `DimAccount`        | CRM (Sales), Invoicing, Costing               |
| `DimCurrency`       | Finance, Revenue, Cost — for FX normalization|

---

## 🔧 Usage

The following Mermaid diagram maps each **metric** to its source **fact table**, forming a visual OLAP layer to support:
- Executive dashboards
- Departmental KPIs
- Financial consolidation
- Cross-system audits and reconciliations

Continue below for the visual structure.


```mermaid
graph TD

%% Metrics grouped by fact tables
%% Sales & Pipeline
FactOpportunity["FactOpportunity (Sales & Pipeline)"]
FactOpportunity --> M1[Total Pipeline Value]
FactOpportunity --> M2[Expected Revenue]
FactOpportunity --> M3[Win Rate by Region]
FactOpportunity --> M4[Deal Conversion Matrix]
FactOpportunity --> M5[Sales Cycle Duration]
FactOpportunity --> M6[Avg Deal Size]
FactOpportunity --> M7[Pipeline Coverage Ratio]
FactOpportunity --> M8[Opportunity Aging]
FactOpportunity --> M9[Lost Deal Reasons]

%% Quote & Order
FactQuote["FactQuote"]
FactOrder["FactOrder"]
FactQuote --> M10[Average Discount]
FactQuote --> M11[Quote-to-Order Conversion Rate]
FactOrder --> M12[Order Volume by Product]
FactOrder --> M13[Fulfillment Lag]

%% Marketing
FactCampaignResponse["FactCampaignResponse"]
FactCampaignResponse --> M14[Campaign Conversion Rate]
FactCampaignResponse --> M15[Attributed Revenue]
FactCampaignResponse --> M16[Channel Effectiveness]
FactCampaignResponse --> M17[Cost per Lead]
FactCampaignResponse --> M18[Marketing ROI]

%% Service & Asset
FactCase["FactCase"]
FactAsset["FactAsset"]
FactCase --> M19[Average Resolution Time]
FactCase --> M20[First Contact Resolution Rate]
FactAsset --> M21[Active Installed Base]
FactAsset --> M22[Maintenance Cost per Asset]
FactCase --> M23[Case Volume by Type and Status]

%% Rep Activity
FactActivity["FactActivity"]
FactActivity --> M24[Calls/Emails per Rep per Day]
FactActivity --> M25[Follow-up Rate]
FactActivity --> M26[Engagement Velocity]
FactActivity --> M27[Open Tasks Aging]

%% Finance
FactInvoice["FactInvoice"]
FactPayment["FactPayment"]
FactInvoice --> M28[Total Invoiced Amount]
FactInvoice --> M29[AR Aging Buckets]
FactPayment --> M30[Collection Rate]
FactInvoice --> M31[DSO]
FactPayment --> M32[Invoice-to-Cash Cycle]

%% Management Accounting
FactCost["FactCost"]
FactRevenue["FactRevenue"]
FactAllocation["FactAllocation"]
FactRevenue --> M33[Gross Margin]
FactRevenue --> M34[Contribution Margin]
FactCost --> M35[Budget vs Actual]
FactCost --> M36[Fixed vs Variable Cost Ratio]
FactCost --> M37[Cost per Unit Installed]
FactCost --> M38[Operating Expense by Department]
FactCost --> M39[Vendor Spend Analysis]
FactAllocation --> M40[Inter-Cost Center Allocations]
FactCost --> M41[CAPEX by Region]

%% Cross-System
CrossMetrics["Salesforce + SAP (Cross-System)"]
CrossMetrics --> M42[Forecasted Revenue vs Actual Cost]
CrossMetrics --> M43[Budget Variance]
CrossMetrics --> M44[Profitability by Customer/Region]
CrossMetrics --> M45[YOY Growth]
```

