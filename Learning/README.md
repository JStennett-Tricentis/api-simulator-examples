# Learning Configuration in Tosca API Simulator

## Overview

This directory demonstrates how to configure the Learning Mode in Tosca API Simulator for different API types. Unlike the `./Learned` directory which shows the output of learning mode, these examples focus on various ways to set up learning connections to capture real API interactions from external services.

## Features

- Learning setup for different API types (`REST`, `JSON`, Query Parameters).
- Pattern matching configuration options.
- Connection to real external APIs for learning.
- Custom pattern extraction using `JSONPath`.
- Resource-based organization of learned interactions.

## Configuration Details

### echo-learning.yml

Demonstrates learning from a JSON-based API:

- **Target API**: Postman Echo API (`https://postman-echo.com/post`)
- **Port**: `14455`
- **Learning Configuration**:
  - Sorts learned interactions by resource.
  - Captures patterns based on `JSON` fields using `JSONPath`:
    - `message` field
    - `userId` field
- **Test**: Sends a `POST` request with `JSON` payload.

### mtg-api.yml

Shows learning from a REST API with URI patterns:

- **Target API**: Magic: The Gathering API (`https://api.magicthegathering.io`)
- **Port**: `14466`
- **Learning Configuration**:
  - Sorts learned interactions by resource.
  - Captures patterns based on URI.
- **Test**: Sends a `GET` request.

### university-learning.yml

Illustrates learning with query parameters:

- **Target API**: University API (`http://universities.hipolabs.com/search`)
- **Port**: `14453`
- **Learning Configuration**:
  - Captures patterns based on:
    - Path
    - Query parameter `name`
- **Test**: Searches for universities with name "`Harvard`".

## How to Configure Learning Mode

### Basic Configuration

```yaml
connections:
  - forward:
      learning:
        patterns:
          - type: Path  # Match on URL path
      mode: Learning
      to: realService
    name: LearningConnection
    port: 12345
```

### JSON Pattern Matching

```yaml
learning:
  patterns:
    - jsonPath: fieldName  # Match on JSON field value
```

### Query Parameter Matching

```yaml
learning:
  patterns:
    - key: paramName
      type: Query  # Match on query parameter
```

### Organizing Learned Interactions

```yaml
learning:
  sort: ByResource  # Group by resource path
```
