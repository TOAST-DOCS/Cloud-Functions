<!-- pre-align:aligned sig=253528824169 -->

<a id="compute-cloud-functions-code-template-guide-python"></a>
## Compute > Cloud Functions > Code Template Guide > Python { #compute-cloud-functions-code-template-guide-python }

This document details how to develop functions by using Python from NHN Cloud's Cloud Functions service.

<a id="template-information"></a>
## Template information { #template-information }
| Item              | Value                  |
|-----------------|------------------|
| **Supported version**       | 3.11, 3.12, 3.13 |
| **File name**         | user.py            |
| **Entry Point** | user.main        |

<a id="basic-template"></a>
## Basic template { #basic-template }

<a id="hello-world-example"></a>
### Hello World example { #hello-world-example }
A basic form of function.

```python
import sys
import yaml

document = """
  a: 1
  b:
    c: 3
    d: 4
"""

def main():
    return yaml.dump(yaml.safe_load(document))
```

<a id="context-object"></a>
### Context object { #context-object }
Python functions can access HTTP request information through Flask's request object.

```python
from flask import request
import json

def main():
    # HTTP request information
    method = request.method
    headers = dict(request.headers)
    args = request.args.to_dict()

    # Request body (such as POST/PUT)
    data = None
    if request.is_json:
        data = request.get_json()
    elif request.data:
        data = request.data.decode('utf-8')

    return {
        'method': method,
        'headers': headers,
        'query_params': args,
        'body': data
    }
```

<a id="download-and-use-template-file"></a>
## Download and use template file { #download-and-use-template-file }

<a id="template-download"></a>
### Template download { #template-download }
You can download the Python template provided by Cloud Functions to develop a local environment.

**Template download link**: [python.zip](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/templates/python/python.zip)

<a id="template-file-structure"></a>
### Template file structure { #template-file-structure }
The structure of the downloaded template file is as follows:

```
python.zip
├── user.py          # Main function file
└── requirements.txt # Dependency management file
```

<a id="template-file-structure-userpy"></a>
#### user.py
Include basic YAML processing functions.
```python
import sys
import yaml

document = """
  a: 1
  b:
    c: 3
    d: 4
"""

def main():
    return yaml.dump(yaml.safe_load(document))
```

<a id="template-file-structure-requirementstxt"></a>
#### requirements.txt
```txt
pyyaml
```

<a id="local-development-process"></a>
### Local development process { #local-development-process }

<a id="local-development-process-unzip"></a>
#### 1. Unzip
```bash
# Unzip
unzip python.zip -d my-function

# Move to task directory
cd my-function
```

<a id="local-development-process-modify-function-codes"></a>
#### 2. Modify function codes
Modify `user.py` file with the logic you want.

```python
# user.py - Simple modification example
from flask import request
import json
from datetime import datetime

def main():
    try:
        # Import name from query parameter
        name = request.args.get('name', 'World')

        # Import message from body if POST request
        custom_message = ''
        if request.method == 'POST':
            try:
                data = request.get_json()
                if data:
                    custom_message = data.get('message', '')
            except Exception:
                # Ignore if JSON parsing fails
                pass

        result = {
            'greeting': f'Hello, {name}!',
            'message': custom_message,
            'method': request.method,
            'timestamp': str(datetime.now())
        }

        return json.dumps(result, ensure_ascii=False)

    except Exception as e:
        return json.dumps({
            'error': 'Internal Server Error',
            'details': str(e)
        }, ensure_ascii=False)
```

<a id="local-development-process-compress-into-a-zip-file"></a>
#### 3. Compress into a ZIP file
Compress the modified source code into ZIP file.

```bash
# Windows (PowerShell)
Compress-Archive -Path .\user.py, .\requirements.txt -DestinationPath my-function.zip

# Windows (when using 7-Zip)
7z a my-function.zip user.py requirements.txt

# macOS/Linux
zip my-function.zip user.py requirements.txt

# Include all files (if any additional files are available)
zip -r my-function.zip . -x "*.git*" "__pycache__/*" "*.pyc" "test.py"
```

<a id="upload-from-cloud-functions-console"></a>
### Upload from Cloud Functions console { #upload-from-cloud-functions-console }
> Used when uploading files from the user's local environment when creating or modifying a function. (refer to Console Guide)

<a id="cautions-for-upload"></a>
### Cautions for upload { #cautions-for-upload }

