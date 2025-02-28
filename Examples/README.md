# Basic Examples for Tosca API Simulator

## Overview

This directory contains a collection of simple examples demonstrating the core functionality of the Tosca API Simulator. These examples provide a starting point for understanding how to configure and use the simulator for various testing scenarios.

Each example builds on concepts from previous examples while introducing new features.

## Examples Included

### Hello World

The simplest possible example showing a basic service that responds with a static message.

- **hello-world.yml**: Configures a service that responds with "`Hello world!`" to any request.
- **hello-world-test.yml**: Tests the `hello-world` service.

Features demonstrated:

- Basic connection setup with a port.
- Simple request/response handling.
- Basic trigger configuration.
- Simple response verification.

### Echo Service

Demonstrates how to echo back request data in responses.

- **echo.yml**: Creates a service that echoes back any input with a prefix.
- **echo-test.yml**: Tests the echo service with different types of inputs (text, numbers, JSON).

Features demonstrated:

- Buffer usage to store request data.
- Referencing buffer values in responses.
- Multiple test cases for different input types.

### Calculator

Shows how to implement mathematical operations and handle JSON payloads.

- **calculator.yml**: Implements a calculator API with four operations (`addition`, `subtraction`, `multiplication`, `division`).
- **calculator-test.yml**: Contains test cases for each operation, including edge cases.

Features demonstrated:

- `JSONPath` for data extraction.
- Mathematical expressions with the `{math[...]}` function.
- Multiple service endpoints in a single configuration.
- Testing edge cases (like division by zero).

### Conditional Cats

Demonstrates conditional forwarding based on request headers.

- **conditional-cats.yml**: Provides a virtual cat fact or forwards to a real cat facts API based on a header.
- **conditional-cats-test.yml**: Tests both modes of operation.

Features demonstrated:

- Conditional forwarding with `simulateOn`.
- Integration with external APIs.
- Header-based request routing.
- Testing different routing scenarios.
