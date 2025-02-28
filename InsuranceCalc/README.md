# Insurance Calculation Service Simulation

## Overview

This directory demonstrates how to use Tosca API Simulator to test insurance calculation services in both live and simulated environments. The examples focus on SOAP-based web services for car information and customer relationship management (CRM) in an insurance context.

## Features

- SOAP API simulation for insurance services.
- Contract testing between live and simulated environments.
- Forwarding and learning mode for API simulation.
- Performance verification with delay constraints.
- Service-oriented architecture representation.

## Configuration Details

### car-service-contract.yml

Tests a car information service:

- **Endpoint**: `http://localhost:51002/carservice`
- **Protocol**: `SOAP` (`XML` over `HTTP`)
- **Operation**: `getCar`
- **Test Scenario**: Retrieves information about BMW cars.
- **Validation**: Status code and response time verification.

Example request:

```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <getCar xmlns="http://tempuri.org/">
      <make>BMW</make>
    </getCar>
  </soap:Body>
</soap:Envelope>
```

### crm-service-contract-LIVE.yml

Tests a live CRM service for customer information:

- **Endpoint**: `http://localhost:51003/crmservice`
- **Protocol**: `SOAP` (`XML` over `HTTP`)
- **Operation**: `getPerson`
- **Test Scenario**: Retrieves information for customer "Max Mustermann".
- **Validation**: Status code and response time verification (`≤ 200ms`).

### crm-service-contract-SIM.yml

Tests a simulated CRM service:

- **Endpoint**: `http://localhost:14346/crmservice`
- **Protocol**: `SOAP` (`XML` over `HTTP`)
- **Operation**: getPerson
- **Test Scenario**: Retrieves information for customer "`Jon Snow`".
- **Validation**: Status code and response time verification (`≤ 200ms`)

### triserve-endpoints.yml

Sets up a forwarding proxy in learning mode:

- **Listening Port**: `14346`
- **Forward To**: `http://localhost:51003/crmservice` (Real CRM service)
- **Mode**: Learning (records interactions for later simulation)
