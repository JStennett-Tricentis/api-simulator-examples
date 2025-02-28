# Resource Management in Tosca API Simulator

## Overview

This directory demonstrates how to work with resources in the Tosca API Simulator, particularly focusing on CSV-based tabular data resources. The examples showcase a complete `CRUD` (`Create`, `Read`, `Update`, `Delete`) API implementation for managing user records, illustrating how the simulator can be used to create realistic data-driven APIs.

## Features

- CSV data resource definition and configuration.
- Complete `CRUD` operations implementation.
- SQL-like `WHERE` conditions for data filtering.
- Data templates for formatting responses.
- Dynamic data insertion and extraction.
- RESTful API design patterns.
- Record-level and collection-level operations.

## Configuration Details

### auth-service.yml

Implements a full user management API with the following endpoints:

- `POST /user` - Creates a new user record.
- `GET /user/{id}` - Retrieves a specific user by ID.
- `GET /user` - Lists all users.
- `PUT /user/{id}` - Updates user information.
- `DELETE /user/{id}` - Removes a specific user.
- `DELETE /user` - Clears all user records.

Key implementation features:

- **Resource Definition**:

  ```yaml
  resources:
    - name: users
      type: Table
      file: ../src/users.csv
      listSeparator: ","
  ```

- **Data Operations**:
  - `insert` - Adds new records.
  - `read` - Retrieves records with optional filtering.
  - `update` - Modifies existing records.
  - `delete` - Removes records.

### user-test.yml

Demonstrates testing the user service API:

- **Endpoint**: `localhost:10090/user`
- **Method**: `GET`
- **Headers**: Validates response headers.
- **Payload**: Sample user data.

### order-service.yml

Shows a sample order service client:

- **Endpoint**: `http://localhost:1900/order`
- **Method**: `POST`
- **Payload**: `JSON` order data with customer and product information.

## Resource Operations

### Creating Records

```yaml
resource:
  insert:
    - ref: users
      value:
        [
          "{b[id]}",
          "{b[name]}",
          "{b[sfdc_username]}",
          # Additional fields...
          "{DATE}",
        ]
```

### Reading Records

#### Single Record

```yaml
resource:
  read:
    - ref: users
      name: result
      where: "id == '{b[id]}'"
```

#### All Records

```yaml
resource:
  read:
    - ref: users
      name: results
      template: |-
        {
          %{results.id},
          %{results.sfdc_username},
          %{results.sfdc_clientid},
          %{results.date_created}
        }
```

### Updating Records

```yaml
resource:
  update:
    - ref: users
      where: "id == '{b[id]}'"
      value:
        sfdc_password: "{b[sfdc_password]}"
        sfdc_clientid: "{b[sfdc_clientid]}"
        # Additional fields...
```

### Deleting Records

#### Single Record

```yaml
resource:
  delete:
    - ref: users
      where: "id == '{b[id]}'"
```

#### All Records

```yaml
resource:
  delete:
    - ref: users
```

## Usage Examples

### Creating a User

```bash
curl -X POST http://localhost:10090/user \
  -H "Content-Type: application/json" \
  -d '{
    "id": "1",
    "name": "John Doe",
    "sfdc_username": "john.doe@example.com",
    "sfdc_password": "password123",
    "sfdc_clientid": "client123",
    "sfdc_secret": "secret456",
    "sfdc_securitytoken": "token789",
    "sfdc_apiendpoint": "na1"
  }'
```

### Reading User Data

```bash
# Get a specific user
curl -X GET http://localhost:10090/user/1

# Get all users
curl -X GET http://localhost:10090/user
```

### Updating a User

```bash
curl -X PUT http://localhost:10090/user/1 \
  -H "Content-Type: application/json" \
  -d '{
    "sfdc_password": "newpassword",
    "sfdc_clientid": "newclient",
    "sfdc_secret": "newsecret",
    "sfdc_securitytoken": "newtoken",
    "sfdc_apiendpoint": "na2"
  }'
```

### Deleting a User

```bash
# Delete a specific user
curl -X DELETE http://localhost:10090/user/1

# Delete all users
curl -X DELETE http://localhost:10090/user
```
