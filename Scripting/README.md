# Scripting in Tosca API Simulator

## Overview

This directory demonstrates the scripting capabilities of the Tosca API Simulator, focusing on conditional XML content manipulation. The example shows how to selectively remove or modify XML elements based on their content, which is particularly useful when simulating systems that handle complex XML documents like financial message formats.

## Features

- Conditional processing with `TRY` expressions.
- XML element manipulation using `XPath`.
- Buffer storage for dynamic data access.
- Element verification in tests.
- Advanced response customization based on request content.
- Handling of empty XML elements.

## Configuration Details

### remove-empty.yml

Demonstrates how to conditionally handle XML elements based on their content:

- **Port**: `17071`
- **Endpoint**: `/extensions/remove/empty`
- **Functionality**:
  - Captures an XML element's value using `XPath` and buffer.
  - Uses a `TRY` expression to conditionally deactivate a tag in the response.
  - Demonstrates handling of empty vs. populated XML elements.

Key implementation features:

```yaml
# Storing XML element content in a buffer
buffer:
  - name: deli_sctiessttlmtxinstr_sttlmparams_modcxlallwd_ind
    xPath: /*[local-name()='RequestPayload']/*[local-name()='Document']/*[local-name()='SctiesSttlmTxInstr']/*[local-name()='SttlmParams']/*[local-name()='ModCxlAllwd']/*[local-name()='Ind']

# Using TRY expression to conditionally modify the response
insert:
  - value: "{TRY[{B[deli_sctiessttlmtxinstr_sttlmparams_modcxlallwd_ind]}][deactivate tag]}"
    xPath: /*[local-name()='Envelope']/*[local-name()='Body']/*[local-name()='AddResponse']/*[local-name()='AddResult']
```

### remove-empty-test.yml

Provides test cases for the XML element handling functionality:

- **Test Cases**:
  1. `test_securities_settlement_transaction`: Tests normal operation with populated data.
  2. `test_modified_ind_value`: Tests operation with a different value.
  3. `test_empty_ind_value`: Tests handling of empty XML elements.

- **Verification Points**:
  - Checks for presence/absence of specific XML elements.
  - Verifies element content.
  - Validates status codes.

## Understanding the TRY Expression

The `TRY` expression in the Tosca API Simulator allows for conditional logic:

```yaml
{TRY[condition][action]}
```

Where:

- `condition`: The value to check (often a buffer reference).
- `action`: The action to take if the condition is truthy.

In the example, `{TRY[{B[deli_sctiessttlmtxinstr_sttlmparams_modcxlallwd_ind]}][deactivate tag]}` means:

- If the captured XML element value is non-empty, proceed normally.
- If empty or not found, "deactivate tag" which removes the element from the response.

## XPath in Scripting

The examples use XPath extensively to target specific XML elements:

```yaml
xPath: /*[local-name()='RequestPayload']/*[local-name()='Document']/*[local-name()='SctiesSttlmTxInstr']/*[local-name()='SttlmParams']/*[local-name()='ModCxlAllwd']/*[local-name()='Ind']
```

This approach is particularly useful for complex XML documents with namespaces, as the `local-name()` function allows targeting elements regardless of their namespace prefix.

## Usage Examples

### Testing XML Element Handling

```bash
# Send an XML message with a populated element
curl -X POST http://localhost:17071/extensions/remove/empty \
  -H "Content-Type: application/xml" \
  -d '<?xml version="1.0" encoding="UTF-8"?>
      <RequestPayload>
          <Document xmlns="urn:iso:std:iso:20022:tech:xsd:sese.023.001.07">
              <SctiesSttlmTxInstr>
                  <SttlmParams>
                      <ModCxlAllwd>
                          <Ind>POPULATED_VALUE</Ind>
                      </ModCxlAllwd>
                  </SttlmParams>
              </SctiesSttlmTxInstr>
          </Document>
      </RequestPayload>'
```
