<!-- pre-align:aligned sig=f22611a38cd5 -->

<a id="cloud-functions-api-v10-guide"></a>
## Cloud Functions API v1.0 Guide { #cloud-functions-api-v10-guide }

**Compute > Cloud Functions > API Guide > API v1.0 Guide**

<a id="cloud-functions-api-v10-common-information"></a>
## Cloud Functions API v1.0 Common Information { #cloud-functions-api-v10-common-information }

<a id="api-endpoint"></a>
### API Endpoint { #api-endpoint }

The endpoints by region for calling the Cloud Functions API are as follows:

| Region | Endpoint                                               |
| --- |-----------------------------------------------------|
| Korea (Pangyo) Region | https://kr1-cloud-functions.api.nhncloudservice.com |

<a id="authentication-and-authorization"></a>
### Authentication and Authorization { #authentication-and-authorization }

Cloud Functions uses User Access Key tokens for authentication and authorization when making API calls.
The User Access Key token is a temporary, Bearer-type access token issued from a User Access Key.
For more information on issuing and using User Access Key tokens, refer to the [User Access Key Token](/nhncloud/en/public-api/user-access-key-token).

<a id="response-common-information"></a>
### Response Common Information { #response-common-information }

Every API response follows the common format as below:

<details>
  <summary><strong>Success response</strong></summary>

```json
{
  "header": {
    "isSuccessful": true,
    "resultCode": 0,
    "resultMessage": "success"
  },
  "data": {
    ...
  }
}
```

</details>

<details>
  <summary><strong>Failure response</strong></summary>

```json
{
  "header": {
    "isSuccessful": false,
    "resultCode": -1,
    "resultMessage": "Error message"
  },
  "data": null
}
```

</details>

| Name | Type | Description |
| --- | --- | --- |
| header | Object | Response header |
| header.isSuccessful | Boolean | Whether the API call was successful |
| header.resultCode | Integer | Result code (success: 0, failure: -1) |
| header.resultMessage | String | Result message |
| data | Object | Response data (varies by API) |

---

<a id="list-environments"></a>
## List Environments { #list-environments }

Retrieves a list of available runtime environments.

<a id="request"></a>
### Request { #request }

```
GET /v1.0/env/list
```

<a id="request-parameter"></a>
### Request Parameter { #request-parameter }

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |

<a id="request-body"></a>
### Request Body { #request-body }

This API does not require a request body.

<a id="response"></a>
### Response { #response }

<details>
  <summary><strong>Example code</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "success"
    },
    "data": [
        {
            "id": 1,
            "environment": "NodeJS",
            "version": "22.5.0",
            "entryPoint": "index.handler"
        },
        {
            "id": 2,
            "environment": "Python",
            "version": "3.9",
            "entryPoint": "main.handler"
        }
    ]
}
```

</details>

| Name | Type | Description |
| --- | --- | --- |
| data | Array | Environment list |
| data[].id | Integer | Environment ID |
| data[].environment | String | Runtime environment (e.g., NodeJS) |
| data[].version | String | Runtime version (e.g., 22.5.0) |
| data[].entryPoint | String | Default entry point |

---

<a id="list-functions"></a>
## List Functions { #list-functions }

Retrieves a list of functions.

<a id="list-functions-request"></a>
### Request { #list-functions-request }

```
GET /v1.0/functions
```

<a id="list-functions-request-parameter"></a>
### Request Parameter { #list-functions-request-parameter }

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| page | Query | Integer | N | Current page (default: 0) |
| pageSize | Query | Integer | N | Number of items to display per page (default: 5,000) |

<a id="list-functions-request-body"></a>
### Request Body { #list-functions-request-body }

This API does not require a request body.

<a id="list-functions-response"></a>
### Response { #list-functions-response }

<details>
  <summary><strong>Example code</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "success"
    },
    "data": {
        "totalCount": 2,
        "functions": [
            {
                "name": "my-function",
                "description": "sample function",
                "runtime": "NodeJS 22.5.0",
                "executorType": "poolmgr",
                "memory": 256,
                "timeout": 60,
                "buildStatus": "SUCCEEDED",
                "createdAt": "1700000000",
                "updatedAt": "2026-03-09T14:59:44Z"
            }
        ]
    }
}
```

</details>

