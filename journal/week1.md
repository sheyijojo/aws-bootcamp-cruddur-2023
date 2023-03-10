# Week 1 — App Containerization

References
[](https://hub.docker.com/_/python)

- Added the backend folder through my vscode application and reloaded my gitpod for sync
- Edited gitpod to configure dockfile in my backend

## Installation

Aim is to put the backend in a container

Run Python to start the server. Make sure to open this port on windows pc.

```sh
cd backend-flask
export FRONTEND_URL="*"
export BACKEND_URL="*"
python3 -m flask run --host=0.0.0.0 --port=4567
cd ..
```
## Docker Extension
Add a docker extension on gitpod by going to the extension icon and searhc for Docker
Install the dependencies and Dev dependencies and start the server.

## Add Dockerfile

create a dockerfile in `$ backend-flask/Dockerfile`
Docker can build images automatically by reading the instrcutions from a dockerfile
read more here: dockefile here: [dockerfile](https://docs.docker.com/engine/reference/builder/)

```sh
FROM python:3.10-slim-buster

WORKDIR /backend-flask

COPY requirements.txt requirements.txt
RUN pip3 install -r requirements.txt

COPY . .

ENV FLASK_ENV=development

EXPOSE ${PORT}
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]
```

### Test the commands 👆  on local machine - gitpod or vscode
These commands are to be run one after the other
```sh
pip3 install -r requirements.txt
python3 -m flask run --host=0.0.0.0--port=4567

```

## result after running on local machine
This is done before running on docker. 
Image below is as a result of running 
`pip3 install -r requirements.txt`
`python3 -m flask run --host=0.0.0.0--port=4567`
`backend url = /api/activities/home`

![gitpod data](/_docs/assets/api-activities-local-machine.png)


## activate variables on local machine
`export FRONTEND_URL = "*"`
`export BACKEND_URL = "*"`

## The "WORKDIR /backend-flask" is a directory inside container
`--host=0.0.0.0` exposes this server to the public
flask loves the 4567... notes coming on thisVAR

## Deactivate Environment(optional)
First search if ENV is present with:

```sh
grep env | grep BACKEND OR
env | grep FRONTEND OR
grep env | grep _URL

```

```sh
unset BACKEND_URL
unset FRONTEND_URL
unset BACKEND
unset FRONTEND
```

## 
- make sure to unlock the port on the port tab
- open the link for 4567 in your browser
- append to the url to /api/activities/home
- you should get back json


## Build Backend container image
Be in your project directory: `/workspace/aws-bootcamp-cruddur-2023`

## Build backend images
```sh
docker build -t backend-flask ./backend-flask
```
![Docker build img](/_docs/assets/docker_build.png)

## Built images
- backend-flask
- python

![built images_docker](/_docs/assets/built_images.png)


## Code interpretation for backend images
```sh
docker build -t backend-flask ./backend-flask
```
- `docker build` - Build an image from a Dockerfile
- `-t`, --tag list   Name and optionally a tag in the 'name:tag' format
- `backend-flask` - 
- `./backend` - directory to my backend  to search for docker file
To see your images, go to your docker icon and check for backendflask

## Summary of build backend container image
Built an image from the dockerfile using docker build . Dockerfile contains the follwing:
- installing base image called python for backend slim buster `FROM pytgon:3.10-slim-buster`

- specify working directory already created `WORKDIR /backend-flask`

- copy requirements.txt from host machine(outside container) into backend dir inside the container ` COPY requirements.txt requirements.txt`

- Runs the required python libraries - `RUN pip3 install -r requirements.txt`

- `COPY . .`- This line copies the rest of the files and directories from the host machine to the backend-flask directory inside the container.

- `ENV FLASK_ENV=development`- This line sets an environment variable FLASK_ENV to development. This variable can be accessed and used by the Flask application running inside the container.

- what is inside requirements? `flask` and `flask-cors`

 - `EXPOSE ${PORT}`- This line exposes the port specified by the `${PORT}` environment variable. This allows external processes to communicate with the container over this port.

- `CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]` - This line specifies the command that will be executed when the container is started. In this case, it will start the Flask application by running the flask run command with python3. The --host=0.0.0.0 option allows external connections to be made to the container, and the --port=4567 option specifies the port on which the Flask application will listen.

## Building docker from desktop instruction coming soon.....

## check images in your terminal
`docker images`
`docker build --help`

## Create/run Container 
One container only? for backend and frontend? 🎅

#### Objective: 
- run a docker container with image created: `flask-backend image`

## check for containers 
`docker container ps --help `- show all the flags with ps
`docker container ps`- shpow running container 
`docker container ps -a` - show all containers(running or not)

## docker list of command. Not to be put in a docker file
- can put in a yaml file to run multiple containers 

```sh

docker run --rm -p 4567:4567 -it backend-flask
FRONTEND_URL="*" BACKEND_URL="*" docker run --rm -p 4567:4567 -it backend-flask
export FRONTEND_URL="*"
export BACKEND_URL="*"
docker run --rm -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask
docker run --rm -p 4567:4567 -it  -e FRONTEND_URL -e BACKEND_URL backend-flask
unset FRONTEND_URL="*"
unset BACKEND_URL="*"

```
## Step 1 Code Explanation - docker run backend
`docker run --rm -p 4567:4567 -it backend-flask`

In this code, the enviroment value is not defined for the backend on the CLI
` docker run` - Basic command to run docker contain or create container
` --rm` - flag tells  container to be removed automatically when it is stopped
`-p 4567:4567` - flag tells port 4567 on the host machine be mapped to port 4567 inside the container.
This allows the Flask app running inside the container to be accessed from outsode the container using host machine's IP and port number
`-it` - Allows user interaction with the shell with a pseudo-TTY
`backend-flask` - Name of the Docker image to be used to create container
## summary of code above 
Overall, this command will start a new container from the backend-flask image, map port 4567 to the host machine, and run the container in interactive mode. This will allow the Flask application running inside the container to be accessed from outside the container using the host machine's IP address and port number.


`docker run --rm -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flas`
In this code, environment variables are defined for backend and frontend on the CLI

`docker run --rm -p 4567:4567 -it  -e FRONTEND_URL -e BACKEND_URL backend-flask`
Here, environment variable is set, we are just to run code

## Debugging Container
#### log check GUI
Always check the logs in the container for debugging, 
use attach shell in the container and get into the container 

#### log check CLI
`docker container logs <container_id>`

#### attach shell GUI
![attach_shell](/_docs/assets/attachshell.png)
This is bring you into the shell(@root127666:/backend-flask#)

check for env variable inside the shell with this command : `env enter`
This will check if env is set


## docker run with env variables

#### run multiple container with set ENV variables
#### set the env variables with export on the CLI
```export FRONTEND_URL="*"``
``export BACKEND_URL="*"``
#### run the container
`docker run --rm -p 4567:4567 -it  -e FRONTEND_URL -e BACKEND_URL backend-flask`
`docker build --help`

``--rm    Automatically remove the container when it exits``
`` -p, --publish list    Publish a container's port(s) to the host``

## got data from the url- api/activities/home

![Webdata-container](/_docs/assets/webdat-api.png)

`docker ps` List containers
`docker ps -a` Show all containers(default shows jut running)

##  Containerize Frontend

## Run NPM Install
`cd frontend-react-js`
`npm i`
write up on this:
# Summary of the `npm i`
This command runs the npm command to install the dependencies specified in the package.json file for the React.js application. 

This command will download and install all the required dependencies and store them in a node_modules directory in the same directory as the package.json file. These dependencies may include various React.js libraries, CSS frameworks, or other packages that the application depends on.

Once these two commands are run, the necessary dependencies for the React.js application should be installed and the application should be ready to run

## Create Docker File for the frontend
create DockerFile here: `frontend-react-js/Dockerfile`

## contenarize the frontend with docker

## Create docker file for frontend: `Dockerfile`
```sh
FROM node:16.18

ENV PORT=3000

COPY . /frontend-react-js
WORKDIR /frontend-react-js
RUN npm install
EXPOSE ${PORT}
CMD ["npm", "start"]
```

`FROM node:16.18` - Base node image with version 
`ENV PORT=3000` - set environmental variable PORT=3000
`COPY . /frontend-react-js - copy all files from d current directory into a new directory called frontend-react-js. 
`WORKDIR`-  working directory to find dockerfile

## Multiple Containers with yaml file
create `docker-compose.yml` at the root of the project ``/workspace/aws-bootcamp-cruddur-2023/docker-compose.yml``

```sh
version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./frontend-react-js
    ports:
      - "3000:3000"
    volumes:
      - ./frontend-react-js:/frontend-react-js
  dynamodb-local:
    # https://stackoverflow.com/questions/67533058/persist-local-dynamodb-data-in-volumes-lack-permission-unable-to-open-databa
    # We needed to add user:root to get this working.
    user: root
    command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"
    image: "amazon/dynamodb-local:latest"
    container_name: dynamodb-local
    ports:
      - "8000:8000"
    volumes:
      - "./docker/dynamodb:/home/dynamodblocal/data"
    working_dir: /home/dynamodblocal

  db:
    image: postgres:13-alpine
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    ports:
      - '5432:5432'
    volumes: 
      - db:/var/lib/postgresql/data

# the name flag is a hack to change the default prepend folder
# name when outputting the image names
networks: 
  internal-network:
    driver: bridge
    name: cruddur

volumes:
  db:
    driver: local
```

## run docker with yml
``docker compose up``

## Observation
running all the yaml files in the root folder will create images and containers that includes the root folder

## all containers after `docker compose up`

![all container](/_docs/assets/all_containers.png)

# all ports after `docker compose up`
![all port](/_docs/assets/all_ports.png)


## Code
This code above 👆 is a `docker compose.yml` file written in version 3.8. It is used to run multiple containers. It defines 4 services:
- backend-flask
- frontend-react-js
- dynamodb-local
- db 
Each service is a containerized application that can run simultaneously/together virtuallly.


## backend-flask container. 
The ``backend-flask``service builds an image using the Dockerfile in the ``./backend-flask`` directory, exposes port ``4567``, and sets an environment variable ``FRONTEND_URL`` and ``BACKEND_URL`` using Gitpod workspace ID and cluster host. 



## name of backend-flask container 
aws-bootcamp-crudder-2023-backend-flask-1(2f2f7a34....)

## image of backend-flask container
aws-bootcamp-crudder-2023-backend-flask

## image to be created from the script above 👆
`backend-flask`
code for this 
```sh 
services:
  backend-flask:
    enviroment:
  build: ./backend-flask

```
## frontend-flask container.
The frontend-react-js service builds an image using the ``Dockerfile`` in the ``./frontend-react-js directory``, exposes port 3000, and sets an environment variable ``REACT_APP_FRONTEND_URL`` using the Gitpod workspace ID and cluster host.

## name of frontend-flask container 
aws-bootcamp-crudder-2023-frontend-react-js-1(330000....)


## image of frontend-flask container
not specified yet 🤷- Actually is in the yaml file as above here

## Observation
running all the yaml file in the root folder will create images and containers that includes the root folder

## ROOT folder
``aws-bootcamp-cruddur-2023``
All container gets destroyed when stopped, but previously created images remain the same. I.e Image don't need to be downlaoded from the internet all over again. 

## dynamo db
The dynamodb-local service runs the ``amazon/dynamodb-local Docker image``, exposes ``port 8000``, and mounts a local directory ``./docker/dynamodb ``to the container's ``/home/dynamodblocal/data directory``, allowing for persistent storage of data.

## db service
The db service runs the ``postgres:13-alpine`` Docker image, exposes ``port 5432``, and sets an environment variable ``POSTGRES_USER and POSTGRES_PASSWORD``. The volume ``db ``is mounted to the container's ``/var/lib/postgresql/data`` directory, allowing for persistent storage of data.

The networks section defines a bridge network called internal-network with the name cruddur.

Finally, the volumes section defines a local volume named db.

## Summary for yaml file
This yaml file called docker-compose allows for multiple container spin for frontend, backend, dynamo db, and db.

## image of backend and frontend communication with data 
![backend_frontend](/_docs/assets/backend_frontend%20docker.png)

# Individual Containers
- amazon dynamodb
- aws-backend
- aws-frontend
- postgres


## BUILD Individual frontend CONTAINER without multi container yaml
`docker build -t frontend-react-js ./frontend-react-js`

## RUN individual frontendCONTAINER 
`docker run -p 3000:3000 -d frontend-react-js`

# Document the Notification Endpoint for the OpenAI Document
#### STEP 1
- Launch your container 
- open the openapi.yml file in frontend `openapi-3.0.yml`

#### Add open api extention
![open Api](/_docs/assets/openapi_swagger.png)

#### STEP 2
- Open the api file and click on the extension
- open api is good for documentating an api
![api_up](/_docs/assets/api_up.png)

#### STEP 3
- `docker compose up`

#### Debugging
- debug with logs on your container if it is not running. 
- It could be some dependencies need installation.

#### STEP 4 - Add an Endpoint

##### Open API specification link to see response objects or any other objects
[](https://spec.openapis.org/oas/v3.0.3)

#### STEP 5 - Add new path through the api extention and eidt on openapi file

- eidited the app.py, and added notifications_activities
![backend end point](/_docs/assets/backend%20endpoint.png)


#### Front end point 
- Done

# Write a Flask Backend Endpoint for Notifications
- Done

# Write a React page for Notifications
- Done

# Run DynamoDB local container and ensure it works
- Done

# Run Postgress container and make sure it works
- Done

#### Adding Postgres

##### Containerize postgress

- Postgres is already integrated in our docker compose file:
- We can bring them into containers and reference them externally. Incase we want to use them in our local machine.

```sh
services:
  db:
    image: postgres:13-alpine
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    ports:
      - '5432:5432'
    volumes: 
      - db:/var/lib/postgresql/data
volumes:
  db:
    driver: local

```
#### install the postgress client into local machine(in this case, Gitpod)

```sh
- name: postgres
    init: |
      curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
      echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list
      sudo apt update
      sudo apt install -y postgresql-client-13 libpq-dev
```

#### containerize DynamoDB local

```sh
services:
  dynamodb-local:
    # https://stackoverflow.com/questions/67533058/persist-local-dynamodb-data-in-volumes-lack-permission-unable-to-open-databa
    # We needed to add user:root to get this working.
    user: root
    command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"
    image: "amazon/dynamodb-local:latest"
    container_name: dynamodb-local
    ports:
      - "8000:8000"
    volumes:
      - "./docker/dynamodb:/home/dynamodblocal/data"
    working_dir: /home/dynamodblocal
```

#### search for dynamo db on the internet

#### check 100 days of cloud
[](https://github.com/100DaysOfCloud/challenge-dynamodb-local)

#### Create table Dynamodb
- paste in terminal with aws cli
```sh
aws dynamodb create-table \
    --endpoint-url http://localhost:8000 \
    --table-name Music \
    --attribute-definitions \
        AttributeName=Artist,AttributeType=S \
        AttributeName=SongTitle,AttributeType=S \
    --key-schema AttributeName=Artist,KeyType=HASH AttributeName=SongTitle,KeyType=RANGE \
    --provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1 \
    --table-class STANDARD
```
![dynamo db table](/_docs/assets/create%20table_dynamodb.png)


#### Create an Item

```sh
aws dynamodb put-item \
    --endpoint-url http://localhost:8000 \
    --table-name Music \
    --item \
        '{"Artist": {"S": "No One You Know"}, "SongTitle": {"S": "Call Me Today"}, "AlbumTitle": {"S": "Somewhat Famous"}}' \
    --return-consumed-capacity TOTAL  

```

#### List Tables - Music

``aws dynamodb list-tables --endpoint-url http://localhost:8000  ``

#### get music records

`` aws dynamodb scan --table-name Music --query "Items" --endpoint-url http://localhost:8000``

#### Dynamo DB is good to go. Working perfectly


#### Postgres Running  5432

#### Install driver for Postgress

```sh
 curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
      echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list
      sudo apt update
      sudo apt install -y postgresql-client-13 libpq-dev
vscode:
```

#### install postgrel extension
![postgre extension](/_docs/assets/postgre.png)

### start progress client

``psql -Upostgres --host localhost``
``\l`` - list default table
``\q`` - quit