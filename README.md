# Automated Crypto Trading
This repository contains a template for an application designed for
* Extracting real-time trading data from a crypto exchange
* Making trading decisions base on the extracted data 
* Persisting the extracted data in a database optimized for time-series data
* Visualizing time-series trading data

We will use the [Kraken](https://kraken.com) crypto exchange as source for data and for performing
trading operations, since we find Kraken to be a reliable, stable, trustworthy and fairly
well documented platform. If you prefer to use another crypto exchange, parts of the code in this
repository need to be replaced with similar code for interacting with the API of the alternative
exchange, but a lot of the code presented here will still be applicable.

The functionality of the code will be explained in detail both as textual descriptions and in a
number of YouTube videos.

Will this project make you a billionaire? I don't know, but we will provide you with a lot of
handy components useful for doing automated crypto trading - all you have to do is to decide which
trading rules you want to implement.

Let's dive in!

## Prerequisites
Before moving on we recommend that you obtain a little knowledge within these technologies, if you
have not already worked with them:
* The [Python](https://python.org) programming language
* [Docker](https://www.docker.com) and [Docker Compose](https://docs.docker.com/compose/)
* Databases and SQL (especially [PostgreSQL](https://www.postgresql.org))
* HTTP and RESTful services
* Websockets
* AMQP (Advanced Message Queuing Protocol)
* (Optional) [Kubernetes](https://kubernetes.io) and [Helm charts](https://helm.sh)

If this seems overwhelming - don't worry. We will explain things the best we can along the way -
and feel to ask questions, raise issues or make pull requests if something is on your mind. Maybe
we will even do a video on the topic you are addressing.

## Design

A microservice approach will be followed to compose the design. The components of the solution are made
of a number of microservices which can be seen in the figure below: 

<p align="center">
    <img alt="The design" src="https://docs.google.com/drawings/d/e/2PACX-1vQsBIpXyLQ0pqOt92D-pfl-pdb5JeeSni1hJWXRcd7hyhbXqjwRs6zTkXZ3AndBj6GZVLEY2buRtDMU/pub?w=708&h=450" style="background-color: white"/>
</p>

At the time of writing, the application consists of the following components:
* **KrakenConnector**: Python component responsible for connecting to the Kraken exchange via a websocket
  connection and listening to trading events. When an event is received, it is converted to a Python object,
  serialized and finally sent a queue in a message-broker.
* **Message Broker**: Message-broker system receiving formatted messages from the KrakenConnector and publishing
  these to a number of queues. Any application interested in the trading events can subscribe to the messages from
  the message-broker and act upon these. Here we use the [RabbitMQ](https://www.rabbitmq.com/) message-broker
  compatible with AMQP.
* **MassageToDB**: Python component receiving messages from the message-broker and persisting these to a database.
* **DB**: Database for persisting the trading events from the Kraken exchange as well as other data. The DBMS
  is [TimescaleDB](https://www.timescale.com/), which is a DBMS build on PostgreSQL optimized for handling
  time-series data.
* **Grafana**: A web-based platform for visualizing data in real-time. [Grafana](https://grafana.com/) is
  capable of connecting to the database and presenting the trading data in all kinds of different views. 