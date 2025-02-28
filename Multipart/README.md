# Multipart Form Data in Tosca API Simulator

## Overview

This directory demonstrates how to configure the Tosca API Simulator to handle multipart form data, which is essential for testing APIs that deal with file uploads or return mixed content types. The example shows how to create a simulated endpoint that returns a multipart response containing both JSON data and a file attachment.

## Features

- Configuration of multipart/mixed response headers.
- Setup of response with multiple content parts.
- JSON data inclusion in multipart responses.
- File attachment simulation.
- Custom boundary definition for part separation.

## Configuration Details

### multipart.yml

Demonstrates a multipart response configuration:

- **Port**: `14741`
- **Endpoint**: `/multipart`
- **Response Content-Type**: `multipart/mixed` with a defined boundary.
- **Response Parts**:
  - A JSON part with pet information.
  - A PDF attachment named "`../src/cat.pdf`".

The response format follows the standard multipart specification with:

- Boundary markers to separate content parts.
- `Content-Type` headers for each part.
- `Content-Disposition` headers to indicate form data or attachment status.

## Understanding Multipart Format

Multipart responses use a boundary string to separate different content sections:

```bash
--boundary-string
Content-Type: application/json
Content-Disposition: form-data

{JSON content here}

--boundary-string
Content-Type: application/pdf
Content-Disposition: attachment; filename="file.pdf"

<binary data here>
--boundary-string--
```

Key components:

- Boundary string must be unique and not appear in the content.
- Each part has its own headers.
- The final boundary includes two hyphens at the end to mark completion.

## Implementation Notes

1. **Content-Type Header**: The `multipart/mixed` content type with boundary parameter is essential for proper parsing by clients.

2. **Boundary Definition**: Must be unique and not appear in any part's content.

3. **Part Headers**: Each part should include appropriate content type and disposition headers.

4. **Binary Content**: For actual binary files, replace the placeholder text with real binary data or use a file reference.

5. **Response Structure**: Follow proper multipart formatting with start, middle, and end boundary markers.
