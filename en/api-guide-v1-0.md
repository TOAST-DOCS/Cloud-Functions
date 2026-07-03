## Cloud Functions API v1.0 Guide

**Compute > Cloud Functions > API Guide > API v1.0 Guide**

## Cloud Functions API v1.0 Common Information

### API Endpoint

The endpoints by region for calling the Cloud Functions API are as follows:

| Region | Endpoint                                               |
| --- |-----------------------------------------------------|
| Korea (Pangyo) Region | https://kr1-cloud-functions.api.nhncloudservice.com |

### Authentication and Authorization

Cloud Functions uses User Access Key tokens for authentication and authorization when making API calls.
The User Access Key token is a temporary, Bearer-type access token issued from a User Access Key.
For more information on issuing and using User Access Key tokens, refer to the [User Access Key Token](/nhncloud/en/public-api/user-access-key-token).

### Response Common Information

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

### 런타임 EOL 상태 공통 필드

함수 및 환경 목록 조회 응답에는 런타임의 EOL(End of Life) 상태 정보가 포함됩니다.

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| runtimeStatus | String | 런타임 상태: `NORMAL`(정상) / `DEPRECATED`(지원 중단) / `DISCONTINUED`(사용 중단) |
| deprecatedAt | String | 지원 중단일(ISO 8601, Asia/Seoul 기준). 없으면 null |
| discontinuedAt | String | 사용 중단일(ISO 8601, Asia/Seoul 기준). 없으면 null |

- 상태 판정: `discontinuedAt`이 현재 시각 이전이면 `DISCONTINUED`, `deprecatedAt`이 현재 시각 이전이면 `DEPRECATED`, 그 외에는 `NORMAL`입니다.
- `DISCONTINUED` 런타임은 환경 목록 조회에서 제외되며, 해당 런타임을 사용하는 함수는 수정할 수 없습니다.

---

## List Environments

Retrieves a list of available runtime environments.

### Request

```
GET /v1.0/env/list
```

### Request Parameter

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |

### Request Body

This API does not require a request body.

