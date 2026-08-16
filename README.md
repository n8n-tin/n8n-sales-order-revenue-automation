# Sales Order Processing & Revenue Reporting Automation

---
## Business Problem

Sales order data often needs to be processed and distributed to different departments for fulfillment, financial reporting, and management review.

When these processes are handled manually, teams may spend significant time separating orders, identifying delivered transactions, consolidating regional results, preparing reports, and sending information to the appropriate stakeholders.

This can lead to:

* Manual data processing
* Delays in order fulfillment
* Repetitive reporting tasks
* Inconsistent calculations or formatting
* Increased risk of human error
* Delayed revenue visibility for Finance and Management
  
---
## Solution

I built an automated sales order processing and reporting workflow using **n8n**.

The workflow retrieves sales data, separates individual orders, calculates order totals, and routes the information through different business processes.

The workflow automatically:

1. Retrieves sales data through an HTTP request.
2. Splits the sales data into individual orders.
3. Calculates order totals.
4. Sends order information to Operations for fulfillment.
5. Identifies delivered orders.
6. Filters out non-delivered orders to ensure only completed deliveries are processed further.
7. **Branches the filtered delivered orders into Yes and No paths for downstream processing.**
8. Groups delivered orders by region.
9. Sends regional revenue analysis to Finance.
10. Generates a CSV report for Management's weekly review.

---
## Process Map

```text
                         Sales Order Data
                                ↓
                        Manual Trigger
                                ↓
                   HTTP — Get Sales Data
                                ↓
                         Split Orders
                                ↓
                      Set Order Totals
                                ↓
                 ┌──────────────┴──────────────┐
                 ↓                             ↓
        OPERATIONS BRANCH              DELIVERY ANALYSIS
                 ↓                             ↓
        Aggregate Orders              Filter Delivered?
                 ↓                         /        \
      Send Orders to Operations          YES        NO
          for Fulfillment                 ↓          ↓
                                  Summarize by     Ignore
                                     Region       Not Delivered
                                         ↓
                              ┌─────────────────────┐
                              │ Regional Metrics:   │
                              │ • Order Totals      │
                              │ • Order Count       │
                              │ • Average Order     │
                              │   Total             │
                              └─────────────────────┘
                                         ↓
                                  Update Field Name
                                         ↓
                              ┌──────────┴──────────┐
                              ↓                     ↓
                       FINANCE BRANCH       MANAGEMENT BRANCH
                              ↓                     ↓
                      Aggregate Regions    Set Report Metadata
                              ↓                     ↓
                    Send Analysis to       Convert to CSV
                         Finance                  ↓
                                           Send Report to
                                            Management
```

---
### Process Summary

The workflow retrieves sales order data, separates the orders, and prepares the order totals. The processed orders then support two business processes.

**Operations:**
Orders are aggregated and sent to the Operations department for fulfillment.

**Delivery & Reporting:**
The workflow checks whether each order has been delivered. Orders that meet the delivered condition continue to **Summarize by Region**, while orders that do not meet the condition are routed to **Ignore Not Delivered**.

Delivered orders are summarized by region using three key metrics:

* **Order Totals** — sum of order values
* **Order Count** — number of delivered orders
* **Average Order Total** — average order value

The resulting fields are standardized by **Update Field Name** before the regional data is used for Finance and Management reporting.

The Finance branch aggregates the regional results and sends the analysis to Finance. The Management branch adds a report generation timestamp, converts the results to CSV, and sends the report to Management for weekly review.

---
## How the Workflow Works

### 1. Manual Trigger

The workflow starts with a manual trigger, allowing the sales order processing and reporting workflow to be executed on demand.

### 2. HTTP Request — Get Sales Data

The workflow retrieves sales order data from an external API or data source through an HTTP request.

The sales data becomes the input for the downstream order processing and reporting steps.

### 3. Split Orders

The incoming sales dataset is separated into individual order records so that each order can be processed independently.

### 4. Set Order Totals

The workflow calculates or sets the total value for each individual order and prepares the order records for downstream processing.

### 5. Split into Operations and Reporting Processes

After the order totals are prepared, the workflow branches into two separate business processes.

#### Branch 1 — Operations / Fulfillment

**Aggregate Orders**

The individual order records are consolidated into a structured dataset containing the processed orders.

**Send Orders to Operations**

The consolidated order information is sent to the Operations department or fulfillment system for order processing and fulfillment.

#### Branch 2 — Delivery Status & Revenue Reporting

