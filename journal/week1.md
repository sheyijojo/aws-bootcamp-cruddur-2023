# Week 1 — App Containerization

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
The "WORKDIR /backend-flask" is a directory inside container
`--host=0.0.0.0` exposes this server to the public
flask loves the 4567... notes coming on thisVAR
Deactivate Env
First check/search if present with grep `env | grep BACKEND` AND `env | grep FRONTEND` AND grep `env | grep _URL`
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
