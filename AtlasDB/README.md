# AtlasDB API Simulation

## Overview

This directory demonstrates how to simulate a database API using the Tosca API Simulator. The examples focus on customer data retrieval with dynamic response selection based on customer IDs. This approach allows for creating realistic database simulation without requiring an actual database connection, making it ideal for testing applications that depend on database services.

## Features

- Dynamic response selection based on request parameters.
- Mapping customer IDs to specific response files.
- Default response handling for unknown IDs.
- Pagination support for collection endpoints.
- Comprehensive test scenarios.
- Data-driven API simulation using CSV mapping.

## Configuration Details

### atlas-db.yml

Implements a customer database API with the following endpoints:

- **FindCustomer/{id}** - Retrieves a specific customer by ID.
- **GetAllCustomers** - Retrieves a paginated list of all customers.

Key implementation features:

- **Port**: `25151`
- **Dynamic Response Selection**: Uses the expression `"{from[dataFiles][file][id='{B[id]}']}"` to select JSON response files based on the customer ID.
- **Resource Definition**: Uses a CSV file to map customer IDs to response files.
- **Test Verification**: Includes basic service tests for both endpoints.

### atlas-db-test.yml

Provides comprehensive test cases for the customer database API:

- **testFindCustomerById**: Tests successful customer retrieval.
- **testFindCustomerNotFound**: Tests handling of non-existent customer IDs.
- **testGetAllCustomers**: Tests the paginated list endpoint.
- **testGetAllCustomersWithDifferentPagination**: Tests pagination parameters.

## Data Structure

### data-files.csv

Maps customer IDs to response files:

```csv
id,file
11111,AtlasDB\data\file-1.json
22222,AtlasDB\data\file-2.json
...
'*',AtlasDB\data\file-default.json
```

The special `'*'` entry provides a default response for any unmapped customer ID.

### JSON Response Files

The `/data` directory contains JSON files representing different customer records:

- **all-customers.json**: Contains a collection of all customer records with pagination metadata.
- **file-1.json, file-2.json, etc.**: Individual customer record responses.
- **file-default.json**: Default response for unknown customer IDs.

Each customer record includes:

- Basic customer information (ID, name, contact details).
- Quote and payment information.
- Address information.
- Integration IDs (Salesforce IDs)

## Usage Examples

### Retrieving a Specific Customer

```bash
# Get customer by ID
curl http://localhost:25151/AtlasDataApi/FindCustomer/640b66ee2f02a7b6562f5061

# Response:
# {
#   "totalRecords": 1,
#   "page": 1,
#   "pageSize": 10,
#   "data": [
#     {
#       "id": "640b66ee2f02a7b6562f5061",
#       "quoteId": "886575251",
#       "firstName": "Aaron",
#       "lastName": "Schimmel",
#       ...
#     }
#   ],
#   "succeeded": true,
#   "errors": [],
#   "message": null
# }
```

### Retrieving All Customers with Pagination

```bash
# Get all customers with pagination parameters
curl http://localhost:25151/AtlasDataApi/GetAllCustomers?pageNumber=1&pageSize=10

# Response includes a collection of customer records with pagination metadata
```

## Implementation Details

### Dynamic Response Selection

The key to this implementation is the dynamic file selection based on the customer ID:

```yaml
insert:
  - file: "{from[dataFiles][file][id='{B[id]}']}"
```

This expression:

1. Takes the customer ID from the request buffer (`{B[id]}`).
2. Looks up the corresponding file path in the dataFiles resource (`id='{B[id]}'`).
3. Inserts the content of that file as the response.

### CSV Resource Definition

```yaml
resources:
  - file: data-files.csv
    name: dataFiles
```

This defines a resource named "dataFiles" backed by the CSV file, which can then be queried using the `from` syntax.
