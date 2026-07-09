<!-- pre-align:aligned sig=9f2d38073162 -->

<a id="compute-cloud-functions-code-template-guide-nodejs"></a>
## Compute > Cloud Functions > Code Template Guide > Node.js { #compute-cloud-functions-code-template-guide-nodejs }

This document details how to develop functions by using Node.js from NHN Cloud's Cloud Functions service.

<a id="template-information"></a>
## Template information { #template-information }
| Item           | Value                  |
|-----------------|-----------------|
| **Supported version**       | 20.16.0, 22.5.0 |
| **File name**         | hello.js        |
| **Entry Point** | hello           |

<a id="basic-template"></a>
## Basic template { #basic-template }
<a id="hello-world-example"></a>
### Hello World example { #hello-world-example }
A basic form of function.

```javascript
module.exports = async (context) => {
    return {
        status: 200,
        body: "Hello, World!\n"
    };
}
```

<a id="context-object"></a>
### Context object { #context-object }
'context' object sent to functions include:

```javascript
module.exports = async (context) => {
    // HTTP request information
    console.log('Method:', context.request.method);
    console.log('Headers:', context.request.headers);
    console.log('Query:', context.request.query);
    console.log('Body:', context.request.body);

    return {
        status: 200,
        body: JSON.stringify({
            message: "Context information logged"
        })
    };
}
```

<a id="download-and-use-template-file"></a>
## Download and use template file { #download-and-use-template-file }

<a id="template-download"></a>
### Template download { #template-download }
You can download the Node.js template provided by Cloud Functions to develop a local environment.

**Template download link**: [nodejs.zip](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/templates/nodejs/nodejs.zip)

<a id="template-file-structure"></a>
### Template file structure { #template-file-structure }
The structure of the downloaded template file is as follows:

```
nodejs.zip
├── hello.js          # Main function file
└── package.json      # Dependency management file
```

<a id="template-file-structure-hellojs"></a>
#### hello.js
Basic Hello World function is included.
```javascript
module.exports = async (context) => {
    return {
        status: 200,
        body: "hello, world!\n"
    };
}
```

<a id="template-file-structure-packagejson"></a>
#### package.json
```json
{}
```

<a id="local-development-process"></a>
### Local development process { #local-development-process }

<a id="local-development-process-unzip"></a>
#### 1. Unzip
```bash
# Unzip
unzip nodejs.zip -d my-function

# Move to task directory
cd my-function
```

<a id="local-development-process-modify-function-codes"></a>
#### 2. Modify function codes
Modify `hello.js` file with the logic you want.

```javascript
// hello.js - Simple modification example
module.exports = async (context) => {
    try {
        // Import name from query parameter
        const { name = 'World' } = context.request.query;

        // Import message from body if POST request
        let customMessage = '';
        if (context.request.method === 'POST' && context.request.body) {
            const body = context.request.body;
            customMessage = body.message || '';
        }

        return {
            status: 200,
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                greeting: `Hello, ${name}!`,
                message: customMessage,
                method: context.request.method,
                timestamp: new Date().toISOString()
            })
        };

    } catch (error) {
        return {
            status: 500,
            body: JSON.stringify({
                error: 'Internal Server Error',
                details: error.message
            })
        };
    }
}
```

<a id="local-development-process-compress-into-a-zip-file"></a>
#### 3. Compress into a ZIP file
Compress the modified source code into ZIP file.

```bash
# Windows (PowerShell)
Compress-Archive -Path .\hello.js, .\package.json -DestinationPath my-function.zip

# Windows (when using 7-Zip)
7z a my-function.zip hello.js package.json

# macOS/Linux
zip my-function.zip hello.js package.json

# Include all files (if any additional files are available)
zip -r my-function.zip . -x "*.git*" "node_modules/*" "test.js"
```

<a id="upload-from-cloud-functions-console"></a>
### Upload from Cloud Functions console { #upload-from-cloud-functions-console }
> Used when uploading files from the user's local environment when creating or modifying a function. (refer to Console Guide)

<a id="cautions-for-upload"></a>
### Cautions for upload { #cautions-for-upload }

