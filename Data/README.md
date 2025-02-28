# Harry Potter Character API Simulator

## Overview

This example demonstrates a data-driven API simulator that serves Harry Potter character information. The simulator uses a CSV data source to dynamically generate responses based on character IDs in the request path.

## Features

- Sets up a mock API endpoint at: `http://localhost:26414/magic/{id}`
- Loads character data from an external CSV file (`../src/hp-characters.csv`).
- Dynamically generates responses with character details based on the ID in the request path.
- Demonstrates data-driven response generation using Tosca API Simulator.

## Configuration Details

- **Port**: 26414
- **Endpoint Pattern**: `/magic/{id}` where `{id}` is the character identifier.
- **Data Source**: External CSV file containing Harry Potter character information.
- **Response Format**: JSON with character attributes (Name, Gender, House, Wand, etc).

## Testing Scenarios

The configuration includes:

1. A service definition that handles incoming requests and generates responses.
2. Data mapping from CSV to JSON response fields.
3. A contract test that verifies the API returns a `200 OK` status.

## Usage

1. Start the API Simulator with this configuration.
2. Send GET requests to `http://localhost:26414/magic/{id}` where `{id}` is the character identifier.
3. Receive a JSON response with the corresponding character's information.

## Example Request

```bash
GET http://localhost:26414/magic/1
```

## Example Response

```json
{
  "Id": 1,
  "Name": "Harry James Potter",
  "Gender": "Male",
  "Job": "Student",
  "House": "Gryffindor",
  "Wand": "11\"  Holly  phoenix feather",
  "Patronus": "Stag",
  "Species": "Human",
  "Blood status": "Half-blood",
  "Hair colour": "Black",
  "Eye colour": "Bright green",
  "Loyalty": "Albus Dumbledore | Dumbledore's Army | Order of the Phoenix | Hogwarts School of Witchcraft and Wizardry",
  "Skills": "Parseltongue| Defence Against the Dark Arts | Seeker",
  "Birth": "31 July 1980",
  "Death": ""
}
```
