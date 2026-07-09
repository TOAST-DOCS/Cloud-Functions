<!-- pre-align:aligned sig=fe65e841fbc2 -->

<a id="compute-cloud-functions-code-template-guide-java"></a>
## Compute > Cloud Functions > Code Template Guide > Java { #compute-cloud-functions-code-template-guide-java }

This document details how to develop functions by using Java from NHN Cloud's Cloud Functions service.

<a id="template-information"></a>
## Template information { #template-information }
| Item               | Value                  |
|-----------------|---------------------|
| **Supported version**       | 17, 21             |
| **File name**          | HelloWorld.java    |
| **Entry Point** | example.HelloWorld |

<a id="basic-template"></a>
## Basic template { #basic-template }

<a id="hello-world-example"></a>
### Hello World example { #hello-world-example }
A basic form of function.

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
### Context object (RequestEntity) { #context-object-requestentity }
In a Java function, you can access HTTP request information through `RequestEntity`.

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
        // HTTP request information
        HttpMethod method = req.getMethod();
        URI url = req.getUrl();
        HttpHeaders headers = req.getHeaders();
        Map<String, Object> body = req.getBody();

        // Response data configuration
        String responseBody = String.format(
            "Method: %s\nURL: %s\nHeaders: %s\nBody: %s",
            method, url, headers, body
        );

        return ResponseEntity.ok(responseBody);
    }
}
```

<a id="download-and-use-template-file"></a>
## Download and use template file { #download-and-use-template-file }

<a id="template-download"></a>
### Template download { #template-download }
You can download the Java template provided by Cloud Functions to develop a local environment.

**Template download link**: [java.zip](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/templates/java/java.zip)

<a id="template-file-structure"></a>
### Template file structure { #template-file-structure }
The structure of the downloaded template file is as follows:

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
### Local development process { #local-development-process }

<a id="local-development-process-unzip"></a>
#### 1. Unzip
```bash
# Unzip
unzip java.zip -d my-java-function

# Move to task directory
cd my-java-function
```

<a id="local-development-process-modify-function-codes"></a>
#### 2. Modify function codes
Modify `src/main/java/example/HelloWorld.java` file with the logic you want.

```java
// HelloWorld.java - Simple modification example
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
            // Import name from query parameter
            String name = Optional.ofNullable(req.getUrl().getQuery())
                                  .map(q -> q.split("="))
                                  .filter(p -> p.length > 1 && p[0].equals("name"))
                                  .map(p -> p[1])
                                  .orElse("World");

            // Import message from body if POST request
            String customMessage = "";
            if (req.getMethod() == org.springframework.http.HttpMethod.POST && req.hasBody()) {
                customMessage = req.getBody().getOrDefault("message", "");
            }

            // Response data configuration
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
#### 3. Compress into a ZIP file
Compress the modified source code into ZIP file. You must compress it so that `pom.xml` and `src` are included at the top level.

```bash
# Windows (PowerShell)
Compress-Archive -Path .\pom.xml, .\src -DestinationPath my-function.zip

# Windows (when using 7-Zip)
7z a my-function.zip pom.xml src

# macOS/Linux
zip -r my-function.zip pom.xml src

# Include all files (exclude unnecessary files)
zip -r my-function.zip . -x "*.git*" "target/*" "*.log"
```

<a id="upload-from-cloud-functions-console"></a>
### Upload from Cloud Functions console { #upload-from-cloud-functions-console }
- When creating or modifying a function, select the **User Local Environment** method.
- Click **Select File** to upload the `my-function.zip` file you created.

<a id="cautions-for-upload"></a>
### Cautions for upload { #cautions-for-upload }
- **Upload File**: Upload **ZIP file** which includes source code and `pom.xml`.
- **ZIP File Structure**: The `pom.xml` and `src` directory must be located directly in the root of the ZIP file.
- **File to Exclude**: Do not include unnecessary files such as the `target` directory, `.git` directory, etc.
- **File Size**: ZIP file size is limited to 100 MiB.

<a id="process-by-http-method"></a>
## Process by HTTP method { #process-by-http-method }

<a id="process-get-request"></a>
### Process GET request { #process-get-request }
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

        // Import query parameter
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
### Process POST request { #process-post-request }
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

        // Processing logic
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
## Manage package (`pom.xml`) { #manage-package-pomxml }

Use the `pom.xml` file to manage dependencies. When you add required libraries to the `dependencies` section, Cloud Functions automatically downloads the dependencies and includes them in the build when you upload your function.

```xml
<dependencies>
    <!-- Basic Spring Boot dependencies (include Jackson) -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <version>3.3.2</version>
        <scope>provided</scope>
    </dependency>

    <!-- Example of adding Apache Commons Lang3 library -->
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-lang3</artifactId>
        <version>3.12.0</version>
    </dependency>
</dependencies>
```

<a id="example-of-using-external-libraries"></a>
### Example of using external libraries { #example-of-using-external-libraries }
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

        // String manipulation using Apache Commons Lang3 StringUtils
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
## Use Environment Variables { #use-environment-variables }
Environment variables registered for a function can be accessed via `System.getProperty`. Environment variables are registered during the code writing step of function creation and modification. For more information, see the console user guide.

!!! tip "Note"
    In the Java runtime, environment variables are injected as JVM system properties rather than OS environment variables. Therefore, they must be read using `System.getProperty` instead of `System.getenv`. Using `System.getenv` will not return the values.

```java
package example;

import org.springframework.http.RequestEntity;
import org.springframework.http.ResponseEntity;
import java.util.HashMap;
import java.util.Map;

public class HelloWorld {

    public ResponseEntity<?> call(RequestEntity<?> req) {
        // Read environment variables (Java injects environment variables as system properties, so use `getProperty` to access them.)
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

    Java

    - `JAVA_TOOL_OPTIONS`, `_JAVA_OPTIONS`, `JDK_JAVA_OPTIONS`, `CLASSPATH`

<a id="entry-point-configuration"></a>
## Entry Point configuration { #entry-point-configuration }

<a id="single-class"></a>
### Single class { #single-class }
Use `packagename.classname` as an Entry Point.

- Package name: `example`
- Class name: `HelloWorld`
- Entry Point: `example.HelloWorld`
- **Important**: Methods that act as functions must have the signature `public ResponseEntity<?> call(RequestEntity<?> req)`.

<a id="caution"></a>
### Caution { #caution }
- **Project structure**: You must maintain the `src/main/java` directory structure.
- **Memory and execution time**: Functions must operate within limited resources, so avoid heavy workloads and optimize the code.
