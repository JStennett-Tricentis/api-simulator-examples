# Dynamic State Management in Tosca API Simulator

## Overview

This directory demonstrates how to implement stateful API simulations using the Tosca API Simulator. The examples show how to maintain state across multiple API requests using buffers, enabling the simulation of complex workflows like order processing systems.

## Features

- State persistence across multiple API requests.
- Dynamic ID generation with random values.
- Conditional request matching based on stored state.
- Sequential workflow simulation.
- Path parameter extraction and reuse.

## Configuration Details

### dynamic-state.yml

Demonstrates a stateful order processing service:

- **Port**: `14323`
- **Service**: "Virtual Order Service"
- **Workflow**:
  1. Create order and generate random order ID.
  2. Initiate shipping for the order.
  3. Update payment status.
  4. Complete the order.

- **State Management Implementation**:

```yaml
# Store state in buffer
- buffer:
    - name: id
    value: "{RND[10]}"

# Reference state in subsequent requests
- trigger:
    - uri: /update/{B[id]}
```

### invoke-state.yml

Provides test cases for the dynamic state service:

- **Connection**: Points to the dynamic state service on port `14323`.
- **Test Services**:
  - "Order Service Tests": Tests order creation.
  - "Update Service Tests": Tests order status updates.

## Usage Examples

### Creating a Stateful Service

```yaml
# Generate and store a unique ID
- trigger:
    - uri: /create
  buffer:
    - name: id
      value: "{RND[10]}"

# Return the ID to the client
- insert:
    - value: "Your Order ID is {B[id]}"

# Match subsequent requests using the stored ID
- trigger:
    - uri: /update/{B[id]}
```

### Testing a Stateful Service

```yaml
# Create an order
- direction: Out
  to: order service endpoint
  insert:
    - type: Path
      value: /create

# Use the returned order ID to update status
- direction: Out
  to: order service endpoint
  insert:
    - type: Path
      value: /update/RETURNED_ORDER_ID
```
