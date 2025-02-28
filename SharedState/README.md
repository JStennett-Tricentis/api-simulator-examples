# Shared State Management in Tosca API Simulator

## Overview

This directory demonstrates how to implement stateful API simulations using the Tosca API Simulator's shared state capabilities. The example implements a simple shopping cart service that maintains state across multiple requests, allowing for `create`, `read`, `update`, and `delete` (`CRUD`) operations on cart items. State is persisted in a `CSV` file, enabling data to survive between API calls and simulator restarts.

## Features

- Persistent data storage using CSV resources.
- Full `CRUD` operations on shared state.
- Random ID generation for new resources.
- Path parameter extraction.
- `SQL`-like filtering with `WHERE` clauses.
- `JSON` response templating from state data.
- RESTful API design pattern.

## Configuration Details

### simple-state.yml

Implements a shopping cart service with the following endpoints:

- `POST /cart/create` - Creates a new shopping cart with a random ID.
- `GET /cart/{id}` - Retrieves details of a specific cart.
- `PUT /cart/{id}` - Updates a cart with new item information.
- `DELETE /cart/{id}` - Removes a cart from the system.

Key implementation features:

- **Resource Definition**:

  ```yaml
  resources:
    - name: cart
      type: Table
      file: ../src/cart.csv
  ```

- **Data Operations**:
  - `insert` - Adds new cart records.
  - `read` - Retrieves cart details.
  - `update` - Modifies cart contents.
  - `delete` - Removes cart entries.

### simple-state-test.yml

Provides test cases for the shopping cart service:

- **CreateCartTest**: Tests cart creation.
- **UpdateCartTest**: Tests adding/updating items in a cart.
- **GetCartTest**: Tests retrieving cart details.
- **DeleteCartTest**: Tests cart deletion.

### ../src/cart.csv

Defines the data structure for shopping carts:

- **cartId**: Unique identifier for each cart.
- **itemId**: Product identifier.
- **itemDesc**: Product description.
- **quantity**: Number of items.

## Shared State Operations

### Creating State

```yaml
# Generate a random cart ID
buffer:
  - name: cartId
    value: "{RND[5]}"

# Store in shared state
resource:
  insert:
    - ref: cart
      value: ["{b[cartId]}", "", "", ""]
```

### Reading State

```yaml
resource:
  read:
    - ref: cart
      where: cartId=='{b[cartId]}'
      name: currentCart

# Use state in response templating
message:
  payload: |
    {
      "cartId": "%{currentCart.cartId}",
      "itemId": "%{currentCart.itemId}"
    }
```

### Updating State

```yaml
resource:
  update:
    - ref: cart
      where: cartId=='{b[cartId]}'
      value:
        itemId: "{b[itemId]}"
        itemDesc: "{b[itemDesc]}"
        quantity: "{b[quantity]}"
```

### Deleting State

```yaml
resource:
  delete:
    - ref: cart
      where: cartId=='{b[cartId]}'
```

## Usage Examples

### Creating a Cart

```bash
# Create a new cart
curl -X POST http://localhost:17079/cart/create

# Response:
# {
#   "message": "Cart Created",
#   "cartId": "12345"
# }
```

### Updating a Cart

```bash
# Add an item to the cart
curl -X PUT http://localhost:17079/cart/12345 \
  -H "Content-Type: application/json" \
  -d '{
    "itemId": "ABC123",
    "itemDesc": "Premium Widget",
    "quantity": 3
  }'
```

### Retrieving Cart Details

```bash
# Get cart details
curl -X GET http://localhost:17079/cart/12345

# Response:
# {
#   "message": "Cart Details",
#   "cartId": "12345",
#   "itemId": "ABC123",
#   "itemDesc": "Premium Widget",
#   "quantity": 3
# }
```

### Deleting a Cart

```bash
# Delete a cart
curl -X DELETE http://localhost:17079/cart/12345
```