| Name | Type | Description |
| --- | --- | --- |
| data.totalCount | Integer | Total number of functions |
| data.functions | Array | Function list |
| data.functions[].name | String | Function name |
| data.functions[].description | String | Function description |
| data.functions[].runtime | String | Runtime (e.g., NodeJS 22.5.0) |
| data.functions[].executorType | String | Execution type (poolmgr or newdeploy) |
| data.functions[].memory | Integer | Memory (MB) |
| data.functions[].timeout | Integer | Timeout (seconds) |
| data.functions[].buildStatus | String | Build status (PENDING, RUNNING, SUCCEEDED, FAILED) |
| data.functions[].createdAt | String | Creation time (epoch seconds) |
| data.functions[].updatedAt | String | Last modified time (ISO 8601, e.g., 2026-03-09T14:59:44Z) |

---

<a id="get-function"></a>
## Get Function { #get-function }

Retrieves detailed information and build logs for a function.

<a id="get-function-request"></a>
### Request { #get-function-request }

```
GET /v1.0/functions/{functionName}
```

<a id="get-function-request-parameter"></a>
### Request Parameter { #get-function-request-parameter }

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| functionName | URL | String | Y | Function name |

<a id="get-function-request-body"></a>
### Request Body { #get-function-request-body }

This API does not require a request body.

<a id="get-function-response"></a>
### Response { #get-function-response }

<details>
  <summary><strong>Example code</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "success"
    },
    "data": {
        "name": "my-function",
        "description": "sample function",
        "runtime": "NodeJS 22.5.0",
        "executorType": "poolmgr",
        "memory": 256,
        "timeout": 60,
        "buildStatus": "SUCCEEDED",
        "createdAt": "1700000000",
        "updatedAt": "2026-03-09T14:59:44Z",
        "entryPoint": "index.handler",
        "requestPerPod": 1,
        "minInstance": null,
        "maxInstance": null,
        "buildLog": "Build succeeded...",
        "currentVersionName": "v1",
        "sourceFileName": "source.zip",
        "lncsAppkey": null
    }
}
```

</details>

| Name | Type | Description |
| --- | --- | --- |
| data.name | String | Function name |
| data.description | String | Function description |
| data.runtime | String | Runtime (e.g., NodeJS 22.5.0) |
| data.executorType | String | Execution type (poolmgr or newdeploy) |
| data.memory | Integer | Memory (MB) |
| data.timeout | Integer | Timeout (seconds) |
| data.buildStatus | String | Build status (PENDING, RUNNING, SUCCEEDED, FAILED) |
| data.createdAt | String | Creation time (epoch seconds) |
| data.updatedAt | String | Last modified time (ISO 8601, e.g., 2026-03-09T14:59:44Z) |
| data.entryPoint | String | Function entry point |
| data.requestPerPod | Integer | Number of concurrent requests per pod |
| data.minInstance | Integer | Minimum number of instances |
| data.maxInstance | Integer | Maximum number of instances |
| data.buildLog | String | Build log |
| data.currentVersionName | String | Current version name |
| data.sourceFileName | String | Source file name |
| data.lncsAppkey | String | LnCS Appkey |

---

<a id="create-function"></a>
## Create Function { #create-function }

Creates a new function. Upload the source file as multipart/form-data.
The runtime must be entered in the `{environment}-{version}` format (e.g., NodeJS-22.5.0). Available runtimes can be checked using the List Environments API.

Required parameters vary depending on the executorType. When using poolManager, requestPerPod is required. When using newDeployment, minInstance and maxInstance are required.

<a id="create-function-request"></a>
### Request { #create-function-request }

```
POST /v1.0/functions
```

<a id="create-function-request-parameter"></a>
### Request Parameter { #create-function-request-parameter }

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |

<a id="create-function-request-body"></a>
### Request Body { #create-function-request-body }

Content-Type: multipart/form-data

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| name | String | Y | Function name |
| description | String | N | Function description |
| executorType | String | Y | Execution type (poolManager or newDeployment) |
| runtime | String | Y | Runtime ({environment}-{version} format, e.g., NodeJS-22.5.0) |
| entryPoint | String | Y | Function entry point |
| memory | Integer | Y | Memory (MB). Default resource set values: 128, 256, 512, 1,024, 2,048, 4,096. For newDeployment, custom values in the range of 64–4096 are also supported. |
| requestPerPod | Integer | Conditional | Number of concurrent requests per pod (1–1,000 when using poolManager, default: 1) |
| timeout | Integer | N | Timeout (seconds, 1–900, default: 60) |
| minInstance | Integer | Conditional | Minimum number of instances (required when using newDeployment, 1–100, must be less than or equal to maxInstance) |
| maxInstance | Integer | Conditional | Maximum number of instances (required when using newDeployment, 1–100) |
| lncsAppkey | String | N | LnCS Appkey |
| sourceFile | Binary | Y | Source code file (ZIP) |

<a id="create-function-response"></a>
### Response { #create-function-response }

<details>
  <summary><strong>Example code</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Successfully created the function."
    },
    "data": null
}
```

