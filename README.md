Root Challenge - https://www.pluralsight.com/resources/blog/cloud/cloudguruchallenge-python-aws-etl

Lookup blog - https://keertisurapaneni.medium.com/cloudguruchallenge-event-driven-python-on-aws-c1adbdec19ae

ny_url = https://raw.githubusercontent.com/nytimes/covid-19-data/master/us.csv

jh_url = https://raw.githubusercontent.com/datasets/covid-19/master/data/time-series-19-covid-combined.csv

## No module found error


- We need to install required dependecies and package them and upload as lambda layer

- For instance, to fix psycopg2 error, we need to install psycopg2-binary and package it as a layer. This can be done in Cloud9 IDE in AWS. Refer https://www.youtube.com/watch?v=80h9lXE07z0 for more details

```bash
1. sudo amazon-linux-extras install python3.8

2. curl -O https://bootstrap.pypa.io/get-pip.py

3. python3.8 get-pip.py --user

4. sudo python3.8 -m pip install psycopg2-binary -t python/

5. zip -r dependancies.zip python
```

### <u>NOTES</u>

* Always use try-except block to catch exceptions and log them

# <u>STEPS</u>

1. Create a new Postgres RDS instance via python code
2. Create a python code called `etl.py` to extract data from the above two csv files and merge the required data
3. Create a lambda.py where we
    * Create the `notify` function
        - where we do SNS publish

    * Create the `main` function
        - where we call the `etl` function (first try block)
        - connect to Postgres RDS (second try block)
        - Create table if table doesn't exist (third try block)
        - Use condition if `query_results == 0`, insert data for the first time (fourth try block)
        - If `query_results != 0`, check for new data and insert it (fifth try block)
        - Send the inserted rows to email

    * Notify for each below steps
        - Data transformation failed
        - Connection to Postgres failed
        - Unable to find info about db table
        - First data insertion into db table failed
        - Successfully first time data insertion into db table
        - Daily insertion of data failed
        - Send the inserted rows to email
        - else Your data is already up to date, good bye


# ETL Process Flowchart

```mermaid
graph TD
    A[Start] --> B[Extract and Transform Data]
    B --> C{Successful?}
    C -->|No| D[Notify Error and Exit]
    C -->|Yes| E[Connect to Postgres DB]
    E --> F{Connection Successful?}
    F -->|No| G[Notify Error and Exit]
    F -->|Yes| H[Create Table if Not Exists]
    H --> I[Check Table Row Count]
    I --> J{Table Empty?}
    J -->|Yes| K[Insert All Data]
    J -->|No| L[Check for New Data]
    L --> M{New Data Available?}
    M -->|Yes| N[Insert New Data]
    M -->|No| O[Notify Up to Date]
    K --> P[Notify Insertion Complete]
    N --> P
    O --> Q[Close Connection]
    P --> Q
    Q --> R[End]
