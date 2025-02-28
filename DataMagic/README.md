# Data-Driven API Simulation Examples

## Overview

This directory showcases advanced data-driven capabilities of the Tosca API Simulator. The examples demonstrate how to use CSV data sources to dynamically generate API responses and implement complex conditional logic based on request parameters.

## Features

- Dynamic data binding from CSV sources to JSON responses.
- Complex conditional logic using `JSONPath` expressions with `AND`/`OR` operators.
- SQL-like `WHERE` conditions for data filtering.
- Path parameter extraction and mapping to data sources.
- Test configurations to verify simulator behavior.

## Configuration Details

### data-driven-magic.yml

- **Port**: `26415`
- **Endpoint Pattern**: `/magic/{id}` where `{id}` is the character identifier.
- **Data Source**: External CSV file (`../src/hp-characters.csv`).
- **Response Format**: JSON with Harry Potter character attributes.

### decisions/conditions-trigger.yml

- **Port**: `22622`
- **Conditional Triggers**: Uses JSONPath expressions with logical operators
- **Test Services**: Includes request examples that match each condition type
- **Conditions Demonstrated**:
  - Simple OR: `@.category == 'Drama' || @.price < 20`
  - Simple AND: `@.author == 'John Doe' && @.price < 30`
  - Complex AND/OR: `@.price > 20 && (@.category == 'Fantasy' || @.rating > 4.5)`

### decisions/data-driven-where.yml

- **Port**: 55513
- **Endpoint**: `/decision` (POST)
- **Data Source**: Local CSV file (`data-driven-where.csv`)
- **Query Conditions**: `(A == '{B[buffera]}' AND B == '{B[bufferb]}') OR (C == '{B[bufferc]}')`

## Usage Examples

### Harry Potter Character API

```bash
# Query a character
GET http://localhost:26415/magic/1
```

### Conditional Logic Testing

```bash
# Test OR condition
POST http://localhost:22622
Content-Type: application/json

[{
  "title": "Book A",
  "author": "Mike Rowe",
  "category": "Comedy",
  "price": 19,
  "rating": 4.5
}]
```

### Data-Driven WHERE Conditions

```bash
# Test data filtering
POST http://localhost:55513/decision
Content-Type: application/json

{
  "a": "value1",
  "b": "value2",
  "c": "value3"
}
```