<a id="cautions-for-upload-zip-file-structure"></a>
#### ZIP file structure
- The `.js` file and `package.json` must be located directly in the root of the ZIP file.
- We recommend avoiding unnecessary folder structures.

**Right structure:**
```
my-function.zip
├── hello.js
├── package.json
└── utils.js (if there are additional files)
```

**Wrong structure:**
```
my-function.zip
└── my-function/
    ├── hello.js
    └── package.json
```

<a id="cautions-for-upload-file-size-limit"></a>
#### File size limit
- ZIP file size is limited to 100MiB.
- `node_modules` file should not be included. (for dependencies, manageed as `package.json`)

<a id="cautions-for-upload-files-to-exclude"></a>
#### Files to exclude
```bash
# Similar to .gitignore, exclude the following files:
zip -r my-function.zip . -x \
  "node_modules/*" \
  ".git/*" \
  "*.log" \
  "test.js" \
  ".env" \
  "*.zip"
```

<a id="process-by-http-method"></a>
## Process by HTTP method { #process-by-http-method }

<a id="process-get-request"></a>
### Process GET request { #process-get-request }
```javascript
module.exports = async (context) => {
    if (context.request.method !== 'GET') {
        return {
            status: 405,
            body: JSON.stringify({ error: 'Method Not Allowed' })
        };
    }

    // Import query parameter
    const { name = 'World', greeting = 'Hello' } = context.request.query;

    return {
        status: 200,
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({
            message: `${greeting}, ${name}!`,
            timestamp: new Date().toISOString()
        })
    };
}
```

<a id="process-post-request"></a>
### Process POST request { #process-post-request }
```javascript
module.exports = async (context) => {
    if (context.request.method !== 'POST') {
        return {
            status: 405,
            body: JSON.stringify({ error: 'Method Not Allowed' })
        };
    }

    // Use request body as a JSON format
    const requestBody = context.request.body;

    // Validate required fields
    if (!requestBody.name) {
        return {
            status: 400,
            body: JSON.stringify({
                error: 'Missing required field: name'
            })
        };
    }

    const { name, email, message } = requestBody;

    // Processing logic
    const response = {
        id: Math.random().toString(36).substr(2, 9),
        name: name,
        email: email || 'not provided',
        message: message || 'No message',
        processed_at: new Date().toISOString()
    };

    return {
        status: 201,
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify(response)
    };

}
```

<a id="manage-packages"></a>
## Manage packages { #manage-packages }

<a id="write-packagejson"></a>
### Write package.json { #write-packagejson }
Write `package.json` to manage dependencies.

```json
{
  "name": "my-cloud-function",
  "version": "1.0.0",
  "description": "NHN Cloud Functions example",
  "main": "index.js",
  "dependencies": {
    "axios": "^1.6.0",
    "lodash": "^4.17.21",
    "moment": "^2.29.4",
    "uuid": "^9.0.1"
  }
}
```

<a id="example-of-external-api-call"></a>
### Example of external API call { #example-of-external-api-call }
```javascript
const axios = require('axios');

module.exports = async (context) => {
    try {
        const { userId } = context.request.query;

        if (!userId) {
            return {
                status: 400,
                body: JSON.stringify({ error: 'userId is required' })
            };
        }

        // External API call
        const response = await axios.get(`https://jsonplaceholder.typicode.com/users/${userId}`);

        return {
            status: 200,
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                user: response.data,
                fetched_at: new Date().toISOString()
            })
        };

    } catch (error) {
        console.error('API call failed:', error.message);

        if (error.response && error.response.status === 404) {
            return {
                status: 404,
                body: JSON.stringify({ error: 'User not found' })
            };
        }

        return {
            status: 500,
            body: JSON.stringify({
                error: 'Internal server error',
                details: error.message
            })
        };
    }
}
```

<a id="data-processing-example"></a>
### Data processing example { #data-processing-example }
```javascript
const _ = require('lodash');
const moment = require('moment');
const { v4: uuidv4 } = require('uuid');

