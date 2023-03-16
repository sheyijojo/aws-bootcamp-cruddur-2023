# Week 2 — Distributed Tracing

## Homework Challenges

- [ ] Instrument Honeycomb for the frontend-application to observe network latency between frontend and backend

- [ ] Add custom instrumentation to Honeycomb to add more attributes eg. UserId, Add a custom span

- [ ] Run custom queries in Honeycomb and save them later eg. Latency by UserID, Recent Traces

#### Distributed Tracing Intro
[YouTube link](http://localhost/ "link title")

#### What the heck is distributed tracing?

- Distributed tracing is a method of tracking application requests as they flow from frontend devices to backend services and databases. Developers can use distributed tracing to troubleshoot requests that exhibit high latency or errors. 
source: [datadog](https://www.datadoghq.com/knowledge-center/distributed-tracing/#:~:text=Distributed%20tracing%20is%20a%20method,exhibit%20high%20latency%20or%20errors.)
- Traces is what is needed for development! 

#### What the heck is Instrumentation?
Instrumentation is the data that sends the code that makes these traces. e.g Amazon x-ray, Honeycomb

#### create environment on Honeycomb - bootcamp
- protect your api keys
- data can be deposited into your ENV if API key is found.


## Honeycomb Setup 
#### Overview:
```sh
export HONEYCOMB_API_KEY=""
export HONEYCOMB_SERVICE_NAME="Cruddur"
gp env HONEYCOMB_API_KEY=""
gp env HONEYCOMB_SERVICE_NAME="Cruddur"
```
- #### API key set up, standardized for honeycomb
-  Get API key from honeycomb website:
 - ``export HONEYCOMB_API_KEY=""``
 - Put in your api key in here:
 - ``gp env HONEYCOMB_API_KEY=""``
 - gp env is persistent for gitpod whenever it restarts the env


 #### why export env is important
 Subshell and shell reason when running commands.

#### set up service name specific to a service e.g backend or frontend
##### This example is not the best, it is better to hardcode it into our OTEL in the Docker compose environment
- `export HONEYCOMB_SERVICE_NAME="backend-flask`
- `gp env HONEYCOMB_SERVICE_NAME="backend-flask"`
- otel service name is the standard
 - `OTEL_SERVICE_NAME: "${HONEYCOMB_SERVICE_NAME}"`
- Otel service name determines the service name in the spans that gets sent from my application. 
- We want different service name for multiple back end services sending traces to honeycomb in a docker compose file, don't want the name consistenet accross. 

#### Backend Solution to setting up service from above

- Hard code it into docker compose file 
- We dont to set ```export HONEYCOMB_SERVICE_NAME="Cruddur``` directly, it is specific to a service.
- set `OTEL_SERVICE_NAME: "${HONEYCOMB_SERVICE_NAME}"` in the docker-compose file in the service env with a service name 
- Entire project should have honeycombAPI key
- Not as easy for the frontend, especially automatic instrumentations`OTEL_SERVICE_NAME: "${backend-flask}"backend-flask`

![otel](/_docs/assets/otelservice.jpg)




##### OTEL - OPEN TELEMETRY
Configuring OTEL to send to Honeycomb. The libs installing our app is opensourced.They are from the OTEL project(part of CNCF- Cloud Native Foundations), which runs kubernetes.

#### Add to the backend environment on docker-compose file
`cd backend-flask`
```sh
OTEL_EXPORTER_OTLP_ENDPOINT: "https://api.honeycomb.io"
OTEL_EXPORTER_OTLP_HEADERS: "x-honeycomb-team=${HONEYCOMB_API_KEY}"
OTEL_SERVICE_NAME: "${HONEYCOMB_SERVICE_NAME}"
```

#### What is happening above ? Honeycomb is not in cloud environment
Honeycomb is not in my cloud environment. Cloud env is sending standardized messages out to honeycomb, honeycomb stores in our database and gives a ui to look at them.
we could can change this configs and send to other OTEL backend. Not tied to honeycomb alone.

#### Add to instrumentation requirements - code from honeycomb python

```sh
opentelemetry-api 
opentelemetry-sdk 
opentelemetry-exporter-otlp-proto-http 
opentelemetry-instrumentation-flask 
opentelemetry-instrumentation-requests
```

#### install instrumentation packages in requirements
`pip install -r requirements.txt`

#### Add to app.py
```sh
# app.py updates
from opentelemetry import trace
from opentelemetry.instrumentation.flask import FlaskInstrumentor
from opentelemetry.instrumentation.requests import RequestsInstrumentor
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

```
#### intialize tracing instrumentations and an exporter that can send data to honeycomb

```sh
provider = TracerProvider()
processor = BatchSpanProcessor(OTLPSpanExporter())
provider.add_span_processor(processor)
trace.set_tracer_provider(provider)
tracer = trace.get_tracer(__name__)
```
#### Initialize automatic instrumentation with Flask

```sh
app = Flask(__name__)
FlaskInstrumentor().instrument_app(app)
RequestsInstrumentor().instrument()
```

![instrumentation 1](/_docs/assets/instrumentation1.png)
![instrumentation 1](/_docs/assets/instrumentation1.png)

#### Docker File in Devlopment and Production
- In real life we don't use the same docker file for development and production.
- This is because we use a different base image 
- In development we have all our tools, e.g we want VIM, Ubuntu, ssh and so on. These are not light-weight
- In production, we want it super slim or light weight. We don't want all these extras installed
- It is more secured, faster to exclude all these extras. Smaller images

#### Make ports open and public always on gitpod 
```sh
ports:
  - name: frontend
    port: 3000
    onOpen: open-browser
    visibility: public
  - name: backend
    port: 4567
    visibility: public
  - name: xray-daemon
    port: 2000
    visibility: public
```


#### Test the Honeycomb instrumentations added
- `cd frontend` to run `npm i install` t
- `cd  ..` `to compose up`
- Accessed frontend3000 and backend3000 to get data

#### Make a data set on Honeycomb
Data is automatically created

#### Send spans to console using mock-data  for testing in app.py
- shows logs within the backend-flask app(SDOUT)
- HoneyComb -----------------
-  add another processor, this was used when trying to capture data on honeycomb
- Console is sending spans to the console
``simple_processor = SimpleSpanProcessor(ConsoleSpanExporter())``
``provider.add_span_processor(simple_processor)``

#### add this to import the modules
``from opentelemetry.sdk.trace.export import ConsoleSpanExporter, SimpleSpanProcessor``

#### Span data on console 
![Telemetry span on console](/_docs/assets/telemetry_console.png)


#### API key difference in ENV Debugging
##### GLITCH
``env | grep HONEY``
- I observed the API key gotten was for the Test Env and not the Bootcamp env created in Honeycomb
``https://honeycomb-whoami.glitch.me/``
- Check what service the api can do and not do.


#### Fortunately, my api key was mapped to Honeycomb in my docker env.
![api_test](/_docs/assets/api_test.jpg)
 

#### This is for setting the key variable incase
`gp env HONEYCOMB_API_KEY="APIKEY"`

##### Span data from Honeycomb in Bootcamp env
![span data](/_docs/assets/span%20data%20honeycomb.png)

- Api end point 'home' is just exemplyfies the twitter feeds and it is hard coded
- It is not connected to a databse, or another service. But in real life the databse should be connected

#### Hardcode span to represent. We are importing to add only the api
``honeycomb python docs``
``https://docs.honeycomb.io/getting-data-in/opentelemetry/python/``
- Every service is hardcoded and returns a result.
- So we need to edit a service called `home_activities.py`

#### acquiring a Tracer
#### To create spans, you need to get a Tracer.
``from opentelemetry import trace ``
``tracer = trace.get_tracer("home.activities")``

#### Service Tracer
![service tracer](/_docs/assets/tracerhomeactivities.png)

#### create a span
- Add this to your def run(function ) in activities
`with tracer.start_as_current_span("http-handler"):`
#### Rename the span
- This is the name of the span
`with tracer.start_as_current_span("home-activities-mock-data"):`

#### Span mock-data after debugging services: home_activities.py
![api_mock_data](/_docs/assets/mock-data.png)

#### search for library names on Honeycomb
- This tells us about where our spans are coming from
- e.g Home/activties and opentelemetry.instrumentation.flask

#### Creating special spans with attributes Spans can be replacement for logs 
- check documentation
- placed into servives e.g home_activities
``span = trace.get_current_span()``
``span.set_attribute("user.id", user.id())``

#### with my data. Two attributes meaningful to me
``span = trace.get_current_span()``
``span.set_attribute("app.now", now.isoformat())``
``span.set_attribute("app.result_length", len(results))``

#### app.now is the new attribute
- This is seen on honycomb
- Create new query `count`
- `count where= emppy groupy = trace.trace_id`

`git tag week-2`
`git push --tags`