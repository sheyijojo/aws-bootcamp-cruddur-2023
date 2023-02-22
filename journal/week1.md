# Week 1 — App Containerization

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
