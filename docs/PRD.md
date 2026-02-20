# Product Requirements Document (PRD)
## Databricks ETL Sales Pipeline

**Project**: Sales & Marketing Data Processing Pipeline
**Target Catalog**: zoltan-verebes-catalog-m
**Target Schema**: dg-day
**Document Version**: 1.0
**Date**: 2026-02-20

---

## 1. High-Level Business Goals & Objectives

### Why This Project?

The organization currently lacks the infrastructure to efficiently process and serve sales and customer data to downstream business teams. This creates operational bottlenecks and missed opportunities for data-driven decision making.

### Expected Outcomes

A successful implementation will deliver:
- **For Sales Teams**: Accurate, aggregated sales data accessible via BI tools to support strategic planning and performance monitoring
- **For Marketing Teams**: Rich customer profiles combined with purchase history to enable targeted marketing campaigns and customer segmentation

### Pain Points Without This Solution

1. **Sales Team**:
   - No centralized source of truth for aggregated sales metrics
   - Manual, error-prone data compilation for reports and analysis
   - Inability to perform daily performance monitoring by product and category
   - Delayed strategic decision-making due to lack of timely insights

2. **Marketing Team**:
   - Cannot execute targeted marketing campaigns due to lack of customer data
   - Manual customer list compilation is time-consuming and unreliable
   - No ability to segment customers by purchase behavior or category preferences
   - External marketing tools remain unutilized due to data unavailability

3. **Overall Organization**:
   - Data scattered across multiple external systems and formats
   - No automated, reliable data processing pipeline
   - Data quality and consistency concerns
   - Security and compliance risks from uncontrolled data access

---

## 2. Data Sources

### 2.1 Source Entities

#### **Entity 1: customers.csv**
- **Description**: Master customer data containing contact information and unique identifiers
- **Key Attributes**: customer_id, name, address, email, phone number
- **Primary Key**: customer_id

#### **Entity 2: sales.csv**
- **Description**: Product catalog with pricing and availability information
- **Key Attributes**: product_id, product_name, category, price, availability status
- **Primary Key**: product_id

#### **Entity 3: sales_orders.csv**
- **Description**: Individual customer orders with fulfillment status and transaction details
- **Key Attributes**: order_id, customer_id, product_id, order_date, quantity, order_status
- **Primary Key**: order_id

### 2.2 Data Source Characteristics

| Characteristic | customers.csv | sales.csv | sales_orders.csv |
|---|---|---|---|
| **Source System** | External system (CRM or ERP) | External system (Product catalog/PIM) | External system (Order Management) |
| **Connection Type** | Manual CSV upload | Manual CSV upload | Manual CSV upload |
| **Data Format** | CSV (flat file) | CSV (flat file) | CSV (flat file) |
| **Ingestion Pattern** | Batch (manual, periodic) | Batch (manual, periodic) | Batch (manual, periodic) |
| **Update Frequency** | Infrequent, unreliable, manual trigger | Infrequent, unreliable, manual trigger | Infrequent, unreliable, manual trigger |
| **Estimated Records** | ~10,000-50,000 customers | ~500-2,000 products | ~100,000-500,000 orders |
| **Estimated Size** | ~5-10 MB | ~1-2 MB | ~50-100 MB |
| **Location** | `/Volumes/zoltan-verebes-catalog-m/dg-day/volume/` | `/Volumes/zoltan-verebes-catalog-m/dg-day/volume/` | `/Volumes/zoltan-verebes-catalog-m/dg-day/volume/` |

### 2.3 Data Update Strategy

- **Triggering**: Manual pipeline execution required (no automatic scheduling initially)
- **Latency Tolerance**: Due to infrequent and unreliable updates from source systems, the pipeline is designed for batch processing with manual triggers
- **Data Reliability**: Source data quality is assumed to be variable; the pipeline must include data validation and cleansing steps

---

## 3. Use Cases

### Use Case 1: Sales Reporting with BI Tools

**Objective**: Enable the sales team to access aggregated sales data for strategic planning and performance monitoring

**Requirements**:
- **Data Access**: Aggregated sales metrics (revenue, order count, quantities) by product and product category
- **Time Dimensions**: Support aggregation at annual, monthly, weekly, and daily levels
- **Data Restrictions**: NO access to PII (customer names, email addresses, phone numbers)
- **Consumer System**: Databricks Dashboards
- **Update Frequency**: Daily (or as frequently as new source data arrives)
- **Performance**: Prompt access to results (sub-second query response times preferred)
- **Users**: Sales team members