</details>

---

<a id="modify-function"></a>
## Modify Function { #modify-function }

Modifies a function. The source file can be uploaded as multipart/form-data.

The source file (sourceFile) is optional. If no source file is included, the existing source code is retained.

<a id="modify-function-request"></a>
### Request { #modify-function-request }

```
PUT /v1.0/functions/{functionName}
```

<a id="modify-function-request-parameter"></a>
### Request Parameter { #modify-function-request-parameter }

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| functionName | URL | String | Y | Name of the function to modify |

<a id="modify-function-request-body"></a>
### Request Body { #modify-function-request-body }

Content-Type: multipart/form-data

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| description | String | N | Function description |
| executorType | String | Y | Execution type (poolManager or newDeployment) |
| runtime | String | Y | Runtime ({environment}-{version} format, e.g., NodeJS-22.5.0) |
| entryPoint | String | Y | Function entry point |
| memory | Integer | Y | Memory (MB). Default resource set values: 128, 256, 512, 1,024, 2,048, 4,096. For newDeployment, custom values in the range of 64–4,096 are also supported. |
| requestPerPod | Integer | Conditional | Number of concurrent requests per pod (1–1,000 when using poolManager) |
| timeout | Integer | N | Timeout (seconds, 1–900, default: 60) |
| minInstance | Integer | Conditional | Minimum number of instances (required when using newDeployment, 1–100, must be less than or equal to maxInstance) |
| maxInstance | Integer | Conditional | Maximum number of instances (required when using newDeployment, 1–100) |
| lncsAppkey | String | N | LnCS Appkey |
| sourceFile | Binary | N | Source code file (ZIP) |

<a id="modify-function-response"></a>
### Response { #modify-function-response }

<details>
  <summary><strong>Example code</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Successfully updated the function."
    },
    "data": null
}
```

</details>

---

<a id="delete-functions"></a>
## Delete Functions { #delete-functions }

Deletes functions in bulk.

<a id="delete-functions-request"></a>
### Request { #delete-functions-request }

```
DELETE /v1.0/functions
```

<a id="delete-functions-request-parameter"></a>
### Request Parameter { #delete-functions-request-parameter }

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |

<a id="delete-functions-request-body"></a>
### Request Body { #delete-functions-request-body }

<details>
  <summary><strong>Example code</strong></summary>

```json
{
    "names": ["function-1", "function-2"]
}
```

</details>

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| names | Array | Y | Function name list to delete |

<a id="delete-functions-response"></a>
### Response { #delete-functions-response }

<details>
  <summary><strong>Example code</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Successfully deleted the function."
    },
    "data": null
}
```

</details>

---

<a id="execute-function-get"></a>
## Execute Function (GET) { #execute-function-get }

Executes a function using the GET method.

<a id="execute-function-get-request"></a>
### Request { #execute-function-get-request }

```
GET /v1.0/functions/{functionName}/invoke
```

<a id="execute-function-get-request-parameter"></a>
### Request Parameter { #execute-function-get-request-parameter }

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| functionName | URL | String | Y | Function name to execute |

<a id="execute-function-get-request-body"></a>
### Request Body { #execute-function-get-request-body }

This API does not require a request body.

<a id="execute-function-get-response"></a>
### Response { #execute-function-get-response }

