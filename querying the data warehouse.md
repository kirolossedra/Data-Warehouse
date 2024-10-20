# Lab: Querying the Data Warehouse (Cubes, Rollups, Grouping Sets, and Materialized Views)

## Exercise 1: Launch a PostgreSQL Server Instance

1. Launch a PostgreSQL server instance on Cloud IDE.
2. Open the pgAdmin Graphical User Interface.

> **Note:** This lab requires completion of the previous lab, **Populate a Data Warehouse**. If you haven't finished it yet, please do so before continuing.

### Overview of Operators
- **GROUPING SETS:** Allows grouping data in different ways within a single `SELECT` statement.
- **ROLLUP:** Creates subtotals and grand totals for specified columns.
- **CUBE:** Produces subtotals and grand totals for every permutation of the specified columns.

---

## Exercise 2: Write a Query Using Grouping Sets

To create a grouping set for the columns `year`, `category`, and the sum of `billedamount`, run the following SQL statement:

```sql
SELECT year, category, SUM(billedamount) AS totalbilledamount
FROM "FactBilling"
LEFT JOIN "DimCustomer" ON "FactBilling".customerid = "DimCustomer".customerid
LEFT JOIN "DimMonth" ON "FactBilling".monthid = "DimMonth".monthid
GROUP BY GROUPING SETS (year, category);
```

> **Output:** Partial output can be seen in the image below.

---

## Exercise 3: Write a Query Using Rollup

To create a rollup using the columns `year`, `category`, and the sum of `billedamount`, run the following SQL statement:

```sql
SELECT year, category, SUM(billedamount) AS totalbilledamount
FROM "FactBilling"
LEFT JOIN "DimCustomer" ON "FactBilling".customerid = "DimCustomer".customerid
LEFT JOIN "DimMonth" ON "FactBilling".monthid = "DimMonth".monthid
GROUP BY ROLLUP(year, category)
ORDER BY year, category;
```

> **Output:** Partial output can be seen in the image below.

---

## Exercise 4: Write a Query Using Cube

To create a cube using the columns `year`, `category`, and the sum of `billedamount`, run the following SQL statement:

```sql
SELECT year, category, SUM(billedamount) AS totalbilledamount
FROM "FactBilling"
LEFT JOIN "DimCustomer" ON "FactBilling".customerid = "DimCustomer".customerid
LEFT JOIN "DimMonth" ON "FactBilling".monthid = "DimMonth".monthid
GROUP BY CUBE(year, category)
ORDER BY year, category;
```

> **Output:** Partial output can be seen in the image below.

---

## Exercise 5: Create a Materialized Query Table

### Step 1: Create Materialized Views

Execute the following SQL statement to create a materialized view named `countrystats`:

```sql
CREATE MATERIALIZED VIEW countrystats (country, year, totalbilledamount) AS
SELECT country, year, SUM(billedamount)
FROM "FactBilling"
LEFT JOIN "DimCustomer" ON "FactBilling".customerid = "DimCustomer".customerid
LEFT JOIN "DimMonth" ON "FactBilling".monthid = "DimMonth".monthid
GROUP BY country, year;
```

### Step 2: Refresh Data in Materialized Views

Execute the following SQL statement to refresh the data in the materialized view:

```sql
REFRESH MATERIALIZED VIEW countrystats;
```

### Step 3: Query the Materialized Views

Once the materialized view is refreshed, query it with the following SQL statement:

```sql
SELECT * FROM countrystats;
```

---

## Practice Exercises

### Problem 1: Grouping Set for Year and Quartername

Create a grouping set for the columns `year`, `quartername`, and the sum of `billedamount`:

```sql
SELECT year, quartername, SUM(billedamount) AS totalbilledamount
FROM "FactBilling"
LEFT JOIN "DimCustomer" ON "FactBilling".customerid = "DimCustomer".customerid
LEFT JOIN "DimMonth" ON "FactBilling".monthid = "DimMonth".monthid
GROUP BY GROUPING SETS (year, quartername);
```

### Problem 2: Rollup for Country and Category

Create a rollup for the columns `country`, `category`, and the sum of `billedamount`:

```sql
SELECT country, category, SUM(billedamount) AS totalbilledamount
FROM "FactBilling"
LEFT JOIN "DimCustomer" ON "FactBilling".customerid = "DimCustomer".customerid
LEFT JOIN "DimMonth" ON "FactBilling".monthid = "DimMonth".monthid
GROUP BY ROLLUP(country, category)
ORDER BY country, category;
```

### Problem 3: Cube for Year, Country, and Category

Create a cube for the columns `year`, `country`, `category`, and the sum of `billedamount`:

```sql
SELECT year, country, category, SUM(billedamount) AS totalbilledamount
FROM "FactBilling"
LEFT JOIN "DimCustomer" ON "FactBilling".customerid = "DimCustomer".customerid
LEFT JOIN "DimMonth" ON "FactBilling".monthid = "DimMonth".monthid
GROUP BY CUBE(year, country, category);
```

### Problem 4: Materialized View for Average Bill Amount

Create a materialized view named `average_billamount` with columns `year`, `quarter`, `category`, `country`, and `average_bill_amount`:

```sql
CREATE MATERIALIZED VIEW average_billamount (year, quarter, category, country, average_bill_amount) AS
SELECT year, quarter, category, country, AVG(billedamount) AS average_bill_amount
FROM "FactBilling"
LEFT JOIN "DimCustomer" ON "FactBilling".customerid = "DimCustomer".customerid
LEFT JOIN "DimMonth" ON "FactBilling".monthid = "DimMonth".monthid
GROUP BY year, quarter, category, country;
```

Refresh the materialized view:

```sql
REFRESH MATERIALIZED VIEW average_billamount;
```

---

## Congratulations!
You have successfully finished the **Querying the Data Warehouse** lab!
