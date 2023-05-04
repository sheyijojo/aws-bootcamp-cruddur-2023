# Week 4 — Postgres and RDS

### aws sts get-caller-identity
- if reseted, the use  env grep

# Objective 

* Provision an RDS instance
* Temporarily stop an RDS instance
- Remotely connect to RDS instance
- Programmatically update a security group rule
- Write several bash scripts for database operations
- Operate common SQL commands
- Create a schema SQL file by hand
- Work with UUIDs and PSQL extensions
- Implement a postgres client for python using a connection pool
- Troubleshoot common SQL errors
- Implement a Lambda that runs in a VPC and commits code to RDS
- Work with PSQL json functions to directly return json from the database
- Correctly sanitize parameters passed to SQL to execute

## Spin up RDS instance

It is faster to provision an RDS instance 
-  Secret manager is a must have in managing Postgres
- It is good to change the port number from 5432(default for Postgress)

#### paste this in my cli
- I commented out the dynamo db container inside docker file

```sh
aws rds create-db-instance \
  --db-instance-identifier cruddur-db-instance \
  --db-instance-class db.t3.micro \
  --engine postgres \
  --engine-version  14.6 \
  --master-username sheyirootdb \
  --master-user-password writeyourpassword\
  --allocated-storage 20 \
  --availability-zone us-east-1a \
  --backup-retention-period 0 \
  --port 5432 \
  --no-multi-az \
  --db-name cruddur \
  --storage-type gp2 \
  --publicly-accessible \
  --storage-encrypted \
  --enable-performance-insights \
  --performance-insights-retention-period 7 \
  --no-deletion-protection

# forgot to inclDE CHARACTER set AND time zone

  --character-set-name <value>\

```
## Do not save the RDS password in your github repo
RDS can be stopped temporarily and started automaitcally after 7 days

## connect to psql client or database explorer

#### To connect to psql via the psql client cli tool remember to use the host flag to specific localhost.
`psql -U postgres --host localhost`

#### Common PSQL commands:
```sh
\x on -- expanded display when looking at data
\q -- Quit PSQL
\l -- List all databases
\c database_name -- Connect to a specific database
\dt -- List all tables in the current database
\d table_name -- Describe a specific table
\du -- List all users and their roles
\dn -- List all schemas in the current database

CREATE DATABASE database_name; -- Create a new database

DROP DATABASE database_name; -- Delete a database
CREATE TABLE table_name (column1 datatype1, column2 datatype2, ...); -- Create a new table
DROP TABLE table_name; -- Delete a table
SELECT column1, column2, ... FROM table_name WHERE condition; -- Select data from a table
INSERT INTO table_name (column1, column2, ...) VALUES (value1, value2, ...); -- Insert data into a table
UPDATE table_name SET column1 = value1, column2 = value2, ... WHERE condition; -- Update data in a table
DELETE FROM table_name WHERE condition; -- Delete data from a table

```

- Good practice to obscure Identification
- When you create a table with a UUID column, you can use the UUID extension to generate a unique identifier for each row of data you insert into the table. 
- The extension provides functions that allow you to generate UUIDs in various formats, such as random UUIDs or UUIDs based on specific input data. You can also use UUIDs in queries to efficiently search for specific data.

#### Create Database
Different options to use: directly or psql client

source
`https://www.postgresql.org/docs/current/app-createdb.html`

![psql client](/_docs/assets/psql%20client.png)


```sh
createdb cruddur -h localhost -U postgres
psql -U postgres -h localhost


#using psql client
CREATE database cruddur;

\l
DROP database cruddur;

```

## create schema , Import Script
create a new database folder /backend-flask
#### We'll create a new SQL file called schema.sql and we'll place it in backend-flask/db

command to import


## Add UUID Extension
We are going to have Postgres generate out UUIDs. We'll need to use an extension called:

```sh

CREATE EXTENSION "uuid-ossp";

#use this first and place inside the schema i.e schema.sql
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

#inside the terminal, backend directory
#this runninig an external script called schema.sql and creating an extension
`psql cruddur < db/schema.sql -h localhost -U postgres`


## avoid typing password all the time using by using connection url string
postgresql://[user[:password]@][netloc][:port][/dbname][?param1=value1&...]

export CONNECTION_URL= "postgresql://postgres:password@localhost:5432/cruddur"

## test it before exporting it
psql postgresql://postgres:password@localhost:5432/cruddur

## Export it now in the terminal env
export CONNECTION_URL="postgresql://postgres:password@localhost:5432/cruddur"

##connect to it
psql $CONNECTION_URL
```




## local host == 127.0.0.1