**Filter Delivered**

The workflow checks the delivery status of each order.

* **Yes:** The delivered order continues to regional revenue analysis.
* **No:** The order is routed to **Ignore Not Delivered** and does not continue to the revenue reporting process.

**Ignore Not Delivered**

Orders that do not meet the delivered condition are routed here and excluded from the regional revenue analysis.

**Summarize by Region**

Delivered orders are grouped by region and key performance metrics are calculated for each region:

* **Order Totals** — sum of order totals
* **Order Count** — number of delivered orders
* **Average Order Total** — average order total

This transforms individual delivered orders into regional performance data.

**Update Field Name**

The aggregated fields are renamed to clear, business-friendly names for downstream reporting:

* `sum_order_total` → `order_totals`
* `count_order_total` → `order_count`
* `average_order_total` → `average_order_total`

This makes the output easier to interpret and use in reports.

### 6. Split into Finance and Management Reporting

After the regional metrics are prepared and the field names are standardized, the results are distributed into two reporting processes.

#### Branch 2A — Finance

**Aggregate Regions**

The regional results are consolidated into a structured dataset containing the regional revenue and order performance metrics.

**Send Analysis to Finance**

The completed regional analysis is sent to the Finance department for revenue reporting and financial review.

#### Branch 2B — Management

**Set Report Metadata**

The workflow adds a `report_generated` field containing the date and time when the report was generated.

The timestamp uses the format:

`yyyy-MM-dd HH:mm:ss`

This provides a clear generation timestamp for the management report.

**Convert to CSV**

The prepared regional reporting data is converted into CSV format, making it easy to distribute and review in spreadsheet applications.

**Send Report to Management**

The completed CSV report is sent to Management for weekly review and business performance monitoring.

## Overall Workflow

The workflow transforms raw sales data into three useful business outputs:

**1. Operations:**
Processed orders → Fulfillment

**2. Finance:**
Delivered orders → Regional metrics → Revenue analysis

**3. Management:**
Regional metrics → Timestamped CSV report → Weekly review

The overall process demonstrates how a single sales dataset can be automatically transformed, analyzed, and routed to different departments according to their specific business requirements.

---
## AI Capabilities

This workflow is primarily a **business process and data automation workflow rather than an AI workflow**.

However, it can be extended with AI capabilities such as:

* AI-generated revenue summaries
* Automated explanations of regional performance
* Anomaly detection in sales data
* Natural-language management reports
* AI-generated weekly business insights
* Automated identification of unusual order patterns

For example, an AI step could analyze the regional revenue results and generate:

> "The South region generated the highest delivered-order revenue this week, while the North region experienced a decline compared with the previous reporting period."

This would turn the workflow from a reporting automation into an **AI-assisted business intelligence workflow**.

---
## Error Handling

The workflow can be designed to handle common automation failures, including:

* Invalid or unavailable API responses
* Missing sales order data
* Incomplete order information
* Unexpected delivery status values
* CSV conversion issues
* Email delivery failures

Potential improvements include adding dedicated error branches, validation steps, and failure notifications to alert the appropriate team when processing fails.

---
## 📸 Workflow Screenshots

The screenshots below show the complete n8n automation, from sales data retrieval and order processing to Operations fulfillment, Finance analysis, and Management reporting.

All screenshots are stored in:

`docs/screenshots/`


---
### 1. Workflow Overview

**Screenshot path:**

`docs/screenshots/01-workflow-overview.png`

