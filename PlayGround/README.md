# Experimental and Playground Examples for Tosca API Simulator

## Overview

This directory contains experimental and playground examples that demonstrate various advanced features and usage patterns of the Tosca API Simulator. These examples showcase flexible routing configurations, complex message flows, and integration with external services, providing a sandbox for learning and experimenting with the simulator's capabilities.

## Features

- Configurable API routing with simulation fallback.
- Integration with real external services.
- Multi-step message flows with intermediate external calls.
- Test configurations for advanced scenarios.
- Different routing modes and forwarding strategies.

## Configuration Details

### demo-web-shop-router.yml

Demonstrates a routing configuration for a demo web shop application:

- **Port**: `28282`
- **Routing Mode**: `SimulateFirst`
- **Target Service**: Tricentis Demo Web Shop (`https://demowebshop.tricentis.com/`)
- **Functionality**: Routes requests through the simulator first, then to the real service if no simulation matches.

This example shows how to create a "`middleware`" layer that can selectively intercept and simulate specific API calls while forwarding others to the real service.

### out-message-in-the-middle.yaml

Demonstrates a complex message flow with an external API call:

- **Port**: `15555`
- **External API**: Cat Facts API (`https://catfact.ninja/fact`)
- **Flow**:
  1. Receives an incoming request.
  2. Makes an outgoing call to the Cat Facts API.
  3. Processes the response from Cat Facts API.
  4. Returns a custom response to the original requester.

This example showcases how to orchestrate multi-step workflows that include both incoming and outgoing messages, enabling complex simulation scenarios.

### tta.json

A simple configuration file used by test frameworks:

- **Sync Flag**: `true`
- **Test URL**: `http://localhost:12345`

This file is likely used for integration with test tools and demonstrates configuration for test automation frameworks.

## Usage Examples

### Running the Web Shop Router

```bash
# Make requests to the routed service
curl http://localhost:28282/some-endpoint
```

The requests will either be handled by simulations (if defined) or forwarded to the real Tricentis Demo Web Shop.

### Testing the Message Flow Example

```bash
# Make a request to trigger the flow
curl http://localhost:15555
```

The simulator will:

1. Receive your request.
2. Make an outgoing request to the Cat Facts API.
3. Return a custom response: "`This the response from API sim`".

## Advanced Concepts Demonstrated

### SimulateFirst Mode

The `SimulateFirst` routing mode attempts to match requests against defined simulations before forwarding to the real service:

```yaml
forward:
  mode: SimulateFirst
  to: realServiceEndpoint
```

This enables "intercepting" specific API calls for simulation while letting others pass through.

### Multi-Step Message Flows

By combining `In` and `Out` direction steps, complex workflows can be created:

```yaml
steps:
  - direction: In      # Receive request
    name: First in message
  - direction: Out     # Make external call
    to: externalEndpoint
  - direction: In      # Process external response
    from: externalEndpoint
  - direction: Out     # Send final response
    message:
      payload: Final response
```

### Testing Complex Flows

The examples include test services that validate the behavior of the simulations:

```yaml
- name: Invoke service
  steps:
    - direction: Out
      to: Test message endpoint
    - verify:
        - property: StatusCode
          value: "200 OK"
```
