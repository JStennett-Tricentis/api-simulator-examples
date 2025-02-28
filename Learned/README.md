# Learning Mode in Tosca API Simulator

## Overview

This directory demonstrates the "Learning Mode" feature of the Tosca API Simulator. Learning Mode allows the simulator to act as a proxy that records real API interactions, enabling you to create accurate simulations based on actual request/response patterns without manually defining them.

## Features

- Automatic recording of API interactions.
- Request/response pattern capturing.
- Configurable learning patterns for paths and payloads.
- Organized storage of learned interactions.
- Echo service integration for demonstration.

## Configuration Details

### sorting-connection.yml

Sets up a learning connection with the following configuration:

- **Listening Port**: `27074`
- **Forwarding To**: An echo service on port `27073`.
- **Learning Configuration**:
  - Records interactions to `learned/service-a.yml`.
  - Captures patterns for both Path and Payload.
  - Commented examples show how to selectively record based on URI patterns.

### service-a.yml

Contains the recorded API interactions:

- Multiple endpoint paths recorded:
  - `/resourceA/alpha`
  - `/resourceA/beta`
  - `/resourceB/gamma`
  - `/resourceB/delta`
  - `/favicon.ico`

- Each recorded interaction includes:
  - The trigger conditions (path and payload).
  - The complete response with headers, status code, and payload.

## How Learning Mode Works

1. **Proxy Setup**: The simulator acts as a proxy between the client and the real service.
2. **Request Forwarding**: Client requests are forwarded to the real service.
3. **Response Capture**: The real service's responses are captured and sent back to the client.
4. **Pattern Recording**: The simulator records the request patterns and corresponding responses.
5. **Simulation Generation**: The recorded patterns are saved to a YAML file for future use.

## Usage Examples

### Recording API Interactions

```bash
# Make requests to the proxy endpoint to be recorded
curl http://localhost:27074/resourceA/alpha
curl http://localhost:27074/resourceA/beta
curl http://localhost:27074/resourceB/gamma
```

### Using the Recorded Simulation

```bash
# The simulator will now respond based on the recorded patterns
curl http://localhost:YOUR_PORT/resourceA/alpha
```

## Advanced Configuration Options

You can customize the learning behavior with these options:

```yaml
learning:
  # Record different paths to different files
  learnOn:
    - uri: "/resourceA"
      file: API-A.yml
    - uri: "/resourceB*"
      file: API-B.yml

  # Or use a single file with a template
  pathTemplate: Learned/service-a.yml

  # Specify which patterns to match on
  patterns:
    - type: Path
    - type: Payload
    - type: Method
```
