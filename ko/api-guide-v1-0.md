## Cloud Functions API v1.0 가이드

**Compute > Cloud Functions > API 가이드 > API v1.0 가이드**

## Cloud Functions API v1.0 공통 정보

### API 엔드포인트

Cloud Functions API를 호출하기 위한 리전별 엔드포인트는 다음과 같습니다.

| 리전 | 엔드포인트                                               |
| --- |-----------------------------------------------------|
| 한국(판교) 리전 | https://kr1-cloud-functions.api.nhncloudservice.com |

### 인증 및 권한

Cloud Functions는 API 호출 시 인증/인가를 위해 User Access Key 토큰을 사용합니다.
User Access Key 토큰은 User Access Key를 기반으로 발급되는 Bearer 타입의 일시적 액세스 토큰입니다.
User Access Key 토큰 발급 및 사용에 대한 자세한 내용은 [User Access Key 토큰](/nhncloud/ko/public-api/user-access-key-token)을 참고하세요.

### 응답 공통 정보

모든 API 응답은 다음과 같은 공통 형식을 따릅니다.

<details>
  <summary><strong>성공 응답</strong></summary>

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
  <summary><strong>실패 응답</strong></summary>

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

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| header | Object | 응답 헤더 |
| header.isSuccessful | Boolean | API 호출 성공 여부 |
| header.resultCode | Integer | 결과 코드(성공: 0, 실패: -1) |
| header.resultMessage | String | 결과 메시지 |
| data | Object | 응답 데이터(API별 상이) |

---

## 환경 목록 조회

사용 가능한 런타임 환경 목록을 조회합니다.

### 요청

```
GET /v1.0/env/list
```

### 요청 파라미터

| 이름 | 구분 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | 앱키 |
| X-NHN-authorization | Header | String | Y | 사용자 토큰(Bearer {token}) |

### 요청 본문

이 API는 요청 본문을 요구하지 않습니다.

### 응답

<details>
  <summary><strong>예시 코드</strong></summary>

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

| 이름 | 타입 | 설명                |
| --- | --- |-------------------|
| data | Array | 환경 목록             |
| data[].id | Integer | 환경 ID             |
| data[].environment | String | 런타임 환경(예: NodeJS) |
| data[].version | String | 런타임 버전(예: 22.5.0)     |
| data[].entryPoint | String | 기본 진입점            |

---

## 함수 목록 조회

함수 목록을 조회합니다.

### 요청

```
GET /v1.0/functions
```

### 요청 파라미터

| 이름 | 구분 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | 앱키 |
| X-NHN-authorization | Header | String | Y | 사용자 토큰(Bearer {token}) |
| page | Query | Integer | N | 현재 페이지(기본값: 0) |
| pageSize | Query | Integer | N | 한 페이지에 노출될 개수 |

### 요청 본문

이 API는 요청 본문을 요구하지 않습니다.

### 응답

<details>
  <summary><strong>예시 코드</strong></summary>

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
                "description": "샘플 함수",
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

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| data.totalCount | Integer | 전체 함수 수 |
| data.functions | Array | 함수 목록 |
| data.functions[].name | String | 함수 이름 |
| data.functions[].description | String | 함수 설명 |
| data.functions[].runtime | String | 런타임(예: NodeJS 22.5.0) |
| data.functions[].executorType | String | 실행 타입(poolmgr 또는 newdeploy) |
| data.functions[].memory | Integer | 메모리(MB) |
| data.functions[].timeout | Integer | 타임아웃(초) |
| data.functions[].buildStatus | String | 빌드 상태(PENDING, RUNNING, SUCCEEDED, FAILED) |
| data.functions[].createdAt | String | 생성 시간(epoch seconds) |
| data.functions[].updatedAt | String | 수정 시간(ISO 8601, 예: 2026-03-09T14:59:44Z) |

---

## 함수 상세 조회

함수의 상세 정보와 빌드 로그를 함께 조회합니다.

### 요청

```
GET /v1.0/functions/{functionName}
```

### 요청 파라미터

| 이름 | 구분 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | 앱키 |
| X-NHN-authorization | Header | String | Y | 사용자 토큰(Bearer {token}) |
| functionName | URL | String | Y | 함수 이름 |

### 요청 본문

이 API는 요청 본문을 요구하지 않습니다.

### 응답

