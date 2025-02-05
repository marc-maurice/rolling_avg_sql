# Rolling 3-Day Average Transaction Amount

## Project Overview
This project demonstrates how to calculate a rolling 3-day average of total transaction amounts processed per day using SQL. The dataset consists of transaction records with timestamps and corresponding amounts.

## Dataset Structure
The dataset is stored in a table named `transactions` with the following structure:

```sql
CREATE OR REPLACE TABLE transactions (
  "transaction_time" TIMESTAMP,
  "transaction_amount" FLOAT
);
```

### Sample Data
Example records inserted into the table:

```sql
INSERT INTO transactions
  ("transaction_time", "transaction_amount")
VALUES
  ('2021-01-16 00:05:54.000000', 25.05),
  ('2021-01-07 20:53:04.000000', 124.00),
  ('2021-01-18 22:55:37.000000', 66.58),
  ('2021-01-31 00:09:01.000000', 23.10),
  ('2021-01-30 00:04:01.000000', 6.75),
  ('2021-01-29 05:39:47.000000', 33.73);
```

## SQL Query to Compute Rolling 3-Day Average
The following SQL query calculates the rolling 3-day average of total transaction amounts per day and retrieves the result for January 31, 2021:

```sql
WITH daily_total AS (
    SELECT 
        DATE(tt."transaction_time") AS "date",
        SUM(tt."transaction_amount") AS "transactions"
    FROM 
        transactions tt
    GROUP BY
        DATE(tt."transaction_time")
    ORDER BY 
        DATE(tt."transaction_time")
),
rolling_3day AS (
    SELECT 
        dt."date",
        AVG(dt."transactions") OVER (
            ORDER BY dt."date"
            ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
        ) AS rolling_3day_avg
    FROM
        daily_total dt
    ORDER BY
        dt."date"
)
SELECT *
FROM rolling_3day
WHERE rolling_3day."date" = '2021-01-31';
```

## Explanation of the Query
1. **`daily_total` CTE**: Aggregates transaction amounts by date.
2. **`rolling_3day` CTE**: Calculates the rolling 3-day average using a window function.
3. **Final Query**: Filters the results to return the rolling average for January 31, 2021.

## Usage
To run this query, ensure that:
- The dataset is loaded into a SQL-compatible environment.
- The `transactions` table is created with the correct schema.
- The SQL engine supports window functions.

This approach can be extended to analyze rolling averages for other dates or time periods.