![Workflow Overview](https://github.com/n8n-tin/n8n-sales-order-revenue-automation/blob/main/docs/screenshots/01%20%E2%80%94%20workflow-overview.png)

The complete workflow showing the main processing flow and the branches for Operations, Finance, and Management reporting.


---
### 2. Data Retrieval & Order Processing

**Screenshot path:**

`docs/screenshots/02-data-retrieval-and-order-processing.png`

![Data Retrieval and Order Processing](https://github.com/n8n-tin/n8n-sales-order-revenue-automation/blob/main/docs/screenshots/02%20%E2%80%94%20data-retrieval-and-order-processing.png)

This section shows:

* Manual Trigger
* HTTP Request — Get Sales Data
* Split Orders
* Set Order Totals

The workflow retrieves the sales data, separates individual orders, and prepares the order totals for downstream processing.


---
### 3. Operations Fulfillment

**Screenshot path:**

`docs/screenshots/03-operations-fulfillment.png`

![Operations Fulfillment](https://github.com/n8n-tin/n8n-sales-order-revenue-automation/blob/main/docs/screenshots/03%20%E2%80%94%20operations-fulfillment.png)


This branch consolidates the processed orders and sends them to Operations for fulfillment.

**Nodes included:**

* Aggregate Orders
* Send Orders to Operations


---
### 4. Regional Revenue Analysis

**Screenshot path:**

`docs/screenshots/04-regional-revenue-analysis.png`

![Regional Revenue Analysis](https://github.com/n8n-tin/n8n-sales-order-revenue-automation/blob/main/docs/screenshots/04%20%E2%80%94%20regional-revenue-analysis.png)

This branch removes non-delivered orders and analyzes delivered orders by region.

**Nodes included:**

* Ignore Not Delivered
* Summarize by Region
* Update Field Name

The regional analysis calculates:

* **Order Totals** — sum of order values
* **Order Count** — number of delivered orders
* **Average Order Total** — average order value

The aggregated fields are then renamed to clear, business-friendly names for downstream reporting.


---
### 5. Finance Reporting

**Screenshot path:**

`docs/screenshots/05-finance-reporting.png`

![Finance Reporting](https://github.com/n8n-tin/n8n-sales-order-revenue-automation/blob/main/docs/screenshots/05%20%E2%80%94%20finance-reporting.png)

The regional results are consolidated and sent to Finance for revenue reporting and analysis.

**Nodes included:**

* Aggregate Regions
* Send Analysis to Finance


---
### 6. Management Reporting

**Screenshot path:**

`docs/screenshots/06-management-reporting.png`

![Management Reporting](https://github.com/n8n-tin/n8n-sales-order-revenue-automation/blob/main/docs/screenshots/06%20%E2%80%94%20management-reporting.png)

The final reporting branch prepares the data for Management's weekly review.

**Nodes included:**

* Set Report Metadata
* Convert to CSV
* Send Report to Management

The workflow adds a `report_generated` timestamp, converts the results into CSV format, and sends the completed report to Management.


---
### Screenshot Directory

```text
docs/
└── screenshots/
    ├── 01-workflow-overview.png
    ├── 02-data-retrieval-and-order-processing.png
    ├── 03-operations-fulfillment.png
    ├── 04-regional-revenue-analysis.png
    ├── 05-finance-reporting.png
    └── 06-management-reporting.png
```

---
## Testing

The workflow was tested by running the automation with sales order data and verifying that:

* Sales data was successfully retrieved.
* Orders were correctly separated.
* Order totals were processed.
* Orders were routed to the Operations process.
* Delivered orders were correctly identified.
* Non-delivered orders were excluded from reporting.
* Delivered orders were summarized by region.
* Regional results were prepared for Finance.
* The final report was converted into CSV format.
* The management report was prepared for weekly review.

---
## Technology Stack

* **n8n** — Workflow automation
* **HTTP/API** — Data retrieval
* **Data transformation** — Order processing and aggregation
* **Conditional filtering** — Delivered-order identification
* **Branching logic** — Yes/No delivery routing
* **CSV** — Report generation
* **Email/Business communication** — Department reporting
* **AI-ready architecture** — Can be extended with AI analysis and reporting

---
## Business Value

This automation demonstrates how repetitive sales operations and reporting processes can be converted into a structured workflow.

Potential business benefits include:

* Reduced manual data processing
* Faster order handoff to Operations
* More consistent revenue reporting
* Reduced reporting errors
* Faster access to regional revenue information
* Automated weekly management reporting
* Improved visibility across departments

The project demonstrates practical knowledge of **workflow automation, data transformation, branching logic, API integration, aggregation, reporting, and cross-department process automation**.

---
## Future Improvements

Future versions could include:

* Connect directly to a CRM or ERP system.
* Replace the manual trigger with a scheduled workflow.
* Add automated error notifications.
* Add database storage for historical sales data.
* Create a dashboard for regional revenue.
* Add AI-powered revenue analysis.
* Detect unusual sales or delivery patterns.
* Generate natural-language weekly management summaries.
* Connect the workflow to SAP or another ERP system.
* Add automated alerts for high-value or delayed orders.

---
## Project Outcome

This project demonstrates how n8n can be used to automate a complete business process from **data retrieval → order processing → operational fulfillment → financial analysis → management reporting**.

It showcases the ability to translate a business requirement into an automated workflow with multiple departments, data transformations, decision points, and reporting outputs.
