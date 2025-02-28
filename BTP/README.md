# Business Technology Platform (BTP) Simulation Scenarios

## Overview

This directory demonstrates how to simulate various common API management scenarios encountered in the SAP Business Technology Platform (BTP) environment. The examples showcase different types of policy implementations typically found in API management platforms, including rate limiting, time-based access control, and origin-based restrictions.

## Features

- API rate limiting simulation.
- Time-based access restrictions.
- Origin/host-based access control.
- Custom response status codes.
- Testing with simulated edge cases.
- Header-based policy decisions.

## Configuration Details

### 5-calls.yml

Demonstrates API rate limiting where an API allows a fixed number of calls before rejecting requests:

- **Port**: `20222`
- **Functionality**: Allows 5 successful API calls, then returns an error for subsequent calls.
- **Test Scenario**: Makes 6 sequential requests to verify the rate limit behavior.
- **Error Handling**: Returns a custom "`999 Call Rate Exceeded`" status code when limit is reached.

This example is useful for testing how client applications handle rate-limiting scenarios without having to configure actual rate limits in a production API gateway.

### off-at-night.yml

Shows how to implement time-based access control for services that are only available during business hours:

- **Port**: `20223`
- **Functionality**:
  - Returns successful responses during "daytime" hours.
  - Returns error responses during "nighttime" hours.
- **Time Simulation**: Uses a custom `fakeTime` header to allow testing without waiting for specific times of day.
- **Comparison Operators**: Demonstrates date/time comparison with `LessOrEqual` and `GreaterOrEqual` operators.

This example helps test applications that need to handle scheduled service availability or maintenance windows.

### origin-test.yml

Illustrates origin-based access control similar to CORS policies or IP-based restrictions:

- **Port**: `20224`
- **Functionality**:
  - Accepts requests from a specific origin (`http://mygoodhost:20224`).
  - Rejects requests from all other origins.
- **Header Processing**: Examines the `origin` header to make access decisions.
- **Error Response**: Returns "`500 I dont know you`" for unauthorized origins.

This example is helpful for testing how applications handle origin-based restrictions commonly implemented in API gateways.

## Usage Examples

### Testing Rate Limiting

```bash
# Run test requests against the rate-limited endpoint
curl http://localhost:20222/5calls/ # Success - Call 1
curl http://localhost:20222/5calls/ # Success - Call 2
curl http://localhost:20222/5calls/ # Success - Call 3
curl http://localhost:20222/5calls/ # Success - Call 4
curl http://localhost:20222/5calls/ # Success - Call 5
curl http://localhost:20222/5calls/ # Failure - 999 Call Rate Exceeded
```

### Testing Time-Based Access

```bash
# Test with different simulated times
curl -H "fakeTime: 08:00:00 GMT" http://localhost:20223/faketime # Success - Morning
curl -H "fakeTime: 20:00:00 GMT" http://localhost:20223/faketime # Failure - 500 Sleeping
```

### Testing Origin-Based Access

```bash
# Test with different origins
curl -H "origin: http://mygoodhost:20224" http://localhost:20224/origin # Success
curl -H "origin: http://mybadhost:20224" http://localhost:20224/origin # Failure - 500 I dont know you
```

## Implementation Notes

### Sequenced Steps for Rate Limiting

The rate limiting example uses sequential steps to track the number of requests:

```yaml
steps:
  - direction: In
    name: Request 1
    trigger:
      - type: Path
        value: /5calls/
  - direction: Out
    message:
      payload: Call 1
    name: Response 1
  # Additional steps for subsequent requests...
```

### Date Comparison for Time-Based Access

The time-based example uses date comparison operators:

```yaml
trigger:
  - type: Header
    key: fakeTime
    dataType: Date
    operator: LessOrEqual
    value: 12:00:00 GMT
```

### String Comparison for Origin Checking

The origin-based example uses string comparison:

```yaml
trigger:
  - type: Header
    key: origin
    operator: Equal
    dataType: String
    value: http://mygoodhost:20224
```
