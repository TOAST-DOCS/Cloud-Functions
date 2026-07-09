<!-- pre-align:aligned sig=fe65e841fbc2 -->

<a id="compute-cloud-functions-code-template-guide-java"></a>
## Compute > Cloud Functions > コードテンプレートガイド > Java { #compute-cloud-functions-code-template-guide-java }

このドキュメントでは、NHN CloudのCloud FunctionsサービスでJavaを使用して関数を開発する方法を詳しく説明します。

<a id="template-information"></a>
## テンプレート情報 { #template-information }
| 項目       | 値                |
|-----------------|--------------------|
| **サポートバージョン** | 17, 21             |
| **ファイル名**    | HelloWorld.java    |
| **Entry Point** | example.HelloWorld |

<a id="basic-template"></a>
## 基本テンプレート { #basic-template }

<a id="hello-world-example"></a>
### Hello Worldの例 { #hello-world-example }
最も基本的な関数の形式です。

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
### Contextオブジェクト(RequestEntity) { #context-object-requestentity }
Javaの関数では、Springの`RequestEntity`を通じてHTTPリクエスト情報にアクセスできます。

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
        // HTTPリクエスト情報
        HttpMethod method = req.getMethod();
        URI url = req.getUrl();
        HttpHeaders headers = req.getHeaders();
        Map<String, Object> body = req.getBody();

        // レスポンスデータの構成
        String responseBody = String.format(
            "Method: %s\nURL: %s\nHeaders: %s\nBody: %s",
            method, url, headers, body
        );

        return ResponseEntity.ok(responseBody);
    }
}
```

<a id="download-and-use-template-file"></a>
## テンプレートファイルのダウンロードと活用 { #download-and-use-template-file }

<a id="template-download"></a>
### テンプレートのダウンロード { #template-download }
Cloud Functionsが提供するJavaテンプレートをダウンロードし、ローカル環境で開発できます。

**テンプレートのダウンロードリンク**: [java.zip](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/templates/java/java.zip)

<a id="template-file-structure"></a>
### テンプレートのファイル構造 { #template-file-structure }
ダウンロードしたテンプレートは、Mavenのプロジェクト構造に従っています。

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
### ローカルでの開発プロセス { #local-development-process }

<a id="local-development-process-unzip"></a>
#### 1. 解凍
```bash
# 解凍
unzip java.zip -d my-java-function

# 作業ディレクトリへ移動
cd my-java-function
```

<a id="local-development-process-modify-function-codes"></a>
#### 2. 関数コードの修正
`src/main/java/example/HelloWorld.java`ファイルを、目的のロジックに合わせて修正します。

```java
// HelloWorld.java - 簡単な修正例
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
            // クエリパラメータから名前を取得
            String name = Optional.ofNullable(req.getUrl().getQuery())
                                  .map(q -> q.split("="))
                                  .filter(p -> p.length > 1 && p[0].equals("name"))
                                  .map(p -> p[1])
                                  .orElse("World");

            // POSTリクエストの場合、bodyからメッセージを取得
            String customMessage = "";
            if (req.getMethod() == org.springframework.http.HttpMethod.POST && req.hasBody()) {
                customMessage = req.getBody().getOrDefault("message", "");
            }

            // レスポンスデータの構成
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
#### 3. ZIPファイルへ圧縮
修正したソースコードを、再度ZIPファイルへ圧縮します。`pom.xml`ファイルと`src`ディレクトリがルートディレクトリに含まれるように圧縮する必要があります。

```bash
# Windows (PowerShell)
Compress-Archive -Path .\pom.xml, .\src -DestinationPath my-function.zip

# Windows (7-Zipを使用する場合)
7z a my-function.zip pom.xml src

# macOS/Linux
zip -r my-function.zip pom.xml src

# 全てのファイルを含める(不要なファイルは除く)
zip -r my-function.zip . -x "*.git*" "target/*" "*.log"
```

<a id="upload-from-cloud-functions-console"></a>
### Cloud Functionsコンソールでのアップロード { #upload-from-cloud-functions-console }
- 関数を作成または修正する際に、**ユーザーローカル環境**方式を選択します。
- **ファイルを選択**をクリックし、作成した`my-function.zip`ファイルをアップロードします。

<a id="cautions-for-upload"></a>
### アップロード時の注意事項 { #cautions-for-upload }
- **アップロードファイル**:ソースコードと`pom.xml`が含まれた**ZIPファイル**をアップロードする必要があります。
- **ZIPファイルの構造**: ZIPファイルのルートに`pom.xml`と`src`ディレクトリを配置する必要があります。
- **除外するファイル**: `target`ディレクトリや`.git`ディレクトリなど、不要なファイルは含めないでください。
- **ファイルサイズ**: ZIPファイルのサイズは100MiB以下に制限されます。