**Example Insights Enabled**:
- Daily revenue by product category
- Weekly order trends
- Monthly performance comparisons by product
- Year-over-year sales analysis

---

### Use Case 2: Marketing Team - Direct Marketing Campaign

**Objective**: Provide the marketing team with customer data and purchase history to support targeted email and phone marketing campaigns

**Requirements**:
- **Data Access**: Customer profiles with purchase history and behavioral metrics
- **Customer Information**: Names, email addresses, phone numbers, customer_id
- **Aggregated Metrics**:
  - Number of orders per customer
  - Total amount spent per customer
  - Last purchase date per customer
  - Product category preferences
- **Data Enrichment**: Associate each customer with appropriate timezone (derived from state information) for call center operations
- **Data Filtering**: Exclude customers without a valid email address OR phone number
- **Customer Clustering**: Support segmentation by product category preferences
- **Consumer System**: External automated call center (REST API)
- **Update Frequency**: Weekly
- **Performance**: Moderate (data retrieval within minutes acceptable)
- **Users**: Marketing team and external call center system

**Example Insights Enabled**:
- High-value customer identification (by spend)
- Customer segmentation by product category
- Contact strategy optimization (timezone-aware calling)
- Campaign targeting based on purchase recency

---

## 4. Functional Requirements

### 4.1 Data Ingestion
- **FR-1**: Ingest customer master data from customers.csv into bronze layer
- **FR-2**: Ingest product data from sales.csv into bronze layer
- **FR-3**: Ingest sales order data from sales_orders.csv into bronze layer
- **FR-4**: Validate file format and schema conformance during ingestion
- **FR-5**: Track and maintain ingestion metadata (timestamp, source filename, record counts) for data lineage and audit trail

### 4.2 Data Transformation & Cleansing
- **FR-6**: Deduplicate customer records based on customer_id (if duplicates exist)
- **FR-7**: Standardize and validate customer contact information (email format, phone format)
- **FR-8**: Link sales orders to customers and products via foreign keys
- **FR-9**: Calculate derived metrics: total orders per customer, total spend per customer, last purchase date
- **FR-10**: Classify products into categories and validate category taxonomy
- **FR-11**: Enrich customer records with timezone information based on state/location

### 4.3 Data Quality & Validation
- **FR-12**: Implement data quality checks (null checks, data type validation, referential integrity)
- **FR-13**: Flag or quarantine records that fail quality checks
- **FR-14**: Generate data quality reports for each ingestion run
- **FR-15**: Validate that all marketing customers have at least one contact method (email OR phone)

### 4.4 Sales Reporting Output (Use Case 1)
- **FR-16**: Create aggregated sales metrics table with dimensions: product, category, time (daily/weekly/monthly/annual)
- **FR-17**: Ensure sales data excludes all customer PII
- **FR-18**: Support drill-down capability from annual to daily granularity
- **FR-19**: Include metrics: order count, total revenue, total quantity sold

### 4.5 Marketing Data Output (Use Case 2)
- **FR-20**: Create marketing customer dataset with customer contact information and behavioral metrics
- **FR-21**: Filter out customers without valid email or phone number
- **FR-22**: Include customer metrics: order count, total spend, last purchase date, product category preferences
- **FR-23**: Ensure timezone information is populated for all marketing customers
- **FR-24**: Support customer segmentation by product category

### 4.6 Access Control & Security
- **FR-25**: Implement separate access permissions for sales team (aggregate data only, no PII)
- **FR-26**: Implement separate access permissions for marketing team (full customer profiles)
- **FR-27**: Create distinct tables/views for each use case to enforce data access policies

---

## 5. Non-Functional Requirements & Constraints

### 5.1 Data Quality
- **NFR-1 - Completeness**: All required fields for each entity must be present; missing values must be identified and handled
- **NFR-2 - Accuracy**: Data must be validated against known constraints and business rules
- **NFR-3 - Consistency**: Customer, product, and order references must be mutually consistent (no orphaned records)
- **NFR-4 - Timeliness**: Data freshness aligned with update frequencies (daily for sales, weekly for marketing)

### 5.2 Performance
- **NFR-5 - Sales Reporting Latency**: Query response times for sales dashboards should be sub-second
- **NFR-6 - Marketing Data Retrieval**: Weekly export for marketing system should complete within acceptable time window (exact SLA to be defined in technical design)
- **NFR-7 - Pipeline Execution**: Manual trigger pipelines should complete within 1-2 hours of initiation (scale-dependent, to be refined in technical design)

