# Customer Support Performance Dashboard (Tableau)
A Tableau project that creates meaningful visualizations to analyse of a fabricated e-commerce company's (Shopzilla's) customer support performance.

![Dashboard Overview](https://github.com/user-attachments/assets/47fc427a-2f1e-4544-b0a1-3825558b3f1c)

---


## 1. Project Overview

**Goal:** To practice end-to-end analytics such as data cleaning, calculated fields, and dashboard design, in Tableau using an e-commerce customer support dataset. This is done through monitoring the ticket volume, response speed, and customer satisfaction across channels in Tableau.

**Tools used:** Tableau, Excel

**Key performance metrics:** Total Tickets, Avg Response Time (hours), Avg CSAT Score (1-5), and % of CSAT ≥ 4

---

## 2. Dataset

The data used in this project comes from the Kaggle dataset [**"eCommerce Customer Service Satisfaction" by ddosad**](https://www.kaggle.com/datasets/ddosad/ecommerce-customer-service-satisfaction/data)
- **Time period:** July-August 2023
- **Rows / columns:** 85907 rows by 20 columns
- **Important fields:**
  - `Issue reported at` - timestamp when ticket was issued by customer
  - `Issue Responded` - timestamp when first response was sent by support team
  - `Channel Name` - Email / Inbound / Outcall
  - `CSAT Score` - customer satisfaction level (1-5); Bad: 1-3, Good: 4-5

 ---

 ## 3. Business Questions
 
 1. **Volume**
    - How many tickets do we receive over time?
    - Which channels generate the most tickets?

2. **Speed**
   - What is our average response time, and how does it change over time?
   - Which channels are fastest/slowest to respond?

3. **Satisfaction**
   - What is the average CSAT score over time?
   - How does satisfaction vary by channel?

4. **Relationships**
   - Does ticket volume impact response time?
   - Is there a relationship between response time and CSAT?

---

## 4. Data Preparation

- **Hiding unnecessary fields**
  - Hiding fields that were mostly/fully null values:
    - `connected_handling_time`
    - `Customer_City`
    - `Product_category`
    - `Item_price`
    - `order_date_time`
    - `Customer Remarks`

- **Handling dates**
  - `Issue Responded` was originally misinterpreted as MM/DD/YYYY in Tableau while CSV used DD/MM/YYYY
  - Fixed by recalculated a corrected datetime field and using it in all response time calculations
   
- **Calculated fields**
  - `Response Time (Hours)`
    ```tableau
    DATEDIFF('minute', [Issue reported at], [Issue Responded (Fixed)]) / 60
    ```
  - `Positive CSAT Scores (%)`
    ```tableau
    SUM(IIF([CSAT Score] >= 4, 1, 0)) / COUNT([CSAT Score])
    ```  
  - `(+/-) Response Time - Diff from Overall Avg (Hours)`
    Used to colour days red/green and show how far a day is above or below the overall average
    ```tableau
    AVG([Response Time (Hours)]) - WINDOW_AVG(AVG([Response Time (Hours)]))
    ```
  - `(+/-) CSAT - Diff from Overall Avg`
    Used to colour days green/red and show how far a day is above or below the overall average
    ```tableau
    AVG([CSAT Score]) - WINDOW_AVG(AVG([CSAT Score]))
    ```

---

## 5. Dashboard Design

### 5.1 KPIs (top row)
- **Total Tickets** – total number of tickets in the selected period
- **Avg Response Time (Hours)** – average first response time
- **Avg CSAT Score** – average satisfaction on a 1–5 scale
- **Positive CSAT Scores (%)** – share of responses with CSAT ≥ 4

### 5.2 Over Time
- **Tickets Over Time** – daily ticket volumes
- **Response Time Over Time** – average response time per day with an overall average reference line and coloured diff vs average in tooltip
- **CSAT Over Time** – average CSAT per day with an overall average reference line and coloured diff vs average in tooltip

### 5.3 By Channel
- **Tickets by Channel (All)** – total workload by Email / Inbound / Outcall
- **Avg Response Time by Channel (Hours)** – bar chart comparing speed by channel (can be for the whole period or selected date)
- **Avg CSAT Score by Channel (All)** – bar chart comparing satisfaction by channel

### 5.4 Relationship
- **Avg Response Time vs Avg CSAT Score (by Channel)** – scatter plot where each point is a day–channel combination, coloured by channel and sized by ticket count. Trend lines show how CSAT changes with slower responses
- **Tickets Over Time by Channel** – multi-line chart to see how each channel’s volume evolves over time

### 5.5 Interactivity
- Hover actions that show “Tickets by Channel on \<date>” or channel breakdowns in tooltips
- Channel filters

---

## 6. Key Insights

**Volume**
- The support team handled **85,903 tickets** between 28 Jul 2023 and 31 Aug 2023
- Inbound is the dominant channel, accounting for around **~79.3%** of all tickets, followed by Outcall **(~17.2%)** and Email **(~0.04%)**
- Ticket volume peaks around weekdays and the end of the month, which may require higher staffing levels on those days

**Speed**
- Overall average response time is **~2.8 hours**, but early days in late July show much slower responses (up to ~70+ hours)
- After August 1st, response times stabilise around **0.6-2.7 hours**
- Email is consistently the **slowest** channel at **~2.4 hours** on average (After ignoring outliers > 48 hours) and has the highest median at **~0.12**
- Inbound and Outcall are consistently the fastest channels

**Satisfaction**
- The average CSAT score is **4.2 / 5**, with **~82%** of responses rated 4 or 5
- Outcall and Inbound have higher CSAT (~4.3) compared to Email (~3.9)
- CSAT dips significantly on **29 Jul 2023**, driven largely by Outcall and Inbound

**Relationship between speed and satisfaction**

After filtering outliers (>48 hours),
- The scatter plot suggests that for **Email**, longer response times are associated with noticeably lower CSAT (steep negative trend line)
- Inbound is relatively flat but still a negative trend, also suggesting association between longer response times with lower CSAT but at a lower degree
- Outcall appears more tolerant of slower responses (positive trend)
- Overall, extremely long response times correlate with lower satisfaction

**Relationship between volume and speed**
- There seems to be no noticeable relationship between ticket volume and response time

---

## 7. Limitations & Next Steps

- CSAT responses exist only for a subset of tickets; results may be biased towards customers who chose to respond
- Response time analysis focuses on **first response**, not full resolution time
- Early July outliers in response time likely reflect a backlog or data quality issue
- Future work:
  - Include **issue type** to see which problems drive low CSAT
  - Add **team/agent** performance breakdowns
  - Connect directly to a database and refresh data on a schedule instead of using a fabricated dataset

---

## 8. How to View / Reproduce

- **Tableau Public link:**  
  [Shopzilla eCommerce Customer Service Satisfaction](https://public.tableau.com/app/profile/bao.jie.char/viz/ShopzillaeCommerceCustomerServiceSatisfaction/ExecutiveOverview)

- **Files in this repo:**
  - ["eCommerce Customer Service Satisfaction" by ddosad](https://www.kaggle.com/datasets/ddosad/ecommerce-customer-service-satisfaction/data) – source dataset  
  - `Support_Dashboard.twbx` – Tableau packaged workbook (optional)    
  - `README.md` – this document
