# PDF File Serving in Tosca API Simulator

## Overview

This directory demonstrates how to serve binary PDF files using the Tosca API Simulator. The examples show how to configure endpoints that return PDF documents with the appropriate `Content-Type` headers, allowing you to test applications that need to download or process PDF files.

## Features

- Serving binary PDF files through API endpoints.
- Setting proper `MIME` type headers for PDFs.
- File path referencing (relative and absolute).
- Testing PDF file serving endpoints.
- Content validation for binary responses.

## Configuration Details

### file-cat.yml

Demonstrates PDF file serving with a relative path reference:

- **Port**: `21111`
- **Endpoint**: `/filecat`
- **File Path**: Uses a relative path (`../src/cat.pdf`) to reference the PDF file.
- **Content Type**: Sets the `application/pdf` content type header.

### fileCat.yml

An alternative implementation using a direct file reference:

- **Port**: `21111`
- **Endpoint**: `/filecat`
- **File Path**: Uses a direct reference (`../src/cat.pdf`), assuming the file is in the current directory.
- **Content Type**: Sets the `application/pdf` content type header.

### file-cat-test.yml

Demonstrates how to test the PDF serving functionality:

- **Client Setup**: Configures a client to connect to the PDF service.
- **Request**: Sends a `GET` request to the `/filecat` endpoint.
- **Verification**: Confirms a successful response with a `200 OK` status code.

## File Path Configuration

When referencing PDF files, you have two options:

1. **Relative Paths**: Reference files relative to the current directory.

   ```yaml
   payloadFile: "../src/cat.pdf"
   ```

2. **Direct References**: Reference files in the same directory.

   ```yaml
   payloadFile: "cat.pdf"
   ```

Ensure the referenced files exist at the specified location.

## Usage Examples

### Testing the PDF Endpoint

```bash
# Download the PDF file using curl
curl -v http://localhost:21111/filecat -o downloaded_cat.pdf

# Or test using the provided test configuration
api-simulator file-cat-test.yml
```

### Viewing the PDF in a Browser

Simply navigate to `http://localhost:21111/filecat` in a web browser to view the PDF directly.

## Implementation Details

### The `payloadFile` Attribute

The key to serving files is the `payloadFile` attribute in the message configuration:

```yaml
message:
  headers:
    - key: Content-Type
      value: "application/pdf"
  payloadFile: "../src/cat.pdf"
```

This tells the simulator to read the file content and use it as the response body.

### Content Type Header

For proper handling by clients, set the `Content-Type` header to `application/pdf`:

```yaml
headers:
  - key: Content-Type
    value: "application/pdf"
```

This ensures browsers and other clients recognize and handle the response as a PDF.
