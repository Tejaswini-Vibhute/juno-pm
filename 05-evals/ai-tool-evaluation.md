# AI Tool Evaluation: ChatGPT for SQL & Data Analytics

**AI Tool:** ChatGPT  
**Use Case:** Writing SQL queries, debugging Snowflake errors, and assisting with dbt development.

## 1. Evaluation Criteria

Score each criterion from **1 (Poor)** to **5 (Excellent)**.

| Criterion | Score 1 | Score 5 |
|---|---|---|
| **Accuracy** | Produces incorrect SQL, invalid syntax, or misleading results. | Generates correct, executable SQL with accurate logic. |
| **Relevance** | Misunderstands requirements or provides generic solutions. | Understands the request and provides a solution tailored to Snowflake/dbt. |
| **Reliability** | Produces inconsistent results, overlooks edge cases, or requires repeated corrections. | Consistently produces dependable solutions and handles edge cases. |

## 2. Five Recent Requests

*These are illustrative scores based on previously discussed work tasks, not verified test results. Rerun the requests to confirm the scores.*

| Recent Request | Accuracy | Relevance | Reliability |
|---|---:|---:|---:|
| Write Snowflake SQL for opening and closing balances | 4 | 5 | 3 |
| Debug dbt incremental model logic | 4 | 5 | 4 |
| Create Snowflake alerts for dashboard queries | 3 | 4 | 3 |
| Handle date-range filtering in Snowsight | 4 | 5 | 3 |
| Explain GitLab cherry-pick and deployment workflow | 5 | 5 | 4 |
| **Average** | **4.0** | **4.8** | **3.4** |

**Illustrative overall score:** 4.1/5 (average of the three criterion averages, rounded to one decimal).

## 3. Where the Tool Falls Short

**Key finding:** ChatGPT performs well at understanding technical requirements but is less reliable when solutions depend on environment-specific configurations or edge cases.

- **Accuracy:** Occasionally generates SQL that requires syntax corrections or adjustments.
- **Relevance:** Usually understands the task and provides context-specific solutions.
- **Reliability:** May overlook date boundaries, NULL handling, incremental processing conditions, or Snowflake-specific limitations.

## 4. Improvement Recommendation

Introduce a **validation checkpoint** before accepting AI-generated solutions. Require ChatGPT to:

1. Check SQL syntax and Snowflake/dbt compatibility.
2. State assumptions and dependencies.
3. Identify edge cases, including NULLs and date boundaries.
4. Provide test queries or verification steps.

## Takeaway

ChatGPT accelerates SQL development and troubleshooting, but its outputs require validation for accuracy, edge cases, and production reliability.
