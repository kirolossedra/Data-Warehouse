Here's a structured overview for your data warehouse assignment based on the scenario you provided, formatted in Markdown for clarity.

```markdown
# Solid Waste Management Data Warehouse Lab

## Scenario
You are a data engineer hired by a solid waste management company in Brazil. The company collects and recycles solid waste across major cities and operates hundreds of trucks. They aim to create a data warehouse for generating reports such as:

- Total waste collected per year per city
- Total waste collected per month per city
- Total waste collected per quarter per city
- Total waste collected per year per truck type
- Total waste collected per truck type per city
- Total waste collected per truck type per station per city

## Learning Objectives
After completing this lab, you will be able to:

- Design a data warehouse.
- Load data into the data warehouse.
- Create a materialized view.

> **Note:** Throughout this lab, you will be prompted to take screenshots of your work. Save these as either .jpg or .png files for peer review.

## About the Dataset
The dataset provided is synthetic and created specifically for this assignment.

## Prerequisites
You will need to use PostgreSQL Database and pgAdmin for this assignment.

---

## Exercise 1: Design a Data Warehouse

### Task 1: Design the Dimension Table `MyDimDate`
#### Fields for `MyDimDate`
- dateid
- year
- month
- monthname
- day
- weekday
- weekdayname

> **Screenshot:** Name as `1-MyDimDate.jpg`.

### Task 2: Design the Dimension Table `MyDimWaste`
#### Fields for `MyDimWaste`
- wasteid
- wastetype
- trucktype

> **Screenshot:** Name as `2-MyDimWaste.jpg`.

### Task 3: Design the Dimension Table `MyDimZone`
#### Fields for `MyDimZone`
- zoneid
- city
- station

> **Screenshot:** Name as `3-MyDimZone.jpg`.

### Task 4: Design the Fact Table `MyFactTrips`
#### Fields for `MyFactTrips`
- tripid
- dateid
- wasteid
- zoneid
- amount_collected

> **Screenshot:** Name as `4-MyFactTrips.jpg`.

---

## Exercise 2: Create Schema for Data Warehouse on PostgreSQL

### Task 5: Create the Dimension Table `MyDimDate`
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
> **Screenshot:** Name as `5-MyDimDate.jpg`.

### Task 6: Create the Dimension Table `MyDimWaste`
```sql
CREATE TABLE MyDimWaste (
    wasteid INT PRIMARY KEY,
    wastetype VARCHAR(255),
    trucktype VARCHAR(255)
);
```
> **Screenshot:** Name as `6-MyDimWaste.jpg`.

### Task 7: Create the Dimension Table `MyDimZone`
```sql
CREATE TABLE MyDimZone (
    zoneid INT PRIMARY KEY,
    city VARCHAR(255),
    station VARCHAR(255)
);
```
> **Screenshot:** Name as `7-MyDimZone.jpg`.

### Task 8: Create the Fact Table `MyFactTrips`
```sql
CREATE TABLE MyFactTrips (
    tripid INT PRIMARY KEY,
    dateid INT,
    wasteid INT,
    zoneid INT,
    amount_collected DECIMAL(10, 2),
    FOREIGN KEY (dateid) REFERENCES MyDimDate(dateid),
    FOREIGN KEY (wasteid) REFERENCES MyDimWaste(wasteid),
    FOREIGN KEY (zoneid) REFERENCES MyDimZone(zoneid)
);
```
> **Screenshot:** Name as `8-MyFactTrips.jpg`.

---

## Exercise 3: Load Data into the Data Warehouse

### Task 9: Load Data into the Dimension Table `DimDate`
1. Download data from: [DimDate.csv](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0260EN-SkillsNetwork/labs/Final%20Assignment/DimDate.csv).
2. Load data into `DimDate`.
3. **Screenshot:** First 5 rows. Name as `9-DimDate.jpg`.

### Task 10: Load Data into the Dimension Table `DimTruck`
1. Download data from: [DimTruck.csv](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0260EN-SkillsNetwork/labs/Final%20Assignment/DimTruck.csv).
2. Load data into `DimTruck`.
3. **Screenshot:** First 5 rows. Name as `10-DimTruck.jpg`.

### Task 11: Load Data into the Dimension Table `DimStation`
1. Download data from: [DimStation.csv](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0260EN-SkillsNetwork/labs/Final%20Assignment/DimStation.csv).
2. Load data into `DimStation`.
3. **Screenshot:** First 5 rows. Name as `11-DimStation.jpg`.

### Task 12: Load Data into the Fact Table `FactTrips`
1. Download data from: [FactTrips.csv](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0260EN-SkillsNetwork/labs/Final%20Assignment/FactTrips.csv).
2. Load data into `FactTrips`.
3. **Screenshot:** First 5 rows. Name as `12-FactTrips.jpg`.

---

## Exercise 4: Write Aggregation Queries and Create Materialized Views

### Task 13: Create a Grouping Sets Query
```sql
SELECT 
    stationid,
    trucktype,
    SUM(amount_collected) AS total_waste
FROM 
    FactTrips
GROUP BY 
    GROUPING SETS (stationid, trucktype);
```
> **Screenshot:** SQL and output rows. Name as `13-groupingsets.jpg`.

### Task 14: Create a Rollup Query
```sql
SELECT 
    year,
    city,
    stationid,
    SUM(amount_collected) AS total_waste
FROM 
    FactTrips
JOIN 
    DimDate ON FactTrips.dateid = DimDate.dateid
JOIN 
    DimZone ON FactTrips.zoneid = DimZone.zoneid
GROUP BY 
    ROLLUP (year, city, stationid);
```
> **Screenshot:** SQL and output rows. Name as `14-rollup.jpg`.

### Task 15: Create a Cube Query
```sql
SELECT 
    year,
    city,
    stationid,
    AVG(amount_collected) AS average_waste
FROM 
    FactTrips
JOIN 
    DimDate ON FactTrips.dateid = DimDate.dateid
JOIN 
    DimZone ON FactTrips.zoneid = DimZone.zoneid
GROUP BY 
    CUBE (year, city, stationid);
```
> **Screenshot:** SQL and output rows. Name as `15-cube.jpg`.

### Task 16: Create a Materialized View
```sql
CREATE MATERIALIZED VIEW max_waste_stats AS
SELECT 
    city,
    stationid,
    trucktype,
    MAX(amount_collected) AS max_waste
FROM 
    FactTrips
JOIN 
    DimZone ON FactTrips.zoneid = DimZone.zoneid
JOIN 
    DimTruck ON FactTrips.trucktype = DimTruck.trucktype
GROUP BY 
    city, stationid, trucktype;
```
> **Screenshot:** SQL. Name as `16-mv.jpg`.
```

Feel free to adjust any sections or ask for further details on specific tasks!
