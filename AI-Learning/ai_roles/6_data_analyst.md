# Data Analyst (AI Focus)

## Introduction
The Data Analyst is the storyteller of the business. While Data Engineers build the pipelines to move data, Data Analysts are the ones who actually look at that data to figure out what happened in the past. In an AI-focused company, they evaluate how the newly deployed AI models are impacting the business financially and operationally. They bridge the gap between complex AI outputs and non-technical executives.

## Syllabus (Learning Path)
1.  **Query Languages:** Advanced SQL (Joins, Window Functions, CTEs).
2.  **Spreadsheets:** Advanced Excel / Google Sheets (Pivot Tables, VLOOKUP).
3.  **Data Visualization:** Tableau, PowerBI, Looker, Metabase.
4.  **Basic Programming:** Python (Pandas, Matplotlib) for data cleaning.
5.  **Statistics:** A/B Testing, Statistical Significance, Distributions.
6.  **Business Acumen:** Understanding KPIs (Key Performance Indicators), ROI, Churn Rate.

## Roles and Responsibilities
*   Query massive databases to extract business insights.
*   Build visual dashboards to track the performance of AI features.
*   Design and analyze A/B tests (e.g., "Did Model A generate more sales than Model B?").
*   Communicate complex data trends clearly to the CEO or stakeholders.

## Real-World Example

### Problem Statement
An E-commerce company recently deployed a new AI Chatbot to handle customer support. The Chatbot costs $5,000 a month in API fees. The CEO wants to know: *"Is this AI actually saving us money, or should we turn it off and hire more human agents?"*

### Solution Approach
Use SQL and Data Visualization tools to analyze the historical chat logs and support ticket data, comparing the metrics before and after the AI was deployed.

### The Steps
1.  **Data Extraction:** Write a complex SQL query joining the `chat_logs` table with the `human_support_tickets` table and the `refunds` table.
2.  **Metric Calculation:** Calculate the "Deflection Rate" (how many users talked to the AI and left without needing to escalate to a human agent).
3.  **Cost Analysis:** Calculate the average salary cost of a human agent resolving a ticket vs. the API cost of the AI resolving a ticket.
4.  **A/B Test Verification:** Check the customer satisfaction scores. Ensure that while the AI is saving money, it isn't making customers angrier and causing them to close their accounts.
5.  **Dashboarding:** Build a live Tableau dashboard that the CEO can check every morning, clearly showing the daily Return on Investment (ROI) of the AI chatbot.

### Tech Stack
*   **Database Querying:** SQL (Snowflake / BigQuery)
*   **Data Cleaning:** Python (Pandas)
*   **Visualization:** Tableau / PowerBI

### Algorithm / Architecture
**A/B Testing & Statistical Significance:** While not a machine learning algorithm, Analysts rely heavily on Statistical Hypothesis Testing (like T-Tests or Z-Tests) to mathematically prove that a 5% increase in sales was actually caused by the new AI feature, rather than just random luck.
