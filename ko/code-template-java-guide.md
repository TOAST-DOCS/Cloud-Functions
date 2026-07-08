<!-- pre-align:aligned sig=fe65e841fbc2 -->

<a id="compute-cloud-functions-code-template-guide-java"></a>
## Compute > Cloud Functions > 코드 템플릿 가이드 > Java { #compute-cloud-functions-code-template-guide-java }

이 문서는 NHN Cloud의 Cloud Functions 서비스에서 Java를 사용하여 함수를 개발하는 방법을 상세히 설명합니다.

<a id="template-information"></a>
## 템플릿 정보 { #template-information }
| 항목              | 값                  |
|-----------------|--------------------|
| 지원 버전       | 17, 21             |
| 파일명         | HelloWorld.java    |
| Entry Point | example.HelloWorld |

<a id="basic-template"></a>
## 기본 템플릿 { #basic-template }

<a id="hello-world-example"></a>
### Hello World 예시 { #hello-world-example }
가장 기본적인 함수 형태입니다.

```java
package example;

import org.springframework.http.RequestEntity;
import org.springframework.http.ResponseEntity;

public class HelloWorld {

    public ResponseEntity<?> call(RequestEntity<?> req) {
        return ResponseEntity.ok("Hello World!");
    }

}
```

<a id="context-object-requestentity"></a>
### Context 객체(RequestEntity) { #context-object-requestentity }
Java 함수에서는 Spring의 `RequestEntity`로 HTTP 요청 정보에 접근할 수 있습니다.

```java
package example;

import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpMethod;
import org.springframework.http.RequestEntity;
import org.springframework.http.ResponseEntity;
import java.net.URI;
import java.util.Map;

public class HelloWorld {

    public ResponseEntity<?> call(RequestEntity<Map<String, Object>> req) {
        // HTTP 요청 정보
        HttpMethod method = req.getMethod();
        URI url = req.getUrl();
        HttpHeaders headers = req.getHeaders();
        Map<String, Object> body = req.getBody();

        // 응답 데이터 구성
        String responseBody = String.format(
            "Method: %s\nURL: %s\nHeaders: %s\nBody: %s",
            method, url, headers, body
        );

        return ResponseEntity.ok(responseBody);
    }
}
```

<a id="download-and-use-template-file"></a>
## 템플릿 파일 다운로드 및 활용 { #download-and-use-template-file }

<a id="template-download"></a>
### 템플릿 다운로드 { #template-download }
Cloud Functions에서 제공하는 Java 템플릿을 다운로드하여 로컬 환경에서 개발할 수 있습니다.

템플릿 다운로드 링크: [java.zip](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/templates/java/java.zip)

<a id="template-file-structure"></a>
### 템플릿 파일 구조 { #template-file-structure }
다운로드한 템플릿은 Maven 프로젝트 구조를 따릅니다.

```
java.zip
├── pom.xml
└── src
    └── main
        └── java
            └── example
                └── HelloWorld.java
```

<a id="local-development-process"></a>
### 로컬 개발 과정 { #local-development-process }

<a id="local-development-process-unzip"></a>
#### 1. 압축 해제
```bash
# 압축 해제
unzip java.zip -d my-java-function

# 작업 디렉터리 이동
cd my-java-function
```

<a id="local-development-process-modify-function-codes"></a>
#### 2. 함수 코드 수정
`src/main/java/example/HelloWorld.java` 파일을 원하는 로직으로 수정합니다.

```java
// HelloWorld.java - 간단한 수정 예시
package example;

import org.springframework.http.RequestEntity;
import org.springframework.http.ResponseEntity;
import java.time.ZonedDateTime;
import java.time.format.DateTimeFormatter;
import java.util.HashMap;
import java.util.Map;
import java.util.Optional;

public class HelloWorld {

    public ResponseEntity<?> call(RequestEntity<Map<String, String>> req) {
        try {
            // 쿼리 파라미터에서 이름 가져오기
            String name = Optional.ofNullable(req.getUrl().getQuery())
                                  .map(q -> q.split("="))
                                  .filter(p -> p.length > 1 && p[0].equals("name"))
                                  .map(p -> p[1])
                                  .orElse("World");

            // POST 요청인 경우 body에서 메시지 가져오기
            String customMessage = "";
            if (req.getMethod() == org.springframework.http.HttpMethod.POST && req.hasBody()) {
                customMessage = req.getBody().getOrDefault("message", "");
            }

            // 응답 데이터 구성
            Map<String, String> response = new HashMap<>();
            response.put("greeting", "Hello, " + name + "!");
            response.put("message", customMessage);
            response.put("method", req.getMethod().toString());
            response.put("timestamp", ZonedDateTime.now().format(DateTimeFormatter.ISO_INSTANT));

            return ResponseEntity.ok(response);

        } catch (Exception e) {
            Map<String, String> errorResponse = new HashMap<>();
            errorResponse.put("error", "Internal Server Error");
            errorResponse.put("details", e.getMessage());
            return ResponseEntity.status(500).body(errorResponse);
        }
    }
}
```

