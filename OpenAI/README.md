# OpenAI API Simulation with Tosca API Simulator

## Overview

This directory demonstrates how to use Tosca API Simulator to test applications that integrate with OpenAI's AI models, particularly Azure OpenAI Service. The examples show how to simulate OpenAI API endpoints, record real interactions for testing, and configure connections for direct API testing.

## Features

- Simulation of OpenAI chat completion endpoints.
- Stream-based response handling (Server-Sent Events).
- Learning mode for recording OpenAI API interactions.
- Direct API testing configuration.
- Support for Azure OpenAI Service endpoints.
- `JSONPath`-based matching for different message patterns.

## Configuration Details

### ai-test.yml

Demonstrates direct API testing with Azure OpenAI:

- **Target API**: Azure OpenAI Service (`eastus-openai-devtest.openai.azure.com`)
- **Model**: `GPT-4o`
- **Operation**: Chat completions
- **Validation**: Verifies successful status code and response time.
- **Request Format**: Standard OpenAI request with messages array.

### learning-connection.yml

Shows how to configure learning mode for OpenAI APIs:

- **Port**: `14444`
- **Forward To**: Azure OpenAI Service
- **Learning Configuration**:
  - Captures API paths and query parameters.
  - Records complete request payloads.
  - Extracts specific message content using `JSONPath`.
  - Tracks temperature settings.

### oai-azure.yml

Simulates an Azure OpenAI endpoint with streaming responses:

- **Port**: `13373`
- **Simulated Endpoint**: `/openai/deployments/gpt35Turbo16k/chat/completions`
- **Forward Mode**: SimulateFirst (responds with simulation, falls back to real API)
- **Response Format**: `text/event-stream` with incremental content chunks.
- **Content Filters**: Includes simulated content filter results.

## Understanding OpenAI API Simulation

### Stream-Based Responses

The `oai-azure.yml` example demonstrates how to simulate streaming responses:

```
data: {"choices":[{"delta":{"content":"As"},"finish_reason":null,"index":0}]}

data: {"choices":[{"delta":{"content":" an"},"finish_reason":null,"index":0}]}
```

Each chunk delivers a small portion of the response, mimicking the incremental nature of real-time AI-generated content.

### Content Filtering

OpenAI responses include content filtering information:

```json
"content_filter_results": {
    "hate": {"filtered": false, "severity": "safe"},
    "self_harm": {"filtered": false, "severity": "safe"},
    "sexual": {"filtered": false, "severity": "safe"},
    "violence": {"filtered": false, "severity": "safe"}
}
```

## Usage Examples

### Testing with Simulated OpenAI API

```bash
# Make a request to the simulated endpoint
curl -X POST http://localhost:13373/openai/deployments/gpt35Turbo16k/chat/completions?api-version=2024-04-01-preview \
  -H "Content-Type: application/json" \
  -H "api-key: test-key" \
  -d '{"messages":[{"role":"system","content":"You are a helpful assistant."},{"role":"user","content":"give me 20 paragraphs of scientific babble"}]}'
```

### Learning from Real OpenAI Interactions

```bash
# Make requests through the learning proxy
curl -X POST http://localhost:14444/openai/deployments/gpt4o/chat/completions?api-version=2024-04-01-preview \
  -H "Content-Type: application/json" \
  -H "api-key: YOUR_API_KEY" \
  -d '{"messages":[{"role":"user","content":"Hello world"}]}'
```

## Implementation Notes

1. **API Keys**: The examples require valid API keys for Azure OpenAI Service when connecting to real endpoints.

2. **Streaming Responses**: Use `text/event-stream` content-type with properly formatted SSE data chunks.

3. **JSON Structure**: Follow the OpenAI API specifications for request and response structures.

4. **Rate Limits**: Simulated endpoints can include rate limit headers to fully mimic API behavior.

5. **Content Filters**: Include content filter results in responses for comprehensive testing.
