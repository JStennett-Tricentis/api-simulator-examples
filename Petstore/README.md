# Petstore API Simulation

## Overview

This directory demonstrates how to simulate the popular Swagger Petstore API using the Tosca API Simulator. The examples showcase three different simulation strategies: `pure simulation`, `forward-first`, and `simulate-first` approaches. These examples are ideal for understanding how to create realistic REST API simulations for testing purposes, especially when working with third-party APIs like the Petstore.

## Features

- Complete REST API simulation.
- Multiple simulation strategies (`pure simulation`, `forward-first`, `simulate-first`).
- Dynamic response generation.
- Query parameter-based routing.
- File-based response templating.
- Random value generation.
- Error response handling.
- Request buffering for data reuse.
- Comprehensive test cases.

## Configuration Details

### petstore-service.yml

Implements a pure simulation approach with no forwarding to real services:

- **Port**: `23432`
- **Endpoints**:
  - `POST /v2/pet` - Add a new pet.
  - `GET /v2/pet/findByStatus` - Find pets by status.
- **Features**:
  - Random ID generation with `{RND[4]}`.
  - Request parameter buffering.
  - Status-based dynamic response selection.
  - JSON response templating.

### petstore-forward-first.yml

Demonstrates a forward-first strategy that prioritizes the real API:

- **Port**: `23555`
- **Strategy**: Forward to real API first, simulate only when real API returns `404`.`
- **Purpose**: Handles edge cases not covered by the real API.
- **Error Handling**: Provides custom error responses when the real API fails.

### petstore-simulate-first.yml

Shows a simulate-first approach with selective endpoint simulation:

- **Port**: `23444`
- **Strategy**: Simulate specific conditions first, forward other requests to real API.
- **Conditions**:
  - Simulates `status=available` and `status=sold` cases.
  - Forwards all other status values to the real service.
- **Request Routing**: Uses query parameters to decide between simulation and forwarding.

### pet-test.yml

Provides comprehensive test cases for the petstore API:

- **Test Cases**:
  - Adding a new pet.
  - Finding pets by status.
- **Verification**: Checks for correct status codes and response times.
- **Custom Assertions**: Uses regex to validate response timing.

## Data Files

The `/data` directory contains JSON files representing different response scenarios:

- **available.json**: Response for available pets.
- **sold.json**: Response for sold pets.
- **pending.json**: Response for pending pets.
- **error.json**: Custom error response with helpful messages.

## Simulation Strategies

### Pure Simulation

Best for when you need complete control over responses:

```yaml
services:
  - name: /v2/pet - POST - addPet
    steps:
      - buffer:
          - jsonPath: name
            name: petName
        # Additional configuration...
      - direction: Out
        insert:
          - jsonPath: id
            value: "{RND[4]}"
          - jsonPath: name
            value: "{B[petName]}"
        # Response configuration...
```

### Forward-First Simulation

Ideal for extending a real API with custom behaviors:

```yaml
connections:
  - name: petFFCon
    port: 23555
    forward:
      mode: ForwardFirst
      simulateOn:
        statusCodes: 404
      to: realPetstoreEndpoint
```

### Simulate-First Simulation

Perfect for selective simulation of specific API endpoints:

```yaml
connections:
  - name: petFailCon
    port: 23444
    forward:
      to: realPetstoreEndpoint
      mode: SimulateFirst

services:
  - name: /v2/pet - GET - findByStatusAVAILABLE
    steps:
      # Configuration for simulating specific status
```

## Usage Examples

### Testing the Add Pet Endpoint

```bash
# Add a new pet
curl -X POST http://localhost:23432/v2/pet \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Fluffy",
    "status": "available"
  }'
```

### Finding Pets by Status

```bash
# Get available pets
curl -X GET http://localhost:23432/v2/pet/findByStatus?status=available

# Get sold pets (when using simulate-first approach)
curl -X GET http://localhost:23444/v2/pet/findByStatus?status=sold
```

## Implementation Notes

### Dynamic Response Selection

The core feature enabling status-based responses:

```yaml
insert:
  - file: ./Petstore/data/{B[petStatus]}.json
```

This expression uses the buffered status value to select the correct JSON response file.

### Query Parameter Extraction

To simulate different responses based on query parameters:

```yaml
buffer:
  - key: status
    name: petStatus
    type: Query
```

### Conditional Triggers

For selective request handling:

```yaml
trigger:
  - type: Path
    value: /v2/pet/findByStatus
  - property: Method
    value: GET
  - type: Query
    key: status
    value: available
```