module.exports = async (context) => {
    try {
        const requestBody = context.request.body;
        const { data } = requestBody;

        if (!Array.isArray(data)) {
            return {
                status: 400,
                body: JSON.stringify({ error: 'Data must be an array' })
            };
        }

        // Data processing
        const processedData = data.map(item => ({
            id: uuidv4(),
            ...item,
            processed_at: moment().format('YYYY-MM-DD HH:mm:ss'),
            normalized_name: _.capitalize(_.trim(item.name))
        }));

        // Data sorting and filtering
        const sortedData = _.orderBy(processedData, ['normalized_name'], ['asc']);
        const validData = sortedData.filter(item => item.normalized_name);

        return {
            status: 200,
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                total_processed: processedData.length,
                valid_items: validData.length,
                data: validData
            })
        };

    } catch (error) {
        return {
            status: 400,
            body: JSON.stringify({
                error: 'Data processing failed',
                details: error.message
            })
        };
    }
}
```

<a id="use-environment-variables"></a>
## Use Environment Variables { #use-environment-variables }
Environment variables registered for a function can be accessed via `process.env`. Environment variables are registered during the code writing step of function creation and modification. For more information, see the console user guide.

```javascript
module.exports = async (context) => {
    // Read environmet variables
    const dbHost = process.env.DB_HOST;
    const apiKey = process.env.API_KEY;

    if (!apiKey) {
        return {
            status: 500,
            body: JSON.stringify({ error: 'API_KEY is not set' })
        };
    }

    return {
        status: 200,
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ dbHost })
    };
}
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
    - Prefixes: `LD_`, `DYLD_`, `KUBERNETES_`, `FISSION_`, `CORECLR_`, `COMPLUS_`, `DOTNET_`, `ASPNETCORE_`, `PYTHON`, `NODE_`, `RUBY`

    Node.js

    - `NODE_PATH`, `NODE_OPTIONS`, `NODE_EXTRA_CA_CERTS`

<a id="configure-entry-point"></a>
## Configure Entry Point { #configure-entry-point }

<a id="single-function"></a>
### Single function { #single-function }
Use the function name as the Entry Point.

File name: `hello.js`
Entry Point: `hello`

<a id="multiple-functions"></a>
### Multiple functions { #multiple-functions }
You can define multiple functions in a single file.

```javascript
// users.js
module.exports.getUser = async (context) => {
    // User lookup logic
    return {
        status: 200,
        body: JSON.stringify({ message: "Get user" })
    };
}

module.exports.createUser = async (context) => {
    // User creation logic
    return {
        status: 201,
        body: JSON.stringify({ message: "User created" })
    };
}

module.exports.updateUser = async (context) => {
    // User modification logic
    return {
        status: 200,
        body: JSON.stringify({ message: "User updated" })
    };
}
```

Entry Point Configuration:
- `users.getUser`
- `users.createUser`
- `users.updateUser`

<a id="entry-point-restriction"></a>
### Entry Point restriction { #entry-point-restriction }
- **Subdirectory specification unavailable**: Files in subdirectories of the root directory cannot be specified as Entry Points.

**Right Entry Point:**
```
hello.js → hello
users.js → users.getUser
```

**Wrong Entry Point:**
```
lib/utils.js → lib.utils ❌
src/handlers.js → src.handlers ❌
modules/auth.js → modules.auth ❌
```

All function files must be located at the root level of the ZIP file.

<a id="caution"></a>
## Caution { #caution }

<a id="commonjs-vs-es-modules"></a>
### CommonJS vs ES Modules { #commonjs-vs-es-modules }
Cloud Functions currently only supports CommonJS method.

**Available (CommonJS):**
```javascript
const axios = require('axios');
module.exports = async (context) => {
    // Function logic
};
```

**Unavailable (ES Modules):**
```javascript
import axios from 'axios';  // ❌ Not supported
export default async (context) => {  // ❌ Not supported
    // Function logic
};
```

<a id="considerations-for-memory-and-execution-time"></a>
### Considerations for Memory and execution time { #considerations-for-memory-and-execution-time }
- Functions must operate within limited memory and execution time.
- Consider stream processing when handling large-scale data.
- Split long-running tasks appropriately.
