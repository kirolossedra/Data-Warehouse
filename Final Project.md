Here's your content formatted in Markdown for clarity and readability:

```markdown
# Data Warehouse Design and Implementation Lab

## Scenario
You are a data engineer hired by a consumer electronics retail company that sells various electronic products through online and offline channels across major cities in the United States. The company aims to create a data warehouse for analyzing sales performance and inventory management to generate reports such as:

- Total sales revenue per year per city
- Total sales revenue per month per city
- Total sales revenue per quarter per city
- Total sales revenue per year per product category
- Total sales revenue per product category per city
- Total sales revenue per product category per store

## Learning Objectives
After completing this lab, you will be able to:

- Develop dimension and fact tables to organize and structure data effectively for analysis.
- Employ SQL queries to create and load data into dimension and fact tables.
- Create materialized views to optimize query performance.

> **Note:** Throughout this lab, you will be prompted to take screenshots and save them on your device. You can use various free screengrabbing tools or your operating system’s shortcut keys (e.g., Alt + PrintScreen in Windows, Command + Shift + 5 on Mac, Shift + Ctrl + Show Windows on Chromebook) to capture the required screenshots. The screenshots can be saved as jpg or png.

## About the Dataset
The dataset used in this assignment is a programmatically created synthetic dataset for this project.

## Prerequisites
You need to use PostgreSQL Database to complete this lab. You will start your project by designing a Star Schema warehouse by identifying the columns for various dimensions and fact tables.

---

## Exercise 1: Design a Data Warehouse

### Sample Sales Data
| Sales ID | Product Type | Price Per Unit | Quantity Sold | City       | Date       |
|----------|--------------|----------------|---------------|------------|------------|
| 001      | Electronics  | $299.99        | 30            | New York   | 2024-04-01 |
| 002      | Apparel      | $49.99         | 50            | Los Angeles | 2024-04-01 |
| 003      | Furniture    | $399.99        | 10            | Chicago    | 2024-04-02 |
| 004      | Electronics  | $199.99        | 20            | Houston    | 2024-04-02 |
| 005      | Groceries    | $2.99          | 100           | Miami      | 2024-04-03 |

### Task 1: Design the Dimension Table MyDimDate
**Fields in MyDimDate table:**
- dateid
- year
- month
- monthname
- day
- weekday
- weekdayname

The table will have a unique identifier (`dateid`) for each date entry. Other fields provide detailed information, allowing flexible reporting options.

### Task 2: Design the Dimension Table MyDimProduct
**Fields in MyDimProduct table:**
- productid
- productname

This table facilitates analysis based on different products sold.

### Task 3: Design the Dimension Table MyDimCustomerSegment
**Fields in MyDimCustomerSegment table:**
- segmentid
- segmentname

### Task 4: Design the Fact Table MyFactSales
**Fields in MyFactSales table:**
- salesid
- productid
- quantitysold
- priceperunit
- segmentid
- dateid

This table serves as the central repository for sales data, enabling analysis and reporting across various dimensions.

---

## Exercise 2: Create Schema for Data Warehouse on PostgreSQL

### Task 5: Create the Dimension Table MyDimDate
```sql
CREATE TABLE MyDimDate (
    dateid INT PRIMARY KEY,
    year INT,
    month INT,
    monthname VARCHAR(20),
    day INT,
    weekday INT,
    weekdayname VARCHAR(20)
);
```

### Task 6: Create the Dimension Table MyDimProduct
```sql
CREATE TABLE MyDimProduct (
    productid INT PRIMARY KEY,
    productname VARCHAR(255)
);
```

### Task 7: Create the Dimension Table MyDimCustomerSegment
```sql
CREATE TABLE MyDimCustomerSegment (
    segmentid INT PRIMARY KEY,
    segmentname VARCHAR(255)
);
```

### Task 8: Create the Fact Table MyFactSales
```sql
CREATE TABLE MyFactSales (
    salesid INT PRIMARY KEY,
    productid INT,
    quantitysold INT,
    priceperunit DECIMAL(10, 2),
    segmentid INT,
    dateid INT
);
```

---

## Exercise 3: Load Data into the Data Warehouse
After the initial schema design, you learned that the data could not be collected in the initially planned format. You will now load data provided in CSV files according to the new design.

### Task 9: Load Data into the Dimension Table DimDate
Download data from: [DimDate.csv](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/-omGFpVSWBIZKFSCxUkBwg/DimDate.csv).

#### Steps:
1. Open pgAdmin. Create database `PracProj` and connect to it.
2. Create the DimDate table:
   ```sql
   CREATE TABLE DimDate (
       Dateid INT PRIMARY KEY,
       date DATE NOT NULL,
       Year INT NOT NULL,
       Quarter INT NOT NULL,
       QuarterName VARCHAR(2) NOT NULL,
       Month INT NOT NULL,
       Monthname VARCHAR(255) NOT NULL,
       Day INT NOT NULL,
       Weekday INT NOT NULL,
       WeekdayName VARCHAR(255) NOT NULL
   );
   ```
3. Use the import tool in pgAdmin to load the CSV file.
4. Run `SELECT * FROM DimDate LIMIT 5;` and take a screenshot.

### Task 10: Load Data into the Dimension Table DimProduct
Download data from: [DimProduct.csv](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/Y-76u4An3zb5R6HxxFPabA/DimProduct.csv).

#### Steps:
1. Create the DimProduct table:
   ```sql
   CREATE TABLE DimProduct (
       Productid INT PRIMARY KEY,
       Producttype VARCHAR(255) NOT NULL
   );
   ```
2. Load the data and take a screenshot of the first 5 rows.

### Task 11: Load Data into the Dimension Table DimCustomerSegment
Download data from: [DimCustomerSegment.csv](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/h_dnxb8yzQyVjeb8oYnm8A/DimCustomerSegment.csv).

#### Steps:
1. Create the DimCustomerSegment table:
   ```sql
   CREATE TABLE DimCustomerSegment (
       Segmentid INT PRIMARY KEY,
       City VARCHAR(255) NOT NULL
   );
   ```
2. Load the data and take a screenshot of the first 5 rows.

### Task 12: Load Data into the Fact Table FactSales
Download data from: [FactSales.csv](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/a8kTjzvpdqzOp46ODatyAA/FactSales.csv).

#### Steps:
1. Create the FactSales table:
   ```sql
   CREATE TABLE FactSales (
       Salesid VARCHAR(255) PRIMARY KEY,
       Dateid INT NOT NULL,
       Productid INT NOT NULL,
       Segmentid INT NOT NULL,
       Price_PerUnit DECIMAL(10, 2) NOT NULL,
       QuantitySold INT NOT NULL,
       FOREIGN KEY (Dateid) REFERENCES DimDate(Dateid),
       FOREIGN KEY (Productid) REFERENCES DimProduct(Productid),
       FOREIGN KEY (Segmentid) REFERENCES DimCustomerSegment(Segmentid)
   );
   ```
2. Load the data and take a screenshot of the first 5 rows.

---

## Exercise 4: Write Aggregation Queries and Create Materialized View

### Task 13: Create a Grouping Sets Query
```sql
SELECT
    p.Productid,
    p.Producttype,
    SUM(f.Price_PerUnit * f.QuantitySold) AS TotalSales