<details>
  <summary><strong>Example code</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "success"
    },
    "data": "Hello, World!"
}
```

</details>

| Name | Type | Description |
| --- | --- | --- |
| data | String | Function execution result |

---

<a id="execute-function-post"></a>
## Execute Function (POST) { #execute-function-post }

Executes a function using the POST method. A request body can be included.

<a id="execute-function-post-request"></a>
### Request { #execute-function-post-request }

```
POST /v1.0/functions/{functionName}/invoke
```

<a id="execute-function-post-request-parameter"></a>
### Request Parameter { #execute-function-post-request-parameter }

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| functionName | URL | String | Y | Function name to execute |

<a id="execute-function-post-request-body"></a>
### Request Body { #execute-function-post-request-body }

<details>
  <summary><strong>Example code</strong></summary>

```json
{
    "key": "value"
}
```

</details>

| Name | Type | Required | Description |
| --- |------| --- | --- |
| body | JSON | N | Body to send to the function |

<a id="execute-function-post-response"></a>
### Response { #execute-function-post-response }

<details>
  <summary><strong>Example code</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "success"
    },
    "data": "Function output"
}
```

</details>

| Name | Type | Description |
| --- | --- | --- |
| data | String | Function execution result |

---

<a id="list-versions"></a>
## List Versions { #list-versions }

Retrieves a list of versions (packages) for a function.

<a id="list-versions-request"></a>
### Request { #list-versions-request }

```
GET /v1.0/functions/{functionName}/versions
```

<a id="list-versions-request-parameter"></a>
### Request Parameter { #list-versions-request-parameter }

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| functionName | URL | String | Y | Function name |

<a id="list-versions-request-body"></a>
### Request Body { #list-versions-request-body }

This API does not require a request body.

<a id="list-versions-response"></a>
### Response { #list-versions-response }

<details>
  <summary><strong>Example code</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "success"
    },
    "data": [
        {
            "versionId": 1,
            "versionName": "v1",
            "sourceFileName": "source.zip",
            "buildStatus": "SUCCEEDED",
            "createdAt": "2026-03-09T14:59:44Z",
            "isCurrent": true
        },
        {
            "versionId": 2,
            "versionName": "v2",
            "sourceFileName": "source-v2.zip",
            "buildStatus": "SUCCEEDED",
            "createdAt": "2026-03-10T18:45:00Z",
            "isCurrent": false
        }
    ]
}
```

</details>

| Name | Type | Description |
| --- | --- | --- |
| data | Array | Version list |
| data[].versionId | Integer | Version ID |
| data[].versionName | String | Version name |
| data[].sourceFileName | String | Source file name |
| data[].buildStatus | String | Build status (PENDING, RUNNING, SUCCEEDED, FAILED) |
| data[].createdAt | String | Creation time (ISO 8601, e.g., 2026-03-09T14:59:44Z) |
| data[].isCurrent | Boolean | Whether the version is currently active |

---

<a id="switch-version"></a>
## Switch Version { #switch-version }

Changes the currently active version of a function.

<a id="switch-version-request"></a>
### Request { #switch-version-request }

```
PUT /v1.0/functions/{functionName}/versions
```

<a id="switch-version-request-parameter"></a>
### Request Parameter { #switch-version-request-parameter }

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| functionName | URL | String | Y | Function name |

<a id="switch-version-request-body"></a>
### Request Body { #switch-version-request-body }

<details>
  <summary><strong>Example code</strong></summary>

```json
{
    "versionId": 12345
}
```

</details>

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| versionId | Integer | Y | Version ID to convert |

<a id="switch-version-response"></a>
### Response { #switch-version-response }

<details>
  <summary><strong>Example code</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "success"
    },
    "data": null
}
```

</details>

---

<a id="delete-versions"></a>
## Delete Versions { #delete-versions }

Deletes versions of a function in bulk.

The currently active version cannot be deleted.

<a id="delete-versions-request"></a>
### Request { #delete-versions-request }

```
DELETE /v1.0/functions/{functionName}/versions
```

<a id="delete-versions-request-parameter"></a>
### Request Parameter { #delete-versions-request-parameter }

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| functionName | URL | String | Y | Function name |

<a id="delete-versions-request-body"></a>
### Request Body { #delete-versions-request-body }

<details>
  <summary><strong>Example code</strong></summary>

```json
{
    "versionIds": [123, 456]
}
```

</details>

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| versionIds | Array | Y | Version ID list to delete |

<a id="delete-versions-response"></a>
### Response { #delete-versions-response }

