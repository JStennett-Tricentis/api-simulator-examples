# Buffer Functionality in Tosca API Simulator

## Overview

This directory demonstrates the powerful buffer functionality in Tosca API Simulator. Buffers allow you to store and manipulate data between requests, enabling stateful simulations and dynamic response generation based on request content.

## Features

- Session tracking with random value buffers.
- String splitting and manipulation.
- Substring extraction.
- Temporary value storage.
- Template substitution with embedded buffers.
- Request validation using buffer content.

## Configuration Details

### buffer.yml

Demonstrates several buffer capabilities:

- **Buffer Scenario**: Using random values to create unique session IDs for tracking requests.
- **Split Buffer**: Parsing delimited strings into multiple values (`a;b;c;d` → individual values).
- **Substring Buffer**: Extracting portions of strings using range notation.
- **Temporary Buffer**: Working with random values and preserving them between requests.
- **Embedded Buffer**: Using buffer values as templates in response payloads.
- **Verify Buffer**: Validating incoming requests against stored buffer values.

### split-buffer.yml

Focuses specifically on the split buffer functionality:

- Takes a hyphen-delimited string (`a-b-c-d`).
- Splits it into individual components.
- Demonstrates accessing individual elements by index (`buffer1`, `buffer2`, etc.).
- Shows combining multiple buffer values in the response.

## Usage Examples

### Session Tracking

```yaml
# Store a random session ID
- buffer:
    - key: sid
      value: "{rnd}"
  insert:
    - type: Path
      value: buffer/scenario/{b[sid]}

# Reference the stored session ID in later requests
- insert:
    - type: Header
      key: sid
      value: "{b[sid]}"
```

### String Splitting

```yaml
# Split a string using a delimiter
- trigger:
    - value: a;b;c;d
  buffer:
    - name: buffer
      value: "{SPLITBUFFER[;]}"

# Access individual segments
- insert:
    - value: "{B[buffer3]}"  # Outputs "c" (third element, zero-based)
```

### Substring Extraction

```yaml
# Extract part of a string
- buffer:
    - name: substring
      range: ^6..
- insert:
    - value: "{B[substring]}"
```

### Template Substitution

```yaml
# Store a value in a buffer
- trigger:
    - value: this is embedded
  buffer:
    - name: buffer
# Use the buffer value inside a template
- message:
    payload: somewhere inside there is a %{buffer} and no path needed
```
