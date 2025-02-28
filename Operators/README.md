# Comparison Operators in Tosca API Simulator

## Overview

This directory demonstrates how to use comparison operators in the Tosca API Simulator to create conditional triggers based on complex comparisons. Operators allow simulation responses to be triggered based on more sophisticated comparisons than simple equality, including numeric, string, and date comparisons.

## Features

- Multiple comparison operator types (`equality`, `inequality`, `greater/less than`)
- Support for different data types (`string`, `numeric`, `date`).
- Works with both `XML` (`XPath`) and `JSON` (`JSONPath`) messages.
- Conditional response selection based on complex criteria.
- Type-specific comparisons with appropriate formatting.

## Supported Operators

| Operator         | Description                            | Example                                   |
|------------------|----------------------------------------|-------------------------------------------|
| `Equal`          | Default behavior, tests equality.      | `value: "test"` (implicit equal operator) |
| `NotEqual`       | Tests if values are not the same.      | `operator: NotEqual`                      |
| `Less`           | Tests if value is less than target.    | `operator: Less`                          |
| `Greater`        | Tests if value is greater than target. | `operator: Greater`                       |
| `LessOrEqual`    | Tests if value is <= target.           | `operator: LessOrEqual`                   |
| `GreaterOrEqual` | Tests if value is >= target.           | `operator: GreaterOrEqual`                |

## Data Types

| Data Type   | Description                           | Example                                   |
|-------------|---------------------------------------|-------------------------------------------|
| `String`    | Default type, text comparison         | `value: "test"` (implicit string type)    |
| `Numeric`   | Number comparison                     | `dataType: Numeric`                       |
| `Date`      | Date comparison                       | `dataType: Date`                          |

## Configuration Details

### operators.yml

Demonstrates operators with both XML and JSON:

- **Port**: `22222`
- **XML Example**: Uses `XPath` to apply operators to `SOAP` message elements.
- **JSON Example**: Uses `JSONPath` to apply operators to `JSON` properties.
- **Data Types**: Shows `string`, `numeric`, and `date` comparisons.

## XML Example

```yaml
trigger:
  - xPath: /*[local-name()='Envelope']/*[local-name()='Body']/*[local-name()='Add']/*[local-name()='n1']
    value: 100
    operator: NotEqual

  - xPath: /*[local-name()='Envelope']/*[local-name()='Body']/*[local-name()='Add']/*[local-name()='n3']
    value: 5
    dataType: Numeric
    operator: Less

  - xPath: /*[local-name()='Envelope']/*[local-name()='Body']/*[local-name()='Add']/*[local-name()='date']
    value: "{Date[07.04.2020][][dd.MM.yyyy]}"
    dataType: Date
    operator: Greater
```

This trigger will match when:

- The value of `n1` is not equal to "`100`".
- The numeric value of `n3` is less than `5`.
- The date in `date` is after `April 7, 2020`.

## JSON Example

```yaml
trigger:
  - jsonPath: name
    value: JSON Operator

  - jsonPath: dimensions.n1
    value: 100
    dataType: Numeric
    operator: NotEqual

  - jsonPath: dimensions.n4
    value: 15
    dataType: Numeric
    operator: LessOrEqual
```

This trigger will match when:

- The `name` property equals "`JSON Operator`".
- The numeric value of `dimensions.n1` is not equal to `100`.
- The numeric value of `dimensions.n4` is less than or equal to `15`.

## Testing the Examples

```bash
# Test XML operators
curl -X POST http://localhost:22222 \
  -H "Content-Type: application/soap+xml" \
  -d '<?xml version="1.0" encoding="utf-8"?>
      <s:Envelope xmlns:s="http://www.w3.org/2003/05/soap-envelope">
        <s:Body>
          <Add xmlns="http://CalculatorService">
            <n1>99</n1>
            <n2>XML Operator</n2>
            <n3>3</n3>
            <n4>12</n4>
            <n5>14</n5>
            <n6>22</n6>
            <date>15.09.2021</date>
          </Add>
        </s:Body>
      </s:Envelope>'

# Test JSON operators
curl -X POST http://localhost:22222 \
  -H "Content-Type: application/json" \
  -d '{
        "id": 2,
        "name": "JSON Operator",
        "price": 12.5,
        "dimensions": {
          "n1": 101,
          "n2": 4,
          "n3": 11,
          "n4": 14,
          "n5": 22,
          "date": "15.09.2021"
        }
      }'
```