<a id="cautions-for-upload-zip-file-structure"></a>
#### ZIP file structure
- The `.py` file and `requirements.txt` must be located directly in the root of the ZIP file.
- We recommend avoiding unnecessary folder structures.

**Right structure:**
```
my-function.zip
├── user.py
├── requirements.txt
└── utils.py (if there are additional files)
```

**Wrong structure:**
```
my-function.zip
└── my-function/
    ├── user.py
    └── requirements.txt
```

<a id="cautions-for-upload-file-size-limit"></a>
#### File size limit
- ZIP file size is limited to 100MiB.
- `__pycache__` file should not be included.

<a id="cautions-for-upload-files-to-exclude"></a>
#### Files to exclude
```bash
# Similar to .gitignore, exclude the following files:
zip -r my-function.zip . -x \
  "__pycache__/*" \
  "*.pyc" \
  ".git/*" \
  "*.log" \
  "test.py" \
  ".env" \
  "*.zip"
```

<a id="process-by-http-method"></a>
## Process by HTTP method { #process-by-http-method }

<a id="process-get-request"></a>
### Process GET request { #process-get-request }
```python
from flask import request
import json
from datetime import datetime

def main():
    if request.method != 'GET':
        return json.dumps({'error': 'Method Not Allowed'}, ensure_ascii=False), 405

    # Import query parameter
    name = request.args.get('name', 'World')
    greeting = request.args.get('greeting', 'Hello')

    result = {
        'message': f'{greeting}, {name}!',
        'timestamp': datetime.now().isoformat(),
        'method': 'GET'
    }

    return json.dumps(result, ensure_ascii=False)
```

<a id="process-post-request"></a>
### Process POST request { #process-post-request }
```python
from flask import request
import json
from datetime import datetime
import uuid

def main():
    if request.method != 'POST':
        return json.dumps({'error': 'Method Not Allowed'}, ensure_ascii=False), 405

    try:
        # Parse request body as a JSON format
        request_body = request.get_json()

        # Validate required fields
        if not request_body or 'name' not in request_body:
            return json.dumps({
                'error': 'Missing required field: name'
            }, ensure_ascii=False), 400

        name = request_body['name']
        email = request_body.get('email', 'not provided')
        message = request_body.get('message', 'No message')

        # Processing logic
        response = {
            'id': str(uuid.uuid4())[:8],
            'name': name,
            'email': email,
            'message': message,
            'processed_at': datetime.now().isoformat()
        }

        return json.dumps(response, ensure_ascii=False)

    except Exception as e:
        return json.dumps({
            'error': 'Invalid JSON format',
            'details': str(e)
        }, ensure_ascii=False), 400
```

<a id="manage-packages"></a>
## Manage packages { #manage-packages }

<a id="write-requirementstxt"></a>
### Write requirements.txt { #write-requirementstxt }
Write `requirements.txt` to manage dependencies.

```txt
pyyaml
requests>=2.28.0
python-dateutil>=2.8.0
```

<a id="example-of-external-api-call"></a>
### Example of external API call { #example-of-external-api-call }
```python
from flask import request
import json
import requests
from datetime import datetime

def main():
    try:
        user_id = request.args.get('userId')

        if not user_id:
            return json.dumps({'error': 'userId is required'}, ensure_ascii=False), 400

        # External API call
        response = requests.get(f'https://jsonplaceholder.typicode.com/users/{user_id}')

        if response.status_code == 404:
            return json.dumps({'error': 'User not found'}, ensure_ascii=False), 404

        response.raise_for_status()
        user_data = response.json()

        result = {
            'user': user_data,
            'fetched_at': datetime.now().isoformat()
        }

        return json.dumps(result, ensure_ascii=False)

    except requests.RequestException as e:
        return json.dumps({
            'error': 'External API error',
            'details': str(e)
        }, ensure_ascii=False), 500
    except Exception as e:
        return json.dumps({
            'error': 'Internal server error',
            'details': str(e)
        }, ensure_ascii=False), 500
```