### 5.3 Availability & Reliability
- **NFR-8 - Data Availability**: Processed data tables must be available for reporting immediately upon pipeline completion
- **NFR-9 - Pipeline Monitoring**: Failed pipeline executions must be identifiable and require manual intervention to resolve

### 5.4 Security & Compliance
- **NFR-10 - Data Access Control**: Role-based access control (RBAC) enforced at table/view level
- **NFR-11 - PII Protection**: Customer PII (names, emails, phone) strictly segregated and only accessible to authorized marketing team
- **NFR-12 - Sales Team Restrictions**: Sales team has no access to customer identities, emails, or phone numbers
- **NFR-13 - Data Retention**: Data retention policies to be defined (retention duration for processed data)

### 5.5 Monitoring & Observability
- **NFR-14 - Pipeline Execution Logs**: All pipeline executions must be logged with start time, end time, status, and record counts
- **NFR-15 - Data Quality Metrics**: Quality metrics must be tracked per entity per run (record counts, error/warning counts, schema validation results)
- **NFR-16 - Alerting**: Mechanism to flag data quality issues or pipeline failures for manual review

### 5.6 Constraints

#### Technical Constraints
- **C-1**: Must use Databricks as the primary platform
- **C-2**: Must integrate with Unity Catalog for data governance and access control
- **C-3**: Target catalog is `zoltan-verebes-catalog-m` and target schema is `dg-day`
- **C-4**: Data sources are currently available in Databricks Unity Catalog Volume: `/Volumes/zoltan-verebes-catalog-m/dg-day/volume/`

#### Operational Constraints
- **C-5**: Pipeline is manually triggered (no automatic scheduling initially)
- **C-6**: Source data updates are infrequent, unreliable, and manual—pipeline robustness must account for data availability variations
- **C-7**: No real-time or streaming processing required (batch processing model acceptable)

---

## 6. Out of Scope

The following items are explicitly excluded from this project:

1. **Downstream System Implementation**: No implementation of or direct integration with downstream consumer systems
   - Databricks Dashboards are assumed to exist or will be created separately
   - External call center system and REST API integration are out of scope

2. **Real-Time Streaming**: No streaming data ingestion; batch processing model only

3. **Automated Scheduling**: Initial implementation assumes manual pipeline triggers; no scheduled/automated execution orchestration

4. **Data Warehousing Best Practices Beyond Medallion**: Only the medallion architecture (bronze → silver → gold) is in scope; advanced optimization or data lakehouse patterns are out of scope

5. **Historical Data Reprocessing**: Only forward-looking processing of new/updated source data; full historical reload is out of scope

6. **Advanced Machine Learning**: No ML model training or inference pipelines; data preparation only

7. **External System Integrations**: No direct connectors to external marketing platforms or call center systems

---

## 7. Open Questions

### Data & Source System Questions
1. **OQ-1**: What is the exact current data volume and growth trajectory for each entity (customers, products, orders)?
2. **OQ-2**: What are the known data quality issues in source systems that the pipeline should be prepared for?
3. **OQ-3**: Will source data continue to arrive via manual CSV uploads, or is there a plan to automate/integrate source systems in the future?
4. **OQ-4**: Are there any historical records that need to be processed, or should the pipeline only process data from a certain point forward?

### Use Case Clarifications
5. **OQ-5**: For sales reporting, are there specific product categories defined, or should they be derived from the sales data?
6. **OQ-6**: For the marketing team, what is the expected format and schema for the REST API export (CSV, JSON, database connection)?
7. **OQ-7**: Are there specific timezone mappings defined for states, or should we use standard timezone mappings?
8. **OQ-8**: Should inactive or deleted customers be retained in the marketing dataset, or should they be filtered out?

### Technical & Operational Questions
9. **OQ-9**: What is the expected timeline for implementing automated scheduling vs. manual triggers in the future?
10. **OQ-10**: Are there specific audit or compliance requirements that the pipeline must meet (e.g., GDPR, data lineage tracking)?
11. **OQ-11**: What is the disaster recovery and backup strategy for the processed data tables?
12. **OQ-12**: Who are the specific stakeholders and what are their roles (data owners, approvers, consumers)?

---

## 8. Approval & Sign-Off

| Role | Name | Date | Signature |
|---|---|---|---|
| Product Owner | TBD | TBD | TBD |
| Data Engineering Lead | TBD | TBD | TBD |
| Sales Team Representative | TBD | TBD | TBD |
| Marketing Team Representative | TBD | TBD | TBD |

---

## Document History

| Version | Date | Author | Changes |
|---|---|---|---|
| 1.0 | 2026-02-20 | Claude Code | Initial PRD based on use-case.md and Phase 1 requirements capture |