<details>
  <summary><strong>예시 코드</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "success"
    },
    "data": {
        "name": "my-function",
        "description": "샘플 함수",
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

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| data.name | String | 함수 이름 |
| data.description | String | 함수 설명 |
| data.runtime | String | 런타임(예: NodeJS 22.5.0) |
| data.executorType | String | 실행 타입(poolmgr 또는 newdeploy) |
| data.memory | Integer | 메모리(MB) |
| data.timeout | Integer | 타임아웃(초) |
| data.buildStatus | String | 빌드 상태(PENDING, RUNNING, SUCCEEDED, FAILED) |
| data.createdAt | String | 생성 시간(epoch seconds) |
| data.updatedAt | String | 수정 시간(ISO 8601, 예: 2026-03-09T14:59:44Z) |
| data.entryPoint | String | 함수 진입점 |
| data.requestPerPod | Integer | Pod당 동시 요청 수 |
| data.minInstance | Integer | 최소 인스턴스 수 |
| data.maxInstance | Integer | 최대 인스턴스 수 |
| data.buildLog | String | 빌드 로그 |
| data.currentVersionName | String | 현재 버전 이름 |
| data.sourceFileName | String | 소스 파일 이름 |
| data.lncsAppkey | String | LnCS 앱키 |

---

## 함수 생성

새 함수를 생성합니다. multipart/form-data로 소스 파일을 업로드합니다.
runtime은 `{environment}-{version}` 형식으로 입력해야 합니다(예: NodeJS-22.5.0). 사용 가능한 런타임은 환경 목록 조회 API로 확인할 수 있습니다.

executorType에 따라 필수 파라미터가 달라집니다. poolManager일 때는 requestPerPod가, newDeployment일 때는 minInstance와 maxInstance가 필수입니다.

### 요청

```
POST /v1.0/functions
```

### 요청 파라미터

| 이름 | 구분 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | 앱키 |
| X-NHN-authorization | Header | String | Y | 사용자 토큰(Bearer {token}) |

### 요청 본문

Content-Type: multipart/form-data

| 이름 | 타입 | 필수         | 설명                                                                                                |
| --- | --- |------------|---------------------------------------------------------------------------------------------------|
| name | String | Y          | 함수 이름                                                                                             |
| description | String | N          | 함수 설명                                                                                             |
| executorType | String | Y          | 실행 타입(poolManager 또는 newDeployment)                                                               |
| runtime | String | Y          | 런타임({environment}-{version} 형식, 예: NodeJS-22.5.0)                                                 |
| entryPoint | String | Y          | 함수 진입점                                                                                            |
| memory | Integer | Y          | 메모리(MB). 기본 리소스 세트 값: 128, 256, 512, 1024, 2048, 4096. newDeployment의 경우 64~4096 범위의 커스텀 값도 사용 가능 |
| requestPerPod | Integer | Conditional | Pod당 동시 요청 수(poolManager일 때 1~1000, 기본값: 1)                                                       |
| timeout | Integer | Y          | 타임아웃(초, 1~900)                                                                                    |
| minInstance | Integer | Conditional | 최소 인스턴스 수(newDeployment일 때 필수, 1~100, maxInstance 이하)                                             |
| maxInstance | Integer | Conditional | 최대 인스턴스 수(newDeployment일 때 필수, 1~100)                                                             |
| lncsAppkey | String | N          | LnCS 앱키                                                                                           |
| sourceFile | Binary | Y          | 소스 코드 파일(ZIP)                                                                                     |

### 응답

<details>
  <summary><strong>예시 코드</strong></summary>

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

## 함수 수정

함수를 수정합니다. multipart/form-data로 소스 파일을 업로드할 수 있습니다.

소스 파일(sourceFile)은 선택 항목입니다. 소스 파일을 포함하지 않으면 기존 소스 코드가 유지됩니다.

### 요청

```
PUT /v1.0/functions/{functionName}
```

### 요청 파라미터

| 이름 | 구분 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | 앱키 |
| X-NHN-authorization | Header | String | Y | 사용자 토큰(Bearer {token}) |
| functionName | URL | String | Y | 수정할 함수 이름 |

### 요청 본문

Content-Type: multipart/form-data

| 이름 | 타입 | 필수         | 설명                                                                                                |
| --- | --- |------------|---------------------------------------------------------------------------------------------------|
| description | String | N          | 함수 설명                                                                                             |
| executorType | String | Y          | 실행 타입(poolManager 또는 newDeployment)                                                               |
| runtime | String | Y          | 런타임({environment}-{version} 형식, 예: NodeJS-22.5.0)                                                     |
| entryPoint | String | Y          | 함수 진입점                                                                                            |
| memory | Integer | Y          | 메모리(MB). 기본 리소스 세트 값: 128, 256, 512, 1024, 2048, 4096. newDeployment의 경우 64~4096 범위의 커스텀 값도 사용 가능 |
| requestPerPod | Integer | Conditional | Pod당 동시 요청 수(poolManager일 때 1~1000)                                                               |
| timeout | Integer | Y          | 타임아웃(초, 1~900)                                                                                    |
| minInstance | Integer | Conditional | 최소 인스턴스 수(newDeployment일 때 필수, 1~100, maxInstance 이하)                                             |
| maxInstance | Integer | Conditional | 최대 인스턴스 수(newDeployment일 때 필수, 1~100)                                                             |
| lncsAppkey | String | N          | LnCS 앱키                                                                                           |
| sourceFile | Binary | N          | 소스 코드 파일(ZIP)                                                                                     |

### 응답

<details>
  <summary><strong>예시 코드</strong></summary>

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

## 함수 삭제

함수를 일괄 삭제합니다.

### 요청

```
DELETE /v1.0/functions
```

### 요청 파라미터

| 이름 | 구분 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | 앱키 |
| X-NHN-authorization | Header | String | Y | 사용자 토큰(Bearer {token}) |

### 요청 본문

<details>
  <summary><strong>예시 코드</strong></summary>

```json
{
    "names": ["function-1", "function-2"]
}
```

</details>

| 이름 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- |
| names | Array | Y | 삭제할 함수 이름 목록 |

### 응답

<details>
  <summary><strong>예시 코드</strong></summary>

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

## 함수 실행(GET)

GET 방식으로 함수를 실행합니다.

### 요청

```
GET /v1.0/functions/{functionName}/invoke
```

### 요청 파라미터

| 이름 | 구분 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | 앱키 |
| X-NHN-authorization | Header | String | Y | 사용자 토큰(Bearer {token}) |
| functionName | URL | String | Y | 실행할 함수 이름 |

### 요청 본문

이 API는 요청 본문을 요구하지 않습니다.

### 응답

<details>
  <summary><strong>예시 코드</strong></summary>

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

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| data | String | 함수 실행 결과 |

---

## 함수 실행(POST)

POST 방식으로 함수를 실행합니다. body를 전달할 수 있습니다.

### 요청

```
POST /v1.0/functions/{functionName}/invoke
```

### 요청 파라미터

| 이름 | 구분 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | 앱키 |
| X-NHN-authorization | Header | String | Y | 사용자 토큰(Bearer {token}) |
| functionName | URL | String | Y | 실행할 함수 이름 |

### 요청 본문

<details>
  <summary><strong>예시 코드</strong></summary>

```json
{
    "key": "value"
}
```

</details>

| 이름 | 타입   | 필수 | 설명 |
| --- |------| --- | --- |
| body | JSON | N | 함수에 전달할 body |

### 응답

<details>
  <summary><strong>예시 코드</strong></summary>

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

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| data | String | 함수 실행 결과 |

---

## 버전 목록 조회

함수의 버전(패키지) 목록을 조회합니다.

### 요청

```
GET /v1.0/functions/{functionName}/versions
```

### 요청 파라미터

| 이름 | 구분 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | 앱키 |
| X-NHN-authorization | Header | String | Y | 사용자 토큰(Bearer {token}) |
| functionName | URL | String | Y | 함수 이름 |

### 요청 본문

이 API는 요청 본문을 요구하지 않습니다.

### 응답

<details>
  <summary><strong>예시 코드</strong></summary>

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

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| data | Array | 버전 목록 |
| data[].versionId | Integer | 버전 ID |
| data[].versionName | String | 버전 이름 |
| data[].sourceFileName | String | 소스 파일 이름 |
| data[].buildStatus | String | 빌드 상태(PENDING, RUNNING, SUCCEEDED, FAILED) |
| data[].createdAt | String | 생성 시간(ISO 8601, 예: 2026-03-09T14:59:44Z) |
| data[].isCurrent | Boolean | 현재 활성 버전 여부 |

---

## 버전 전환

함수의 현재 활성 버전을 변경합니다.

### 요청

```
PUT /v1.0/functions/{functionName}/versions
```

### 요청 파라미터

| 이름 | 구분 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | 앱키 |
| X-NHN-authorization | Header | String | Y | 사용자 토큰(Bearer {token}) |
| functionName | URL | String | Y | 함수 이름 |

### 요청 본문

<details>
  <summary><strong>예시 코드</strong></summary>

```json
{
    "versionId": 12345
}
```

</details>

| 이름 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- |
| versionId | Integer | Y | 전환할 버전 ID |

### 응답

<details>
  <summary><strong>예시 코드</strong></summary>

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

## 버전 삭제

함수의 버전을 일괄 삭제합니다.

현재 활성 버전은 삭제할 수 없습니다.

### 요청

```
DELETE /v1.0/functions/{functionName}/versions
```

### 요청 파라미터

| 이름 | 구분 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | 앱키 |
| X-NHN-authorization | Header | String | Y | 사용자 토큰(Bearer {token}) |
| functionName | URL | String | Y | 함수 이름 |

### 요청 본문

<details>
  <summary><strong>예시 코드</strong></summary>

```json
{
    "versionIds": [123, 456]
}
```

</details>

| 이름 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- |
| versionIds | Array | Y | 삭제할 버전 ID 목록 |

### 응답

<details>
  <summary><strong>예시 코드</strong></summary>

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

## 트리거 목록 조회

함수의 트리거 목록을 조회합니다.

### 요청

```
GET /v1.0/triggers/{functionName}
```

### 요청 파라미터

| 이름 | 구분 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | 앱키 |
| X-NHN-authorization | Header | String | Y | 사용자 토큰(Bearer {token}) |
| functionName | URL | String | Y | 함수 이름 |

### 요청 본문

이 API는 요청 본문을 요구하지 않습니다.

### 응답

<details>
  <summary><strong>예시 코드</strong></summary>

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

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| data | Array | 트리거 목록 |
| data[].type | String | 트리거 타입(http, time, api-gateway) |
| data[].name | String | 트리거 이름 |
| data[].value | String | 트리거 값(URL 또는 cron 표현식) |
| data[].isActivated | Boolean | 활성화 여부 |

---

## 타임 트리거 생성

함수에 타임 트리거를 생성합니다.

### 요청

```
POST /v1.0/triggers/{functionName}/time
```

### 요청 파라미터

| 이름 | 구분 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | 앱키 |
| X-NHN-authorization | Header | String | Y | 사용자 토큰(Bearer {token}) |
| functionName | URL | String | Y | 함수 이름 |

### 요청 본문

<details>
  <summary><strong>예시 코드</strong></summary>

```json
{
    "cron": "0 0 * * *"
}
```

</details>

| 이름 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- |
| cron | String | Y | cron 표현식 |

### 응답

<details>
  <summary><strong>예시 코드</strong></summary>

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

## 타임 트리거 수정

타임 트리거의 cron 표현식을 수정합니다.

### 요청

```
PUT /v1.0/triggers/{functionName}/time
```

### 요청 파라미터

| 이름 | 구분 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | 앱키 |
| X-NHN-authorization | Header | String | Y | 사용자 토큰(Bearer {token}) |
| functionName | URL | String | Y | 함수 이름 |

### 요청 본문

<details>
  <summary><strong>예시 코드</strong></summary>

```json
{
    "name": "time-trigger-1",
    "cron": "0 0 * * *"
}
```

</details>

| 이름 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- |
| name | String | Y | 트리거 이름 |
| cron | String | Y | cron 표현식 |

### 응답

<details>
  <summary><strong>예시 코드</strong></summary>

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

## 타임 트리거 삭제

타임 트리거를 일괄 삭제합니다.

### 요청

```
DELETE /v1.0/triggers/{functionName}/time
```

### 요청 파라미터

| 이름 | 구분 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | 앱키 |
| X-NHN-authorization | Header | String | Y | 사용자 토큰(Bearer {token}) |
| functionName | URL | String | Y | 함수 이름 |

### 요청 본문

<details>
  <summary><strong>예시 코드</strong></summary>

```json
{
  "names": ["time-trigger-1", "time-trigger-2"]
}
```

</details>

| 이름 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- |
| names | Array | Y | 삭제할 트리거 이름 목록 |

### 응답

<details>
  <summary><strong>예시 코드</strong></summary>

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