<a id="local-development-process-compress-into-a-zip-file"></a>
#### 3. ZIP 파일로 압축
수정된 소스 코드를 다시 ZIP 파일로 압축합니다. `pom.xml` 파일과 `src` 디렉터리가 최상위에 포함되도록 압축해야 합니다.

```bash
# Windows (PowerShell)
Compress-Archive -Path .\pom.xml, .\src -DestinationPath my-function.zip

# Windows (7-Zip 사용 시)
7z a my-function.zip pom.xml src

# macOS/Linux
zip -r my-function.zip pom.xml src

# 모든 파일 포함(불필요한 파일 제외)
zip -r my-function.zip . -x "*.git*" "target/*" "*.log"
```

<a id="upload-from-cloud-functions-console"></a>
### Cloud Functions 콘솔에서 업로드 { #upload-from-cloud-functions-console }
- 함수 생성 또는 수정 시, **사용자 로컬 환경** 방식을 선택합니다.
- **파일 선택**을 클릭하여 생성한 `my-function.zip` 파일을 업로드합니다.

<a id="cautions-for-upload"></a>
### 업로드 시 주의사항 { #cautions-for-upload }
- 업로드 파일: 소스 코드와 `pom.xml`이 포함된 ZIP 파일을 업로드해야 합니다.
- ZIP 파일 구조: ZIP 파일의 루트에 `pom.xml`과 `src` 디렉터리가 위치해야 합니다.
- 제외할 파일: `target` 디렉터리, `.git` 디렉터리 등 불필요한 파일은 포함하지 마세요.
- 파일 크기: ZIP 파일 크기는 100MiB 이하로 제한됩니다.

<a id="process-by-http-method"></a>
## HTTP 메서드별 처리 { #process-by-http-method }

<a id="process-get-request"></a>
### GET 요청 처리 { #process-get-request }
```java
package example;

import org.springframework.http.RequestEntity;
import org.springframework.http.ResponseEntity;
import java.net.URLDecoder;
import java.nio.charset.StandardCharsets;
import java.time.Instant;
import java.util.HashMap;
import java.util.Map;
import java.util.Optional;

public class GetHandler {

    public ResponseEntity<?> call(RequestEntity<Void> req) {
        if (req.getMethod() != org.springframework.http.HttpMethod.GET) {
            return ResponseEntity.status(405).body("Method Not Allowed");
        }

        // 쿼리 파라미터 가져오기
        String name = Optional.ofNullable(req.getUrl().getQuery())
                              .map(q -> URLDecoder.decode(q, StandardCharsets.UTF_8))
                              .map(q -> q.split("=")[1])
                              .orElse("World");

        Map<String, String> response = new HashMap<>();
        response.put("message", "Hello, " + name + "!");
        response.put("timestamp", Instant.now().toString());
        response.put("method", "GET");

        return ResponseEntity.ok(response);
    }
}
```

<a id="process-post-request"></a>
### POST 요청 처리 { #process-post-request }
```java
package example;

import org.springframework.http.RequestEntity;
import org.springframework.http.ResponseEntity;
import java.time.Instant;
import java.util.HashMap;
import java.util.Map;
import java.util.UUID;

public class PostHandler {

    public ResponseEntity<?> call(RequestEntity<Map<String, String>> req) {
        if (req.getMethod() != org.springframework.http.HttpMethod.POST) {
            return ResponseEntity.status(405).body("Method Not Allowed");
        }

        if (!req.hasBody()) {
            return ResponseEntity.badRequest().body("Request body is missing");
        }

        Map<String, String> requestBody = req.getBody();
        String name = requestBody.get("name");

        if (name == null || name.isEmpty()) {
            return ResponseEntity.badRequest().body("Missing required field: name");
        }

        // 처리 로직
        Map<String, String> response = new HashMap<>();
        response.put("id", UUID.randomUUID().toString().substring(0, 8));
        response.put("name", name);
        response.put("email", requestBody.getOrDefault("email", "not provided"));
        response.put("processed_at", Instant.now().toString());

        return ResponseEntity.status(201).body(response);
    }
}
```

<a id="manage-package-pomxml"></a>
## 패키지 관리(`pom.xml`) { #manage-package-pomxml }

의존성 관리를 위해 `pom.xml` 파일을 수정합니다. `dependencies` 섹션에 필요한 라이브러리를 추가하면, 함수 업로드 시 Cloud Functions가 자동으로 의존성을 다운로드하여 빌드에 포함합니다.

```xml
<dependencies>
    <!-- 기본 Spring Boot 의존성(Jackson 포함) -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <version>3.3.2</version>
        <scope>provided</scope>
    </dependency>

    <!-- Apache Commons Lang3 라이브러리 추가 예시 -->
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-lang3</artifactId>
        <version>3.12.0</version>
    </dependency>
</dependencies>
```

