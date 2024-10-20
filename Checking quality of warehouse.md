# Exercise 1 - Getting the Environment Ready

In this exercise, you will prepare the environment to perform data quality checks.

## Step 1: Start the PostgreSQL Server

1. Click on **Skills Network toolbox**.
2. Select **PostGres database**.
3. Click on the **Start** button.

## Step 2: Download the Staging Area Setup Script

1. Open a new terminal by clicking on the menu bar and selecting **Terminal → New Terminal**.
   ![New Terminal in Menu](screenshot-url)
   
   This will open a new terminal at the bottom of the screen.
   ![New Terminal at Bottom](screenshot-url)

2. Run the command below to download the staging area setup script:

   ```bash
   wget https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/8ZUkKar_boDbhNgMiwGAWg/setupstagingarea.sh
   ```

## Step 3: Export Your PostgreSQL Password

Run the command below in the terminal, replacing `<your postgres password>` with your PostgreSQL password found under the connection information tab.

```bash
export PGPASSWORD=<your postgres password>
```

## Step 4: Run the Setup Script

Run the command below to execute the staging area setup script:

```bash
bash setupstagingarea.sh
```

When you see the message **Successfully setup the staging area**, you are ready to perform data quality checks.

---

# Exercise 2 - Getting the Testing Framework Ready

You can perform most of the data quality checks by manually running SQL queries on the data warehouse. However, automating these checks using custom programs or tools is recommended. Automation helps you to easily:

- Create new tests
- Run tests
- Schedule tests

We will be using a Python-based framework to run the data quality tests.

## Step 1: Download the Framework

Run the commands below to download the framework:

```bash
wget https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0260EN-SkillsNetwork/labs/Verifying%20Data%20Quality%20for%20a%20Data%20Warehouse/dataqualitychecks.py
wget https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/HB0XK4MDrGwigMmVPmPoeQ/dbconnect.py
wget https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0260EN-SkillsNetwork/labs/Verifying%20Data%20Quality%20for%20a%20Data%20Warehouse/mytests.py
wget https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/saTxV8y9Kt-e8Zxe29M0TA/generate-data-quality-report.py
```

Then, list the downloaded files:

```bash
ls
```

## Step 2: Install the Python Driver for PostgreSQL

Run the command below to install the Python driver for the PostgreSQL database:

```bash
python3 -m pip install psycopg2
```

## Step 3: Update Password in `dbconnect.py`

1. Go to **File > Open**. A window will appear; double-click on `dbconnect.py` to open it.
   ![Open dbconnect.py](screenshot-url)

2. In line 3, replace `<replace this with your postgres password>` with your `POSTGRES_PASSWORD` from the Connection Information tab.

3. Save the file by selecting **File > Save**.
   ![Replace Password](screenshot-url)

4. Repeat the above steps for `generate-data-quality-report.py`, updating your password in line 15 and saving it.

## Step 4: Test Database Connectivity

Now we need to check:

- If the PostgreSQL Python driver is installed properly
- If the PostgreSQL server is up and running
- If our micro framework can connect to the database

Run the command below:

```bash
python3 dbconnect.py
```

If all goes well, you should see the message **Successfully connected to database**. The command also disconnects from the server with a message **Connection closed**.

---

# Exercise 3 - Create a Sample Data Quality Report

Run the command below to install Pandas:

```bash
python3 -m pip install pandas tabulate
```

Next, run the command below to generate a sample data quality report:

```bash
python3 generate-data-quality-report.py
```

You should see a list of tests that were run and their status.

---

# Exercise 4 - Explore the Data Quality Tests

Open the file `mytests.py` in the editor:

![Open mytests.py](screenshot-url)

The file `mytests.py` contains all the data quality tests and provides a quick way to author and run new tests.

### Available Tests

- **check_for_nulls**: Checks for nulls in a column
- **check_for_min_max**: Checks if values in a column are within a specified range
- **check_for_valid_values**: Checks for any invalid values in a column
- **check_for_duplicates**: Checks for duplicates in a column

Each test can be authored by specifying a minimum of four parameters:

- `testname`: The human-readable name of the test for reporting
- `test`: The actual test name provided by the testing micro framework
- `table`: The name of the table on which the test is performed
- `column`: The name of the column on which the test is performed

---

# Exercise 5 - Check for Nulls

Here is a sample `check_for_nulls` test:

```python
test1={
    "testname":"Check for nulls",
    "test":check_for_nulls,
    "column": "monthid",
    "table": "DimMonth"
}
```

All tests must be uniquely identified, and provide an easy-to-understand description.

### Create a New Check for Nulls Test

The following test checks for any null values in the `year` column of the `DimMonth` table. The test fails if nulls exist.

Copy and paste the code below at the end of the `mytests.py` file:

```python
test5={
    "testname":"Check for nulls",
    "test":check_for_nulls,
    "column": "year",
    "table": "DimMonth"
}
```

Save the file using **Menu → File → Save**.

Run the command below to generate the new data quality report:

```bash
python3 generate-data-quality-report.py
```

---

# Exercise 6 - Check for Min Max Range

Here is a sample `check_for_min_max` test:

```python
test2={
    "testname":"Check for min and max",
    "test":check_for_min_max,
    "column": "monthid",
    "table": "DimMonth",
    "minimum":1,
    "maximum":12
}
```

### Create a New Check for Min Max Test

The following test checks for a minimum of 1 and a maximum of 4 in the `quarter` column of the `DimMonth` table. The test fails if any values are outside this range.

Copy and paste the code below at the end of the `mytests.py` file:

```python
test6={
    "testname":"Check for min and max",
    "test":check_for_min_max,
    "column": "quarter",
    "table": "DimMonth",
    "minimum":1,
    "maximum":4
}
```

Save the file and run the command below:

```bash
python3 generate-data-quality-report.py
```

---

# Exercise 7 - Check for Any Invalid Entries

Here is a sample `check_for_valid_values` test:

```python
test3={
    "testname":"Check for valid values",
    "test":check_for_valid_values,
    "column": "category",
    "table": "DimCustomer",
    "valid_values":{'Individual','Company'}
}
```

### Create a New Check for Valid Values Test

The following test checks for valid values in the `quartername` column of the `DimMonth` table. The valid values are `Q1`, `Q2`, `Q3`, `Q4`.

Copy and paste the code below at the end of the `mytests.py` file:

```python
test7={
    "testname":"Check for valid values",
    "test":check_for_valid_values,
    "column": "quartername",
    "table": "DimMonth",
    "valid_values":{'Q1','Q2','Q3','Q4'}
}
```

Save the file and run the command below:

```bash
python3 generate-data-quality-report.py
```

---

# Exercise 8 - Check for Duplicate Entries

Here is a sample `check_for_duplicates` test:

```python
test4={
    "testname":"Check for duplicates",
    "test":check_for_duplicates,
    "column": "monthid",
    "table": "DimMonth"
}
```

### Create a New Check for Duplicates Test

The following test checks for any duplicate values in the `customerid` column of the `DimCustomer` table. The test fails if duplicates exist.

Copy and paste the code below at the end of the `mytests.py` file:

```python
test8={
    "testname":"Check for duplicates",
    "test":check_for_duplicates,
    "column": "customerid",
    "table": "DimCustomer"
}
```

Save the file and run the command below:

```bash
python3 generate-data-quality-report.py
```

---

# Practice Exercises

### Problem 1
Create a `check_for_nulls` test on the `billedamount`
