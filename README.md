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

Let's dive in!

## Design

The components of the solution can be seen in the figure below:

<p align="center">
    <img alt="The design" src="https://docs.google.com/drawings/d/e/2PACX-1vQsBIpXyLQ0pqOt92D-pfl-pdb5JeeSni1hJWXRcd7hyhbXqjwRs6zTkXZ3AndBj6GZVLEY2buRtDMU/pub?w=708&h=450" style="background-color: white"/>
</p>

At the time of writing, the application consists of the following components:
* **KrakenConnector**: A Python component responsible for connecting to the Kraken exchange via a websocket
  connection and listening to trading events. When an event is received, it is converted to a Python object,
  serialized and finally sent a queue in a message-broker.
  * **Message Broker**: A message-broker system receiving formatted messages from the KrakenConnector and publishing
    these to a number of queues. Any application interested in the trading events can subscribe to the messages from
    the message-broker and act upon these. Here we use the [RabbitMQ](https://www.rabbitmq.com/) message-broker
    comppatible with AMQP.