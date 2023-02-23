#python image from docker to be used in the container
FROM python:3.10-slim-buster

# Inside the container
# make a new folder inside the container
WORKDIR /backend-flask

# outisde the container -> Inside the container
# contains the libraries that wants to install to run the app
COPY requirements.txt requirements.txt

# Inside the container
# Install the python libraries used for the app
RUN pip3 install -r requirements.txt

# Outside container -> Inside container
# . means everything in d current directory
# first period  . - /backend-flask (outside container)
# second period . /backend-flask (inside container)
COPY . .

# set environment variables(env var)
# Inside the container and will remain set when d container is runnig
ENV FLASK_ENV=development

EXPOSE ${PORT}

# CMD (Command)
# python3 -m flask run --host=0.0.0.0 --port=4567
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]