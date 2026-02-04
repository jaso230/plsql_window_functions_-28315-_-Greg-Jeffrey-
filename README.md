# plsql_window_functions_-28315-_-Greg-Jeffrey-
ASSIGNMENT 1

         BANK SALES ANALYSIS WINDOW FUNCTION

1. Business Problem

Context: A bank wants to improve marketing and customer retention using transaction and product data.
Challenge: The bank doesn’t know which customers or products drive most revenue or which customers are inactive.
Expected Outcome: Identify top products, customer trends, and high-risk customers for targeted campaigns.

2 success criteria

| Goal                        | Window Function |
| --------------------------- | --------------- |
| Top 5 products per region   | `RANK()`        |
| Running monthly sales       | `SUM() OVER()`  |
| Month-over-month growth     | `LAG()/LEAD()`  |
| Customer quartiles          | `NTILE(4)`      |
| Three-month moving averages | `AVG() OVER()`  |

3. Database Tables

Customers
| Column | Type | Key |
|--------|------|-----|
| customer_id | INT | PK |
| first_name | VARCHAR | |
| last_name | VARCHAR | |
| region | VARCHAR | |

Customers
| Column | Type | Key |
|--------|------|-----|
| customer_id | INT | PK |
| first_name | VARCHAR | |
| last_name | VARCHAR | |
| region | VARCHAR | |

Transactions
| Column | Type | Key |
|--------|------|-----|
| transaction_id | INT | PK |
| customer_id | INT | FK → Customers(customer_id) |
| product_id | INT | FK → Products(product_id) |
| transaction_date | DATE | |
| amount | DECIMAL | |

Demonstrate correct and meaningful use of SQL JOINs using the tables step 3

INNER JOIN
    SELECT c.Name, c.City, a.AccountID, a.Balance
    FROM Clients c
    INNER JOIN Comptes a
    ON c.ClientID = a.ClientID;

LEFT JOIN
   SELECT c.Name, c.City, a.AccountID, a.Balance
   FROM Clients c
   LEFT JOIN Comptes a
   ON c.ClientID = a.ClientID;
    
RIGHT JOIN
    SELECT c.Name, a.AccountID, a.Balance
    FROM Clients c
    RIGHT JOIN Comptes a
    ON c.ClientID = a.ClientID;

FULL OUTER JOIN 
   SELECT c.Name, c.City, a.AccountID, a.Balance
   FROM Clients c
   FULL OUTER JOIN Comptes a
   ON c.ClientID = a.ClientID;

SELF JOIN
    SELECT a.Name AS Client1, b.Name AS Client2, a.City
    FROM Clients a
    INNER JOIN Clients b
    ON a.City = b.City
    AND a.ClientID < b.ClientID;

Impliment all categories of window functions

Ranking function 
   
   SELECT 
    c.Name,  -- Client's name
    SUM(a.Balance) AS TotalBalance,  -- Total balance across all their accounts
    RANK() OVER (ORDER BY SUM(a.Balance) DESC) AS RankBalance  
    -- Rank of clients by total balance (1 = richest client)
FROM Clients c
INNER JOIN Accounts a
    ON c.ClientID = a.ClientID  -- Join each client to their accounts
GROUP BY c.Name;  -- Group by client to sum balances

Business Interpretation: 
This query ranks clients by their total balance. It helps the bank identify VIP clients for personalized offers and priority services.

Aggregate Window Function

SELECT 
    t.AccountID,  -- Account ID for this transaction
    c.Name,       -- Client's name
    t.Amount,     -- Transaction amount
    AVG(t.Amount) OVER (PARTITION BY c.ClientID) AS AvgTransaction  
    -- Average transaction amount per client (partitioned by client)
FROM Transactions t
INNER JOIN Accounts a
    ON t.AccountID = a.AccountID  -- Join each transaction to its account
INNER JOIN Clients c
    ON a.ClientID = c.ClientID;   -- Join each account to its client

Business Interpretation:
This calculates the average transaction amount per client. Useful to detect unusual behavior or potential fraud risk.

Navigation Function

SELECT 
    t.AccountID,  -- Account ID
    t.TransactionDate,  -- Date of the transaction
    t.Amount,          -- Current transaction amount
    LAG(t.Amount) OVER (PARTITION BY t.AccountID ORDER BY t.TransactionDate) AS PrevTransaction
    -- Previous transaction amount in the same account, ordered by date
FROM Transactions t;

Business Interpretation:
Shows the previous transaction for each account to track changes over time. Helps the bank monitor for sudden spikes or unusual withdrawals/deposits.

Distribution Function

SELECT 
    c.Name,  -- Client's name
    SUM(a.Balance) AS TotalBalance,  -- Total balance across all accounts
    NTILE(4) OVER (ORDER BY SUM(a.Balance) DESC) AS BalanceQuartile
    -- Assigns each client to a quartile (1 = top, 4 = lowest)
FROM Clients c
INNER JOIN Accounts a
    ON c.ClientID = a.ClientID  -- Join accounts to clients
GROUP BY c.Name;  -- Group by client

Business Interpretation:
Clients are divided into quartiles by total balance. This helps the bank segment its customer base and target different marketing strategies or offers based on wealth level.

Results Analysis

Descriptive: Top products/customers identified; some inactive customers found.

Diagnostic: Regional preferences, promotions, and seasonal trends explain differences.

Prescriptive: Target inactive customers, promote underperforming products, plan inventory using trends.

References

PostgreSQL Docs – Window Functions: https://www.postgresql.org/docs/current/functions-window.html

SQLShack – Window Functions: https://www.sqlshack.com/sql-window-functions-ranking-aggregate-navigation/

W3Schools – SQL Joins: https://www.w3schools.com/sql/sql_join.asp

Integrity Statement:

All work is original. Sources cited. No AI-generated content copied without adaptation.



