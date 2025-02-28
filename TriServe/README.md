# TriServe SOAP Service Simulation

## Overview

This directory demonstrates how to simulate SOAP web services using the Tosca API Simulator. The TriServe examples showcase a complete testing environment for interconnected SOAP services, with the ability to switch between live and simulated endpoints. This approach enables comprehensive testing of service consumers without requiring all backend services to be available.

## Features

- SOAP web service simulation.
- XML content manipulation with XPath.
- Conditional response selection based on request content.
- Live vs. simulated endpoint switching.
- Response time validation.
- Service contract testing.
- Dynamic data injection in responses.
- Request routing.

## Configuration Details

### triserve-endpoints.yml

Defines the basic endpoint configuration:

- **Port**: `14346` (for the CRM service listener)
- **Connections**: One connection for the listener and one for routing to the real service.

This file establishes the foundation for routing between simulated and live services.

### get-person.yml

Simulates a CRM service that returns person information:

- **Conditional Processing**: Different responses based on `firstName`/`lastName` combinations.
- **XML Manipulation**: Uses `XPath` to dynamically modify XML responses.
- **Pattern Matching**: Wildcard support for lastName with `"*"` pattern.
- **Response Delay**: Simulates realistic service timing with delays.

### car-service-contract.yml

Defines a contract test for the Car service:

- **Endpoint**: `http://localhost:51002/carservice`
- **SOAP Action**: `http://tempuri.org/getCar`
- **Request**: XML payload with car make (BMW).
- **Verification**: Checks for `200 OK` status and validates response timing.

### crm-service-contract-LIVE.yml and crm-service-contract-SIM.yml

Demonstrates testing against both live and simulated CRM services:

- **Live Endpoint**: `http://localhost:51003/crmservice`
- **Simulated Endpoint**: `http://localhost:14346/crmservice`
- **Request Data**: Different test data for live vs. simulated environment.
- **Contract Testing**: Same verification rules applied to both environments.

### triserve-router.yml

Provides central routing for the TriServe application:

- **Port**: `18182`
- **Forward To**: Real service at `http://localhost:55000/SampleApp/`.
- **Purpose**: Routes requests to the appropriate service (simulated or real).

## SOAP Service Implementation

The examples demonstrate several key aspects of SOAP service simulation:

### Request Matching with XPath

```yaml
trigger:
  - xPath: /*[local-name()='Envelope']/*[local-name()='Body']/*[local-name()='GetPerson']/*[local-name()='firstName']
    value: jon
  - xPath: /*[local-name()='Envelope']/*[local-name()='Body']/*[local-name()='GetPerson']/*[local-name()='lastName']
    value: snow
```

### Dynamic Response Modification

```yaml
insert:
  - xPath: /*[local-name()='Envelope']/*[local-name()='Body']/*[local-name()='GetPersonResponse']/*[local-name()='GetPersonResult']/*[local-name()='Insurant']/*[local-name()='StreetAddress']
    value: 99999999999999999 &*^(*) Cold Street
```

### SOAP Headers Setup

```yaml
headers:
  - key: SOAPAction
    value: "http://tempuri.org/getPerson"
  - key: Content-Type
    value: "text/xml; charset=utf-8"
```
