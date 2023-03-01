# Week 1 — App Containerization

References
[](https://hub.docker.com/_/python)

- Added the backend folder through my vscode application and reloaded my gitpod for sync
- Edited gitpod to configure dockfile in my backend

## Installation

Aim is to put the backend in a container

Run Python to start the server. Make sure to open this port on windows pc.

## merge git gitpod & vscode

This will merge them without problems
`git config pull.rebase false`

```sh
cd backend-flask
export FRONTEND_URL="*"
export BACKEND_URL="*"
python3 -m flask run --host=0.0.0.0 --port=4567
cd ..
```

Added a docker extension on gitpod by going to the extension icon and searhc for Docker
Install the dependencies and devDependencies and start the server.

## Add Dockerfile

created a dockerfile in `$ backend-flask/Dockerfile`
Docker can build images automatically by reading the instrcutions from a dockerfile
read more on dockefile here: [dockerfile](https://docs.docker.com/engine/reference/builder/)

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

### Test the commands on local machine(gitpod)
These commands are to be run one after the other
```sh
pip3 install -r requirements.txt
python3 -m flask run --hos=0.0.0.0--port=4567
```
Some env variables have to be activated
`export FRONTEND_URL = "*"`
`export BACKEND_URL = "*"`

## The "WORKDIR /backend-flask" is a directory inside container
`--host=0.0.0.0` exposes this server to the public
flask loves the 4567... notes coming on thisVAR

## Deactivate Env
First check/search if ENV is present with:
grep `env | grep BACKEND` AND 
`env | grep FRONTEND` AND 
grep `env | grep _URL`
```sh
unset BACKEND_URL
unset FRONTEND_URL
unset BACKEND
unset FRONTEND
```

## merge gitpod & vscode
- make sure to unlock the port on the port tab
- open the link for 4567 in your browser
- append to the url to /api/activities/home
- you should get back json


## Build container
Be in your project directory 
```sh
docker build -t backend-flask ./backend-flask
```
`![Docker build img](/_docs/assets/docker_build.png)`
- Code interpretation
- t - tags
- ./ - to my backend-flask and look for docker file
To see your images, go to your docker icon and check for backendflask

Building Docker from Desktop Instrucion coming soon.....
check for your images in your terminal
`docker images`
`docker build --help`

## Run Container 

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
## Code Explanation - docker run

`docker run --rm -p 4567:4567 -it backend-flask`
In this code, the enviroment value is not defined for the backend on the CLI


`docker run --rm -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flas`
In this code, environment variables are defined for backend and frontend on the CLI

`docker run --rm -p 4567:4567 -it  -e FRONTEND_URL -e BACKEND_URL backend-flask`
Here, environment variable is set, we are just to run code

## Log check
Always check the logs in the container for debugging
use attach shell in the container and get into the container 

This is bring you into the shell
check for env var
`env enter`
This will check if env is set

make sure you export env variables before running 
`--rm` when we stop the container, the img gets removed
`docker run --rm -p 4567:4567 -it  -e FRONTEND_URL -e BACKEND_URL backend-flask`
##### docker run

`docker run --rm -p 4567:4567 -it  -e FRONTEND_URL -e BACKEND_URL backend-flask`

`docker build --help`

- set the env variables with export on the CLI
```export FRONTEND_URL="*"``
``export BACKEND_URL="*"``
- run the container

``--rm    Automatically remove the container when it exits``
`` -p, --publish list    Publish a container's port(s) to the host``


got data from the url- api/activities/home

![Webdata-container](/_docs/assets/webdat-api.png)

`docker ps` List containers
'docker ps -a` Show all containers(default shows jut running)

##  Containerize Frontend

## Run NPM Install
`cd frontend-react-js`
`npm i`
write up on this:

## Create Docker File for the frontend
create DockerFile here: `frontend-react-js/Dockerfile`

## conterize the frontend with docker
```sh
FROM node:16.18

ENV PORT=3000

COPY . /frontend-react-js
WORKDIR /frontend-react-js
RUN npm install
EXPOSE ${PORT}
CMD ["npm", "start"]
```

## Multiple Containers
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
not specified yet 🤷

## dynamo db
The dynamodb-local service runs the ``amazon/dynamodb-local Docker image``, exposes ``port 8000``, and mounts a local directory ``./docker/dynamodb ``to the container's ``/home/dynamodblocal/data directory``, allowing for persistent storage of data.

## db service
The db service runs the ``postgres:13-alpine`` Docker image, exposes ``port 5432``, and sets an environment variable ``POSTGRES_USER and POSTGRES_PASSWORD``. The volume ``db ``is mounted to the container's ``/var/lib/postgresql/data`` directory, allowing for persistent storage of data.

The networks section defines a bridge network called internal-network with the name cruddur.

Finally, the volumes section defines a local volume named db.

## Summary for yaml file
This yaml file called docker-compose allows for multiple container spin for frontend, backend, dynamo db, and db.

## run the docker-compose yaml file

## BUILD CONTAINER
`docker build -t frontend-react-js ./frontend-react-js`

## RUN CONTAINER
`docker run -p 3000:3000 -d frontend-react-js`

