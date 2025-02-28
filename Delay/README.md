# Response Delay Simulation in Tosca API Simulator

## Overview

This directory demonstrates how to implement delayed API responses in Tosca API Simulator. Simulating response delays is crucial for testing application behavior under various network conditions, timeout scenarios, and performance testing.

## Features

- Conditional response delays based on request content.
- Random delay intervals for realistic response time variation.
- Simple configuration using the `Delay` property.
- No code required to implement sophisticated timing behavior.

## Configuration Details

### delay-eval.yml

Demonstrates a conditional delay implementation:

- **Port**: `15555`
- **Service Structure**:
  - **"less than 10"**: Responds immediately when request number `≤ 10`.
  - **"greater than 10"**: Adds a random delay (200-800ms) when request number `≥ 11`.
  - **"SND Number Test"**: A test service that sends a number to trigger the delay.

- **Delay Implementation**:

  ```yaml
  - insert:
      - property: Delay
        value: "{RND[200][800]}"
  ```

## Usage Examples

### Basic Delay Configuration

```yaml
# Add a fixed 500ms delay to a response
- insert:
    - property: Delay
      value: "500"
  message:
    payload: |
      {"status": "delayed_response"}
```

### Random Delay

```yaml
# Add a random delay between 100ms and 2000ms
- insert:
    - property: Delay
      value: "{RND[100][2000]}"
  message:
    payload: |
      {"status": "variable_delay_response"}
```

### Conditional Delay

```yaml
# Apply delay only for specific request criteria
- trigger:
    - jsonPath: priority
      value: "low"
  insert:
    - property: Delay
      value: "1000"
  message:
    payload: |
      {"status": "low_priority_response"}
```