### Response

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
            "entryPoint": "index.handler",
            "runtimeStatus": "NORMAL",
            "deprecatedAt": "2027-04-30T00:00:00",
            "discontinuedAt": "2027-10-30T00:00:00"
        },
        {
            "id": 2,
            "environment": "NodeJS",
            "version": "20.16.0",
            "entryPoint": "index.handler",
            "runtimeStatus": "DEPRECATED",
            "deprecatedAt": "2026-04-30T00:00:00",
            "discontinuedAt": "2026-10-30T00:00:00"
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
| data[].runtimeStatus | String | 런타임 EOL 상태(`NORMAL`, `DEPRECATED`). 사용 중단(`DISCONTINUED`) 런타임은 목록에서 제외됩니다. |
| data[].deprecatedAt | String | 지원 중단일(ISO 8601). 없으면 null |
| data[].discontinuedAt | String | 사용 중단일(ISO 8601). 없으면 null |

---

## List Functions

Retrieves a list of functions.

### Request

```
GET /v1.0/functions
```

### Request Parameter

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| page | Query | Integer | N | Current page (default: 0) |
| pageSize | Query | Integer | N | Number of items to display per page (default: 5,000) |

### Request Body

This API does not require a request body.

### Response

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
                "updatedAt": "2026-03-09T14:59:44Z",
                "runtimeStatus": "NORMAL",
                "deprecatedAt": "2027-04-30T00:00:00",
                "discontinuedAt": "2027-10-30T00:00:00"
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
| data.functions[].runtimeStatus | String | 런타임 EOL 상태(`NORMAL`, `DEPRECATED`, `DISCONTINUED`) |
| data.functions[].deprecatedAt | String | 지원 중단일(ISO 8601). 없으면 null |
| data.functions[].discontinuedAt | String | 사용 중단일(ISO 8601). 없으면 null |

---

## Get Function

Retrieves detailed information and build logs for a function.

### Request

```
GET /v1.0/functions/{functionName}
```

### Request Parameter

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| functionName | URL | String | Y | Function name |

### Request Body

This API does not require a request body.

### Response

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
        "lncsAppkey": null,
        "runtimeStatus": "NORMAL",
        "deprecatedAt": "2027-04-30T00:00:00",
        "discontinuedAt": "2027-10-30T00:00:00",
        "envVars": {
            "DB_HOST": "10.0.0.1",
            "LOG_LEVEL": "info"
        }
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
| data.runtimeStatus | String | 런타임 EOL 상태(`NORMAL`, `DEPRECATED`, `DISCONTINUED`) |
| data.deprecatedAt | String | 지원 중단일(ISO 8601). 없으면 null |
| data.discontinuedAt | String | 사용 중단일(ISO 8601). 없으면 null |
| data.envVars | Object | 함수에 설정된 환경 변수(키-값). 단건 조회에서만 제공되며, 환경 변수가 없으면 null |

---

## Create Function

Creates a new function. Upload the source file as multipart/form-data.
The runtime must be entered in the `{environment}-{version}` format (e.g., NodeJS-22.5.0). Available runtimes can be checked using the List Environments API.

Required parameters vary depending on the executorType. When using poolManager, requestPerPod is required. When using newDeployment, minInstance and maxInstance are required.

환경 변수는 `envVars` 필드에 JSON 문자열로 전달합니다. 함수당 최대 100개, 키는 `^[A-Za-z_][A-Za-z0-9_]*$`(최대 128자, 중복 불가), 값은 최대 4,096자이며, 보안상 예약된 키는 등록할 수 없습니다. 잘못된 JSON이면 실패 응답을 반환합니다.

### Request

```
POST /v1.0/functions
```

### Request Parameter

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |

### Request Body

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
| envVars | String | N          | 환경 변수(JSON 문자열, 예: `{"DB_HOST":"10.0.0.1"}`). 미지정 시 환경 변수 없음. 잘못된 JSON이면 실패 응답 |
| sourceFile | Binary | Y | Source code file (ZIP) |

### Response

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

## Modify Function

Modifies a function. The source file can be uploaded as multipart/form-data.

The source file (sourceFile) is optional. If no source file is included, the existing source code is retained.

환경 변수는 `envVars` 필드에 JSON 문자열로 전달합니다. 미지정 시 기존 환경 변수가 유지되고, `"{}"`이면 전체 삭제, 값이 있으면 전체 교체됩니다. 제약 조건은 함수 생성과 동일합니다.

사용 중단(`DISCONTINUED`)된 런타임을 사용하는 함수는 수정할 수 없습니다. 요청 시 실패 응답(`header.isSuccessful`이 `false`)과 함께 "사용 중단된 런타임으로 함수를 수정할 수 없습니다. 최신 런타임으로 함수를 새로 생성해주세요." 메시지가 반환됩니다.

### Request

```
PUT /v1.0/functions/{functionName}
```

### Request Parameter

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| functionName | URL | String | Y | Name of the function to modify |

### Request Body

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
| envVars | String | N          | 환경 변수(JSON 문자열). 미지정 시 기존 유지, `"{}"`이면 전체 삭제, 값이 있으면 전체 교체. 잘못된 JSON이면 실패 응답 |
| sourceFile | Binary | N | Source code file (ZIP) |

### Response

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

## Delete Functions

Deletes functions in bulk.

### Request

```
DELETE /v1.0/functions
```

### Request Parameter

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |

### Request Body

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

### Response

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

## Execute Function (GET)

Executes a function using the GET method.

### Request

```
GET /v1.0/functions/{functionName}/invoke
```

### Request Parameter

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| functionName | URL | String | Y | Function name to execute |

### Request Body

This API does not require a request body.

### Response

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

## Execute Function (POST)

Executes a function using the POST method. A request body can be included.

### Request

```
POST /v1.0/functions/{functionName}/invoke
```

### Request Parameter

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| functionName | URL | String | Y | Function name to execute |

### Request Body

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

### Response

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

## List Versions

Retrieves a list of versions (packages) for a function.

### Request

```
GET /v1.0/functions/{functionName}/versions
```

### Request Parameter

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| functionName | URL | String | Y | Function name |

### Request Body

This API does not require a request body.

### Response

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

## Switch Version

Changes the currently active version of a function.

### Request

```
PUT /v1.0/functions/{functionName}/versions
```

### Request Parameter

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| functionName | URL | String | Y | Function name |

### Request Body

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

### Response

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

## Delete Versions

Deletes versions of a function in bulk.

The currently active version cannot be deleted.

### Request

```
DELETE /v1.0/functions/{functionName}/versions
```

### Request Parameter

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| functionName | URL | String | Y | Function name |

### Request Body

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

### Response

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

## List Triggers

Retrieves a list of triggers for a function.

### Request

```
GET /v1.0/triggers/{functionName}
```

### Request Parameter

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| functionName | URL | String | Y | Function name |

### Request Body

This API does not require a request body.

### Response

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

## Create Time Trigger

Creates a time trigger for a function.

### Request

```
POST /v1.0/triggers/{functionName}/time
```

### Request Parameter

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| functionName | URL | String | Y | Function name |

### Request Body

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

### Response

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

## Modify Time Trigger

Modifies the cron expression of a time trigger.

### Request

```
PUT /v1.0/triggers/{functionName}/time
```

### Request Parameter

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| functionName | URL | String | Y | Function name |

### Request Body

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

### Response

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

## Delete Time Triggers

Deletes time triggers in bulk.

### Request

```
DELETE /v1.0/triggers/{functionName}/time
```

### Request Parameter

| Name | Category | Type | Required | Description |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | User token (Bearer {token}) |
| functionName | URL | String | Y | Function name |

### Request Body

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

### Response

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
