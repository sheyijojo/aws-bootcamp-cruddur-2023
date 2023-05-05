# Week 4 — Postgres and RDS

### aws sts get-caller-identity
- if reseted, the use  env | grep psql to check for env var.
```sh
curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg

 echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list

sudo apt update
sudo apt install -y postgresql-client-13 libpq-dev

whereis psql
export PSQL_DIR=/usr/bin/psql 
export PATH="$PSQL_DIR:$PATH"

export HONEYCOMB_SERVICE_NAME="Cruddur"
gp env PATH="$PSQL_DIR:$PATH"
env | grep psql
```

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


# Start of my local mode DB Postgres
#### paste this in my cli
- I commented out the dynamo db container inside docker file
- I am working on RDS Posgres
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
RDS can be stopped in the AWS console temporarily and started automaitcally after 7 days

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
## persist it for later
gp env CONNECTION_URL="postgresql://postgres:password@localhost:5432/cruddur"

##connect to it
psql $CONNECTION_URL
```
## local host == 127.0.0.1

## set for production URL for the RDS instance
```sh
export PROD_CONNECTION_URL="postgresql://sheyirootdb:passwordismyhead@cruddur-db-instance.cxahjycbg2wr.us-east-1.rds.amazonaws.com:5432/cruddur"

gp env PROD_CONNECTION_URL="postgresql://sheyirootdb:passwordismyhead@cruddur-db-instance.cxahjycbg2wr.us-east-1.rds.amazonaws.com:5432/cruddur"
```

# Shell Script
## create a bin folder, wanna run a bash script
- Add db-create file
- Add db-drop file
- Add db-schema-load file

`whereis bash` on the terminal

## Inside the db-create script file
```sh
#! /usr/bin/bash
ls -l ./bin
#chmod mode for the user only
chmod u+x bin/db-create


#chmod for all the groups
chmod +x bin/db-create

chmod for all the files in bin
chmod u+x bin/*

PSQL $CONNECTION_URL -c "DROP database cruddur;"
```

## Note 

`` ./bin/db-drop``. This won't run because. we are connecting to the database and the same time, we want to drop the database. 

## Introduce SED. 
`man SED` we need to manipulate the test
## SOURCE
https://askubuntu.com/questions/595269/use-sed-on-a-string-variable-rather-than-a-file

## Drop database SHell script using SED

Put inside the db-drop
```sh
#! /usr/bin/bash
# /\ - escapes it
# g - globally

echo "db-drop"

NO_DB_CONNECTION_URL=$(sed 's/\/cruddur//g' <<<"$CONNECTION_URL")
psql $NO_DB_CONNECTION_URL -c "DROP database cruddur;"

#on terminal
./bin/db-drop
or
 source /bin/db-drop
```

## code explanation above
```sThis Bash script does the following:

It extracts the connection URL from the environment variable $CONNECTION_URL.
It removes the "/cruddur" string from the connection URL using the sed command and stores the modified URL in the $NO_DB_CONNECTION_URL variable.


It runs the psql command with the modified connection URL to connect to the PostgreSQL database and execute the SQL command "DROP database cruddur;", which drops the "cruddur" database from the server.


In short, this script drops the "cruddur" database from the PostgreSQL server by connecting to it using the provided connection URLh

```

## Create Database

```sh
#! /usr/bin/bash

NO_DB_CONNECTION_URL=$(sed 's/\/cruddur//g' <<<"$CONNECTION_URL")
psql $NO_DB_CONNECTION_URL  -c "CREATE database cruddur;"

```

## Load the schema
connect to the database with the schema file db-schema
schema is like the excel sheet

## Shell script to load the seed data
```sh
#! /usr/bin/bash

echo "db-schema-load"

schema_path="$(realpath .)/db/schema.sql"

echo $schema_path

#toggle between prod mode and local dev mode
if [ "$1" = "prod" ]; then
  echo "Running in production mode"
  URL=$PROD_CONNECTION_URL
else
  URL=$CONNECTION_URL
fi

psql $CONNECTION_URL cruddur < $schema_path



# run this using
./bin/db-schema-load prod


```
## Get a beautiful color in db-schema

```sh
CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="db-schema-load"
printf "${CYAN}== ${LABEL}${NO_COLOR}\n"


```

## Create Tables on schmema.sql
- Load the data through a connection db-schema-load
- create the data in db using sql, schema.sql
- Make changes to schema, then have to rerun
- `chmod u+x ./db/schema.sql`
```sh
-- forcefully drop our tables if they already exist
DROP TABLE IF EXISTS public.users cascade;
DROP TABLE IF EXISTS public.activities;


CREATE TABLE public.users (
  uuid UUID default uuid_generate_v4() primary key,
  display_name text,
  handle text,
  cognito_user_id text,
  created_at timestamp default current_timestamp NOT NULL
);

## table 2
CREATE TABLE public.activities (
  uuid UUID default uuid_generate_v4() primary key,
  user_uuid UUID REFERENCES public.users(uuid) NOT NULL,
  message text NOT NULL,
  replies_count integer default 0,
  reposts_count integer default 0,
  likes_count integer default 0,
  reply_to_activity_uuid integer,
  expires_at timestamp,
  created_at timestamp default current_timestamp NOT NULL
);

```
## Schema specification is found in the open-api file in the backend

![openapischema](/_docs/assets/api%20schema%20specification.png)

## connect to the db
```sh
#coonect
sh./bin/db-connect

#list tables
\dt

```## Other clor codes
```sh

CYAN='\033[1;36m'
RED='\033[0;31m'
GREEN='\033[0;32m'
Yellow='\033[0;33m'
Blue='\033[0;34m'
Magenta='\033[0;35m'
NO_COLOR='\033[0m'

LABEL="db-connect"

printf "${RED}==${LABEL}${NO_COLOR}\n"

```

![tables in database](/_docs/assets/database%20tables%20in%20local%20mode.png)


# seed data or load data into the database
##### Create a file /bin/db-seed


```sh
echo "db-schema-load"
seed_path="$(realpath .)/db/schema.sql"
echo $seed_path

if [ "$1" = "prod" ]; then
  echo "Running in production mode"
  URL=$PROD_CONNECTION_URL
else
  URL=$CONNECTION_URL
fi

psql $URL cruddur < $seed_path

``
## create a connecting url for seed.sql
Apparently we need to the seed file to load data into the database. Data to be loaded is found in the seed.sql

```sh
#! /usr/bin/bash


CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="db-seed"
printf "${CYAN}== ${LABEL}${NO_COLOR}\n"

echo "db-schema-load"
seed_path="$(realpath .)/db/seed.sql"
echo $seed_path

if [ "$1" = "prod" ]; then
  echo "Running in production mode"
  URL=$PROD_CONNECTION_URL
else
  URL=$CONNECTION_URL
fi

psql $URL cruddur < $seed_path

```



## create a file in db that has the data seed.sql
# Always run the file throug the connection in bin
##### /db/seed.ql

```sh
-- this file was manually created
INSERT INTO public.users (display_name, handle, cognito_user_id)
VALUES
  ('Sheyi Gaji', 'sheyiuser' ,'MOCK'),
  ('Victoria Alata', 'vicky' ,'MOCK');

INSERT INTO public.activities (user_uuid, message, expires_at)
VALUES
  (
    (SELECT uuid from public.users WHERE users.handle = 'sheyiuser' LIMIT 1),
    'This was imported as seed data!',
    current_timestamp + interval '10 day'
  )

  chmod u+x ./db/seed.ql
  ./bin/db-seed
```
![seed data created](/_docs/assets/databse%20sheyi%20created.png)