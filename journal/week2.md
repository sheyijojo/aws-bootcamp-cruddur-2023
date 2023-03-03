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
Instrumentation is the data that sends the code that makes these trace. e.g Amazon x-ray, Honeycomb

#### create environment on Honeycomb - bootcamp
- protect your api keys
- data can be added to


## Honeycomb Setup 
#### Overview:
```sh
export HONEYCOMB_API_KEY=""
export HONEYCOMB_SERVICE_NAME="Cruddur"
gp env HONEYCOMB_API_KEY=""
gp env HONEYCOMB_SERVICE_NAME="Cruddur"
```
- #### API key set up, standardized for honeycomb
- Get API key from honeycomb website:
 - ``export HONEYCOMB_API_KEY=""``
 - Put in your api key in here:
 - ``gp env HONEYCOMB_API_KEY=""``
 - gp env is persistent for gitpod wheneve it restarts the env


 #### why export env is important
 Subshell annd shell reason when running commands.

#### set up service name
- `export HONEYCOMB_SERVICE_NAME="Cruddur`
- `gp env HONEYCOMB_SERVICE_NAME="Cruddur"`
- otel service name is the standard
 - `OTEL_SERVICE_NAME: "${HONEYCOMB_SERVICE_NAME}"`
- Otel service name determines the service name in the spans that gets sent from my application. 
- We want different service name for multiple back end services sending traces to honeycomb in a docker compose file, don't want the name consistenet accross. 

#### Backend Solution to setting up service from above

- Hard code it into docker compose file 
- We dont to set ```export HONEYCOMB_SERVICE_NAME="Cruddur``` directly, it is specific to a service.
- set `OTEL_SERVICE_NAME: "${HONEYCOMB_SERVICE_NAME}"` in the docker-compose file in the service env with a service name `OTEL_SERVICE_NAME: "${backend-flask}"backend-flask`

[otel](/_docs/assets/otelservice.jpg)

- Entire project should have honeycombAPI key
- Not as easy for the frontend, especially automatic instrumentations


##### OTEL - OPEN TELEMETRY
Configuring OTEL to send to Honeycomb. The libs installing our app is opensource.They are from the OTEL project(part of CNCF- Cloud Native Foundations), which runs kubernetes

#### Add to the backend environment on docker-compose file
`cd backend-flask`
```sh
OTEL_EXPORTER_OTLP_ENDPOINT: "https://api.honeycomb.io"
OTEL_EXPORTER_OTLP_HEADERS: "x-honeycomb-team=${HONEYCOMB_API_KEY}"
OTEL_SERVICE_NAME: "${HONEYCOMB_SERVICE_NAME}"
```

#### What is happening above ? Honeycomb not in cloud environment
Honey comb is not in my cloud environment. Cloud env is sending standardized messages out to oneycomb, honeycomb stores in our database and gives a ui to look at them.
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

#### intialize instrumentations inside app.py
```sh
# app.py updates
from opentelemetry import trace
from opentelemetry.instrumentation.flask import FlaskInstrumentor
from opentelemetry.instrumentation.requests import RequestsInstrumentor
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

```