<a id="example-of-using-external-libraries"></a>
### 외부 라이브러리 활용 예시 { #example-of-using-external-libraries }
```java
package example;

import org.apache.commons.lang3.StringUtils;
import org.springframework.http.RequestEntity;
import org.springframework.http.ResponseEntity;
import java.util.HashMap;
import java.util.Map;

public class StringUtilsHandler {

    public ResponseEntity<?> call(RequestEntity<Map<String, String>> req) {
        if (!req.hasBody()) {
            return ResponseEntity.badRequest().body("Request body is missing");
        }

        String originalText = req.getBody().get("text");
        if (StringUtils.isEmpty(originalText)) {
            return ResponseEntity.badRequest().body("Body must contain a 'text' field.");
        }

        // Apache Commons Lang3 StringUtils를 사용한 문자열 조작
        Map<String, Object> response = new HashMap<>();
        response.put("original", originalText);
        response.put("reversed", StringUtils.reverse(originalText));
        response.put("isAlphanumeric", StringUtils.isAlphanumeric(originalText));
        response.put("wordCount", StringUtils.split(originalText, ' ').length);

        return ResponseEntity.ok(response);
    }
}
```

<a id="use-environment-variables"></a>
## 환경 변수 사용 { #use-environment-variables }
함수에 등록한 환경 변수는 `System.getProperty`로 접근할 수 있습니다. 환경 변수는 함수 생성 및 수정의 코드 작성 단계에서 등록합니다. 자세한 내용은 콘솔 사용 가이드를 참고하세요.

!!! tip "참고"
    Java 런타임에서는 환경 변수가 OS 환경 변수가 아닌 JVM 시스템 프로퍼티로 주입됩니다. 따라서 `System.getenv`가 아닌 `System.getProperty`로 읽어야 하며, `System.getenv`를 사용하면 값이 조회되지 않습니다.

```java
package example;

import org.springframework.http.RequestEntity;
import org.springframework.http.ResponseEntity;
import java.util.HashMap;
import java.util.Map;

public class HelloWorld {

    public ResponseEntity<?> call(RequestEntity<?> req) {
        // 환경 변수 읽기(Java는 시스템 프로퍼티로 주입되므로 getProperty 사용)
        String dbHost = System.getProperty("DB_HOST");
        String apiKey = System.getProperty("API_KEY");

        if (apiKey == null || apiKey.isEmpty()) {
            Map<String, String> error = new HashMap<>();
            error.put("error", "API_KEY is not set");
            return ResponseEntity.status(500).body(error);
        }

        Map<String, String> response = new HashMap<>();
        response.put("dbHost", dbHost);
        return ResponseEntity.ok(response);
    }
}
```

!!! tip "참고"
    보안상 다음 키·접두사는 환경 변수로 등록할 수 없습니다.

    공통(모든 런타임)

    - 셸/subprocess: `PATH`, `IFS`, `HOME`, `BASH_ENV`, `ENV`, `SHELLOPTS`
    - 로더/라이브러리: `LD_PRELOAD`, `LD_LIBRARY_PATH`, `LD_AUDIT`
    - glibc 동적 로딩: `GCONV_PATH`, `LOCPATH`, `HOSTALIASES`
    - 프록시: `HTTP_PROXY`, `HTTPS_PROXY`, `ALL_PROXY`(소문자 포함)
    - TLS 신뢰 저장소: `SSL_CERT_FILE`, `SSL_CERT_DIR`, `REQUESTS_CA_BUNDLE`, `CURL_CA_BUNDLE`
    - 플랫폼 내부: `RUNTIME_PORT`, `USERFUNCVOL`, `WSGI_FRAMEWORK`, `SENTRY_DSN`, `SENTRY_RELEASE`, `TIMEOUT`, `BODY_PARSER_LIMIT`
    - 접두사: `LD_`, `DYLD_`, `KUBERNETES_`, `FISSION_`

    Java

    - `JAVA_TOOL_OPTIONS`, `_JAVA_OPTIONS`, `JDK_JAVA_OPTIONS`, `CLASSPATH`

<a id="entry-point-configuration"></a>
## Entry Point 설정 { #entry-point-configuration }

<a id="single-class"></a>
### 단일 클래스 { #single-class }
`패키지명.클래스명`을 Entry Point로 사용합니다.

- 패키지명: `example`
- 클래스명: `HelloWorld`
- Entry Point: `example.HelloWorld`

!!! danger "중요"
    함수 역할을 하는 메서드는 `public ResponseEntity<?> call(RequestEntity<?> req)` 시그니처를 가져야 합니다.

<a id="caution"></a>
### 주의사항 { #caution }
- 프로젝트 구조: `src/main/java` 디렉터리 구조를 유지해야 합니다.
- 메모리 및 실행 시간: 함수는 제한된 리소스 내에서 동작해야 하므로, 무거운 작업은 피하고 코드를 최적화하세요.