<details>
  <summary><strong>Example code</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "success"
    },
    "data": null
}
```

</details>

---

<a id="list-triggers"></a>
## List Triggers { #list-triggers }

Retrieves a list of triggers for a function.

<a id="list-triggers-request"></a>
### Request { #list-triggers-request }

```
GET /v1.0/triggers/{functionName}
```

<a id="list-triggers-request-parameter"></a>
### Request Parameter { #list-triggers-request-parameter }

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| functionName | URL | String | Y | Function name |

<a id="list-triggers-request-body"></a>
### Request Body { #list-triggers-request-body }

This API does not require a request body.

<a id="list-triggers-response"></a>
### Response { #list-triggers-response }

<details>
  <summary><strong>Example code</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "success"
    },
    "data": [
        {
            "type": "http",
            "name": "http-trigger-1",
            "value": "https://example.com/trigger/path",
            "isActivated": true
        },
        {
            "type": "time",
            "name": "time-trigger-1",
            "value": "0 0 * * *",
            "isActivated": true
        }
    ]
}
```

</details>

| Name | Type | Description |
| --- | --- | --- |
| data | Array | Trigger list |
| data[].type | String | Trigger type (http, time, api-gateway) |
| data[].name | String | Trigger name |
| data[].value | String | Trigger value (URL or cron expression) |
| data[].isActivated | Boolean | Whether the trigger is activated |

---

<a id="create-time-trigger"></a>
## Create Time Trigger { #create-time-trigger }

Creates a time trigger for a function.

<a id="create-time-trigger-request"></a>
### Request { #create-time-trigger-request }

```
POST /v1.0/triggers/{functionName}/time
```

<a id="create-time-trigger-request-parameter"></a>
### Request Parameter { #create-time-trigger-request-parameter }

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| functionName | URL | String | Y | Function name |

<a id="create-time-trigger-request-body"></a>
### Request Body { #create-time-trigger-request-body }

<details>
  <summary><strong>Example code</strong></summary>

```json
{
    "cron": "0 0 * * *"
}
```

</details>

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| cron | String | Y | cron expression |

<a id="create-time-trigger-response"></a>
### Response { #create-time-trigger-response }

<details>
  <summary><strong>Example code</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Successfully created the trigger."
    },
    "data": null
}
```

</details>

---

<a id="modify-time-trigger"></a>
## Modify Time Trigger { #modify-time-trigger }

Modifies the cron expression of a time trigger.

<a id="modify-time-trigger-request"></a>
### Request { #modify-time-trigger-request }

```
PUT /v1.0/triggers/{functionName}/time
```

<a id="modify-time-trigger-request-parameter"></a>
### Request Parameter { #modify-time-trigger-request-parameter }

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| functionName | URL | String | Y | Function name |

<a id="modify-time-trigger-request-body"></a>
### Request Body { #modify-time-trigger-request-body }

<details>
  <summary><strong>Example code</strong></summary>

```json
{
    "name": "time-trigger-1",
    "cron": "0 0 * * *"
}
```

</details>

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| name | String | Y | Trigger name |
| cron | String | Y | cron expression |

<a id="modify-time-trigger-response"></a>
### Response { #modify-time-trigger-response }

<details>
  <summary><strong>Example code</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Successfully modified the trigger."
    },
    "data": null
}
```

</details>

---

<a id="delete-time-triggers"></a>
## Delete Time Triggers { #delete-time-triggers }

Deletes time triggers in bulk.

<a id="delete-time-triggers-request"></a>
### Request { #delete-time-triggers-request }

```
DELETE /v1.0/triggers/{functionName}/time
```

<a id="delete-time-triggers-request-parameter"></a>
### Request Parameter { #delete-time-triggers-request-parameter }

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| functionName | URL | String | Y | Function name |

<a id="delete-time-triggers-request-body"></a>
### Request Body { #delete-time-triggers-request-body }

<details>
  <summary><strong>Example code</strong></summary>

```json
{
  "names": ["time-trigger-1", "time-trigger-2"]
}
```

</details>

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| names | Array | Y | List of trigger names to delete |

<a id="delete-time-triggers-response"></a>
### Response { #delete-time-triggers-response }

<details>
  <summary><strong>Example code</strong></summary>

```json
{
  "header": {
    "isSuccessful": true,
    "resultCode": 0,
    "resultMessage": "Successfully deleted the trigger."
  },
  "data": null
}
```

</details>
