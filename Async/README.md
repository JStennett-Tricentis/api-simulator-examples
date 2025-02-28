# Asynchronous Messaging in Tosca API Simulator

## Overview

This directory demonstrates how to implement and test asynchronous messaging patterns using the Tosca API Simulator. The examples showcase integration with two popular messaging systems: IBM MQ and Apache Kafka. Unlike synchronous `HTTP` requests, asynchronous messaging allows systems to communicate through message queues or topics without waiting for immediate responses, making it ideal for distributed systems, event-driven architectures, and scenarios requiring decoupling between systems.

## Features

- IBM MQ (Message Queue) integration.
- Apache Kafka with SSL support.
- Bidirectional message processing (consuming and producing).
- Message filtering with `JSONPath` expressions.
- Comprehensive test patterns for async services.
- Template-based connection configurations.
- Support for multiple messaging patterns.

## Configuration Details

### MQ Integration (mq.yml)

Demonstrates IBM MQ integration with the following components:

- **Connection Templates**: Reusable MQ connection configurations.
- **Queue Connections**:
  - Listening connections (for receiving messages).
  - Non-listening connections (for sending messages).
- **Services**:
  - A simulation service that listens for messages and responds.
  - A test service that sends messages and verifies responses.

```yaml
templates:
  connections:
    - name: MQconnection
      type: IbmMq
      channel: CH1
      manager: OSV
      endpoint: your.queuemanager.com

connections:
  - name: MQqueueIn
    basedOn: MQconnection
    listen: true
    queue: demo.in
```

### Kafka SSL Integration (kafka-ssl.yml)

Shows how to connect to Kafka topics with SSL security:

- **Client Properties**: Uses external properties file for SSL configuration.
- **Topic Connections**: Configurable topic and group ID.
- **Message Patterns**: JSON-based message exchange.
- **Security**: SSL configuration for secure communication.

```yaml
templates:
  connections:
    - name: kafkaSsl
      topic: rol2
      groupId: iris
      type: Kafka
      endpoint: 1.1.1.1:1111
      clientPropertiesFile: c:\workspaces\AsyncDemo\kafka-ssl\kafka-client.properties
```

### Testing Async Services (mq-test.yml)

Demonstrates how to test asynchronous messaging:

- **Comprehensive Test Cases**:
  - Happy path testing with expected data.
  - Testing with different input data.
  - Error case testing with malformed requests.
- **Message Verification**: JSONPath-based validation of responses.
- **Multiple Scenarios**: Multiple test services to cover different use cases.

## Message Flow

### IBM MQ Flow

1. A client application sends a message to the `demo.in` queue.
2. The API Simulator listens to the `demo.in` queue and processes messages matching specific criteria.
3. The simulator processes the message and sends a response to the `demo.out` queue.
4. A client application receives the response from the `demo.out` queue.

### Kafka Flow

1. A producer application sends a message to a Kafka topic.
2. The API Simulator consumes messages from the topic, filtering by content.
3. The simulator processes messages and produces new messages on the same or different topics.
4. Consumer applications read the simulator's responses from the topics.

## Usage Examples

### Setting Up MQ Simulation

1. Configure the template connection with your MQ server details:

```yaml
templates:
  connections:
    - name: MQconnection
      type: IbmMq
      channel: YOUR_CHANNEL
      manager: YOUR_QUEUE_MANAGER
      endpoint: your.queuemanager.com
```

2. Define the queues you want to interact with:

```yaml
connections:
  - name: MQqueueIn
    basedOn: MQconnection
    listen: true
    queue: your.input.queue
  - name: MQqueueOut
    basedOn: MQconnection
    listen: false
    queue: your.output.queue
```

3. Create services to process messages:

```yaml
services:
  - name: YourMessageProcessor
    steps:
      - direction: In
        from: MQqueueIn
        trigger:
          - jsonPath: $.someProperty
            value: expectedValue
      - direction: Out
        to: MQqueueOut
        message:
          payload: '{"status": "processed", "result": "success"}'
```

### Setting Up Kafka with SSL

1. Create a Kafka client properties file with your SSL configuration:

```properties
security.protocol=SSL
ssl.truststore.location=/path/to/truststore.jks
ssl.truststore.password=truststorepassword
ssl.keystore.location=/path/to/keystore.jks
ssl.keystore.password=keystorepassword
```

2. Configure the Kafka connection in your simulation:

```yaml
connections:
  - name: kafkaConnection
    topic: your-topic
    groupId: your-group
    type: Kafka
    endpoint: kafka-broker:9093
    clientPropertiesFile: /path/to/kafka-client.properties
```

## Test Implementation

The MQ test file demonstrates a structured approach to testing asynchronous services:

1. Basic happy path test that verifies correct responses to valid inputs.
2. Variant tests with different input values.
3. Error case tests with invalid or malformed messages.

These patterns provide a comprehensive testing strategy for asynchronous systems.
