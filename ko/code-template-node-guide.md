## Compute > Cloud Functions > 코드 템플릿 가이드 > Node.js

이 문서는 NHN Cloud Functions에서 Node.js를 사용하여 함수를 개발하는 방법을 상세히 설명합니다.

## 기본 템플릿
### Hello World 예시
가장 기본적인 함수 형태입니다.

```javascript
module.exports = async (context) => {
    return {
        status: 200,
        body: "Hello, World!\n"
    };
}
```

### Context 객체
함수에 전달되는 `context` 객체는 다음과 같은 정보를 포함합니다:

```javascript
module.exports = async (context) => {
    // HTTP 요청 정보
    console.log('Method:', context.request.method);
    console.log('Headers:', context.request.headers);
    console.log('Query:', context.request.query);
    console.log('Body:', context.request.body);

    // 함수 정보
    console.log('Function Name:', context.functionName);
    console.log('Request ID:', context.requestId);

    return {
        status: 200,
        body: JSON.stringify({
            message: "Context information logged"
        })
    };
}
```

## HTTP 메서드별 처리

### GET 요청 처리
```javascript
module.exports = async (context) => {
    if (context.request.method !== 'GET') {
        return {
            status: 405,
            body: JSON.stringify({ error: 'Method Not Allowed' })
        };
    }

    // 쿼리 파라미터 가져오기
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

### POST 요청 처리
```javascript
module.exports = async (context) => {
    if (context.request.method !== 'POST') {
        return {
            status: 405,
            body: JSON.stringify({ error: 'Method Not Allowed' })
        };
    }

    try {
        // JSON 형태의 request body 파싱
        const requestBody = JSON.parse(context.request.body);

        // 필수 필드 검증
        if (!requestBody.name) {
            return {
                status: 400,
                body: JSON.stringify({
                    error: 'Missing required field: name'
                })
            };
        }

        const { name, email, message } = requestBody;

        // 처리 로직
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

    } catch (error) {
        return {
            status: 400,
            body: JSON.stringify({
                error: 'Invalid JSON format',
                details: error.message
            })
        };
    }
}
```

## 패키지 관리

### package.json 작성
의존성 관리를 위해 `package.json` 파일을 작성합니다.

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

### 외부 API 호출 예시
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

        // 외부 API 호출
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

### 데이터 처리 예시
```javascript
const _ = require('lodash');
const moment = require('moment');
const { v4: uuidv4 } = require('uuid');

module.exports = async (context) => {
    try {
        const requestBody = JSON.parse(context.request.body);
        const { data } = requestBody;

        if (!Array.isArray(data)) {
            return {
                status: 400,
                body: JSON.stringify({ error: 'Data must be an array' })
            };
        }

        // 데이터 처리
        const processedData = data.map(item => ({
            id: uuidv4(),
            ...item,
            processed_at: moment().format('YYYY-MM-DD HH:mm:ss'),
            normalized_name: _.capitalize(_.trim(item.name))
        }));

        // 데이터 정렬 및 필터링
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

## Entry Point 설정

### 단일 함수
파일명을 Entry Point로 사용합니다.

파일명: `hello.js`
Entry Point: `hello`

### 다중 함수
하나의 파일에서 여러 함수를 내보낼 수 있습니다.

```javascript
// users.js
module.exports.getUser = async (context) => {
    // 사용자 조회 로직
    return {
        status: 200,
        body: JSON.stringify({ message: "Get user" })
    };
}

module.exports.createUser = async (context) => {
    // 사용자 생성 로직
    return {
        status: 201,
        body: JSON.stringify({ message: "User created" })
    };
}

module.exports.updateUser = async (context) => {
    // 사용자 수정 로직
    return {
        status: 200,
        body: JSON.stringify({ message: "User updated" })
    };
}
```

Entry Point 설정:
- `users.getUser`
- `users.createUser`
- `users.updateUser`

## 주의사항

### CommonJS vs ES Modules
현재 NHN Cloud Functions는 CommonJS 방식만 지원합니다.

**사용 가능 (CommonJS):**
```javascript
const axios = require('axios');
module.exports = async (context) => {
    // 함수 로직
};
```

**사용 불가 (ES Modules):**
```javascript
import axios from 'axios';  // ❌ 지원하지 않음
export default async (context) => {  // ❌ 지원하지 않음
    // 함수 로직
};
```

### 메모리 및 실행 시간 고려사항
- 함수는 제한된 메모리와 실행 시간 내에서 동작해야 합니다
- 대용량 데이터 처리 시 스트림 처리를 고려하세요
- 장시간 실행되는 작업은 적절히 분할하세요


이 가이드를 참고하여 효율적이고 안정적인 Node.js Cloud Functions를 개발하시기 바랍니다.