FROM
    FactSales f
INNER JOIN
    DimProduct p ON f.Productid = p.Productid
GROUP BY GROUPING SETS (
    (p.Productid, p.Producttype),
    p.Productid,
    p.Producttype,
    ()
)
ORDER BY
    p.Productid,
    p.Producttype;
```

### Task 14: Create a Rollup Query
```sql
SELECT
    d.Year,
    cs.City,
    p.Productid,
    SUM(f.Price_PerUnit * f.QuantitySold) AS TotalSales 
FROM
    FactSales f
JOIN
    DimDate d ON f.Dateid = d.Dateid
JOIN
    DimProduct p ON f.Productid = p.Productid
JOIN
    DimCustomerSegment cs ON f.Segmentid = cs.Segmentid
GROUP BY ROLLUP (d.Year, cs.City, p.Productid)
ORDER BY
    d.Year DESC,
    cs.City,
    p.Productid;
```

### Task 15: Create a Cube Query
```sql
SELECT
    d.Year,
    cs.City,
    p.Productid,
    AVG(f.Price_PerUnit * f.QuantitySold) AS AverageSales
FROM
    FactSales f
INNER JOIN
    DimDate d ON f.Dateid = d.Dateid
INNER JOIN
    DimProduct p ON f.Productid = p.Productid
INNER JOIN
    DimCustomerSegment cs ON f.Segmentid = cs.Segmentid
GROUP BY CUBE (d.Year, cs.City, p.Productid);
```

### Task 16: Create a Materialized View
```sql
CREATE MATERIALIZED VIEW max_sales AS


SELECT
    d.Year,
    cs.City,
    p.Productid,
    SUM(f.Price_PerUnit * f.QuantitySold) AS TotalSales
FROM
    FactSales f
JOIN
    DimDate d ON f.Dateid = d.Dateid
JOIN
    DimProduct p ON f.Productid = p.Productid
JOIN
    DimCustomerSegment cs ON f.Segmentid = cs.Segmentid
GROUP BY
    d.Year,
    cs.City,
    p.Productid;
```
```

Feel free to make any modifications or ask if you need anything else!