<a id="process-by-http-method"></a>
## HTTPメソッド別の処理 { #process-by-http-method }

<a id="process-get-request"></a>
### GETリクエストの処理 { #process-get-request }
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

        // クエリパラメータの取得
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
### POSTリクエストの処理 { #process-post-request }
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

        // 処理ロジック
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
## パッケージ管理(`pom.xml`) { #manage-package-pomxml }

依存関係の管理には、`pom.xml`ファイルを修正します。`dependencies`セクションに必要なライブラリを追加すると、関数のアップロード時にCloud Functionsが自動で依存関係をダウンロードし、ビルドに含めます。

```xml
<dependencies>
    <!-- 基本Spring Boot依存関係(Jackson含む) -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <version>3.3.2</version>
        <scope>provided</scope>
    </dependency>

    <!-- Apache Commons Lang3ライブラリの追加例 -->
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-lang3</artifactId>
        <version>3.12.0</version>
    </dependency>
</dependencies>
```

<a id="example-of-using-external-libraries"></a>
### 外部ライブラリの活用例 { #example-of-using-external-libraries }
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

        // Apache Commons Lang3のStringUtilsを使用した文字列操作
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
## 環境変数の使用 { #use-environment-variables }
関数に登録した環境変数は、`System.getProperty`でアクセスできます。環境変数は、関数の作成及び修正のコード作成段階で登録します。詳細については、コンソール使用ガイドをご参照ください。

!!! tip "ポイント"
    Javaランタイムでは、環境変数がOSの環境変数ではなくJVMのシステムプロパティとして設定されます。そのため、`System.getenv`ではなく`System.getProperty`で読み込む必要があり、`System.getenv`を使用すると値を取得できません。

```java
package example;

import org.springframework.http.RequestEntity;
import org.springframework.http.ResponseEntity;
import java.util.HashMap;
import java.util.Map;

public class HelloWorld {

    public ResponseEntity<?> call(RequestEntity<?> req) {
        // 環境変数の読み込み(Javaはシステムプロパティとして設定されるためgetPropertyを使用)
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

!!! tip "ポイント"
    セキュリティ上、以下のキー・プレフィックスは環境変数として登録できません。

    共通(全てのランタイム)

    - シェル/subprocess: `PATH`、`IFS`、`HOME`、`BASH_ENV`、`ENV`、`SHELLOPTS`
    - ローダー/ライブラリ: `LD_PRELOAD`、`LD_LIBRARY_PATH`、`LD_AUDIT`
    - glibc動的ローディング: `GCONV_PATH`、`LOCPATH`、`HOSTALIASES`
    - プロキシ: `HTTP_PROXY`、`HTTPS_PROXY`、`ALL_PROXY`(小文字を含む)
    - TLSトラストストア: `SSL_CERT_FILE`、`SSL_CERT_DIR`、`REQUESTS_CA_BUNDLE`、`CURL_CA_BUNDLE`
    - プラットフォーム内部: `RUNTIME_PORT`、`USERFUNCVOL`、`WSGI_FRAMEWORK`、`SENTRY_DSN`、`SENTRY_RELEASE`、`TIMEOUT`、`BODY_PARSER_LIMIT`
    - プレフィックス: `LD_`、`DYLD_`、`KUBERNETES_`、`FISSION_`、`CORECLR_`、`COMPLUS_`、`DOTNET_`、`ASPNETCORE_`、`PYTHON`、`NODE_`、`RUBY`

    Java

    - `JAVA_TOOL_OPTIONS`, `_JAVA_OPTIONS`, `JDK_JAVA_OPTIONS`, `CLASSPATH`

<a id="entry-point-configuration"></a>
## エントリーポイントの設定 { #entry-point-configuration }

<a id="single-class"></a>
### 単一クラス { #single-class }
`パッケージ名。クラス名`をエントリーポイントとして使用します。

- パッケージ名: `example`
- クラス名: `HelloWorld`
- Entry Point: `example.HelloWorld`
- **重要**:関数として機能するメソッドは、`public ResponseEntity<?> call(RequestEntity<?> req)`というシグネチャを持つ必要があります。

<a id="caution"></a>
### 注意事項 { #caution }
- **プロジェクト構造**: `src/main/java`のディレクトリ構造を維持する必要があります。
- **メモリと実行時間**:関数は限られたリソース内で動作する必要があるため、重い処理は避け、コードを最適化してください。