<a id="data-processing-example"></a>
### Data processing example { #data-processing-example }
```python
from flask import request
import json
from datetime import datetime
import uuid
import re

def main():
    try:
        request_body = request.get_json()
        data = request_body.get('data', [])

        if not isinstance(data, list):
            return json.dumps({'error': 'Data must be an array'}, ensure_ascii=False), 400

        # Data processing
        processed_data = []
        for item in data:
            if 'name' in item:
                processed_item = {
                    'id': str(uuid.uuid4())[:8],
                    **item,
                    'processed_at': datetime.now().isoformat(),
                    'normalized_name': normalize_name(item['name'])
                }
                processed_data.append(processed_item)

        # Data sorting and filtering
        valid_data = [item for item in processed_data if item['normalized_name']]
        valid_data.sort(key=lambda x: x['normalized_name'])

        result = {
            'total_processed': len(processed_data),
            'valid_items': len(valid_data),
            'data': valid_data
        }

        return json.dumps(result, ensure_ascii=False)

    except Exception as e:
        return json.dumps({
            'error': 'Data processing failed',
            'details': str(e)
        }, ensure_ascii=False), 400

def normalize_name(name):
    """name normalization function"""
    if not name:
        return ''
    # Remove spaces and capitalize the first letter
    return name.strip().title()
```

<a id="use-environment-variables"></a>
## Use Environment Variables { #use-environment-variables }
Environment variables registered for a function can be accessed via `os.environ` (or `os.getenv`). Environment variables are registered during the code writing step of function creation and modification. For more information, see the console user guide.

```python
from flask import request
import os
import json

def main():
    # Read environment variables
    db_host = os.environ.get('DB_HOST')
    api_key = os.getenv('API_KEY')

    if not api_key:
        return json.dumps({'error': 'API_KEY is not set'}, ensure_ascii=False), 500

    return json.dumps({'db_host': db_host}, ensure_ascii=False)
```

!!! tip "Note"
    For security reasons, the following keys and prefixes cannot be registered as environment variables:

    Common (all runtimes)

    - Shell/subprocess: `PATH`, `IFS`, `HOME`, `BASH_ENV`, `ENV`, `SHELLOPTS`
    - Loader/library: `LD_PRELOAD`, `LD_LIBRARY_PATH`, `LD_AUDIT`
    - glibc dynamic loading: `GCONV_PATH`, `LOCPATH`, `HOSTALIASES`
    - Proxy: `HTTP_PROXY`, `HTTPS_PROXY`, `ALL_PROXY` (including lowercase)
    - TLS trust store: `SSL_CERT_FILE`, `SSL_CERT_DIR`, `REQUESTS_CA_BUNDLE`, `CURL_CA_BUNDLE`
    - Platform internal: `RUNTIME_PORT`, `USERFUNCVOL`, `WSGI_FRAMEWORK`, `SENTRY_DSN`, `SENTRY_RELEASE`, `TIMEOUT`, `BODY_PARSER_LIMIT`
    - Prefixes: `LD_`, `DYLD_`, `KUBERNETES_`, `FISSION_`

    Python

    - `PYTHONPATH`, `PYTHONSTARTUP`, `PYTHONHOME`, `PYTHONEXECUTABLE`
    - Prefix `PYTHON`

<a id="configure-entry-point"></a>
## Configure Entry Point { #configure-entry-point }

<a id="single-function"></a>
### Single function { #single-function }
Use `filename.functionname` as the Entry Point.

File name: `user.py`
Function name: `main`
Entry Point: `user.main`

<a id="multiple-functions"></a>
### Multiple functions { #multiple-functions }
You can define multiple functions in a single file.

```python
# handlers.py
from flask import request
import json

def get_user():
    # User lookup logic
    return json.dumps({'message': 'Get user'}, ensure_ascii=False)

def create_user():
    # User creation logic
    return json.dumps({'message': 'User created'}, ensure_ascii=False)

def update_user():
    # User modification logic
    return json.dumps({'message': 'User updated'}, ensure_ascii=False)
```

Entry Point Configuration:
- `handlers.get_user`
- `handlers.create_user`
- `handlers.update_user`

<a id="caution"></a>
## Caution { #caution }

<a id="unsupported-packages"></a>
### Unsupported packages { #unsupported-packages }
Complex packages with the following features are not currently supported:

**Examples of unsupported packages:**
- `numpy`, `pandas` (C/C++ extension module required)
- `scipy` (system library dependencies)
- `tensorflow`, `pytorch` (complex initialization process)

**Examples of supported packages:**
- `requests` (HTTP client)
- `pyyaml` (YAML processing)
- `python-dateutil` (date/time processing)
- `pillow` (image processing - basic feature)

<a id="considerations-for-memory-and-execution-time"></a>
### Considerations for Memory and execution time { #considerations-for-memory-and-execution-time }
- Functions must operate within limited memory and execution time.
- Consider generator and stream processing when handling large-scale data.
- Split long-running tasks appropriately.
