## Compute > Cloud Functions > 코드 템플릿 가이드
이 문서는 NHN Cloud Functions 함수의 코드 작성 시 제공되는 템플릿 코드에 대해 설명합니다.
- 각 언어별로 `Hello World` 함수를 작성하는 방법을 알아봅니다.

## 템플릿 코드 목록
| 언어     | 버전       | 파일명            | Entry Point         |
|----------|------------|-------------------|-------------------|
| NodeJS   | 20.16.0, 22.5.0 | hello.js        | hello              |
| Python   | 3.11       | user.py         | user.main          |
| Go       | 1.22, 1.23 | functions.go    | Handler            |
| Java     | 17, 21     | HelloWorld.java | example.HelloWorld |
| Ruby     | 2.6.1      | parse.rb        | handler            |
| .NET     | 7          | func.cs         | func               |

## NodeJS
`hello.js`
``` js
module.exports = async (context) => {
    return {
        status: 200,
        body: "hello, world!\n"
    };
}
```
### POST 예시
``` js
module.exports = async (context) => {
    try {
        // JSON 형태의 request body 가져오기
        const requestBody = JSON.parse(context.request.body);

        const name = requestBody.name || 'World';

        return {
            status: 200,
            body: `Hello, ${name}!`
        };
    } catch (error) {
        return {
            status: 400,
            body: `Error: ${error.message}`
        };
    }
}
```

### Package
`package.json`
- 이 파일을 작성하여 의존성을 관리합니다.

### Entry Point
- `hello`
    - NodeJS의 Entry Point는 `파일명`입니다.

#### 두 개의 함수를 사용하는 경우 Entry Point 지정
``` js
module.exports.entry1 = async (context) => {
    return {
        status: 200,
        body: "Hello, entry 1!\n"
    };
}

module.exports.entry2 = async (context) => {
    return {
        status: 200,
        body: "Hello, entry 2!\n"
    };
}
```
- 위 예시 코드의 Entry Point
    - `파일명.entry1`
    - `파일명.entry2`

> **[참고]**<br>
> 현재 ES Modules 사용은 지원하지 않습니다. CommonJS 방식만 사용 가능합니다.
> <br>(ES Modules 추후 지원 예정)

## Python
`user.py`
``` python
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

### POST 예시
``` python
import json
from flask import request

def main():
    try:
        # JSON 형태의 request body 가져오기
        request_body = request.get_json()

        name = request_body.get('name', 'World')

        return f"Hello, {name}!"
    except Exception as e:
        return f"Error: {str(e)}"
```

### Package
`requirements.txt`
- 이 파일을 작성하여 의존성을 관리합니다.

예시
```txt
pyyaml
```


### Entry Point
- `user.main`
    - Python의 Entry Point는 `파일명.함수명`입니다.

> **[참고]**<br>
> 현재 아래 특징이 있는 복잡한 Package는 지원하지 않습니다. <br> 예: numpy, pandas
> 1. C/C++/Rust 확장 모듈 필수
> 2. 시스템 라이브러리 의존성
> 3. 복잡한 초기화 과정
> 4. 소스 디렉토리 검사 수행
> 5. 다중 서브패키지 구조
> 6. 런타임 환경 검증

## Go
`functions.go`
```go
package main

import (
	"fmt"
	"log"
	"net/http"

	"github.com/brianvoe/gofakeit/v6"
)

func Handler(w http.ResponseWriter, r *http.Request) {
	name := gofakeit.Name()
	email := gofakeit.Email()
	city := gofakeit.City()

	msg := fmt.Sprintf("Fake Name: %s\nEmail: %s\nCity: %s\n", name, email, city)
	_, err := w.Write([]byte(msg))
	if err != nil {
		log.Printf("Error writing response: %v", err)
	}
}
```

### POST 예시
```go
package main

import (
	"encoding/json"
	"fmt"
	"io"
	"net/http"
)

type RequestBody struct {
	Name string `json:"name"`
}

func Handler(w http.ResponseWriter, r *http.Request) {
	// request body 읽기
	body, err := io.ReadAll(r.Body)
	if err != nil {
		http.Error(w, fmt.Sprintf("Error reading body: %v", err), http.StatusBadRequest)
		return
	}
	defer r.Body.Close()

	var requestBody RequestBody
	err = json.Unmarshal(body, &requestBody)
	if err != nil {
		http.Error(w, fmt.Sprintf("Error parsing JSON: %v", err), http.StatusBadRequest)
		return
	}

	name := requestBody.Name
	if name == "" {
		name = "World"
	}

	response := fmt.Sprintf("Hello, %s!", name)
	w.WriteHeader(http.StatusOK)
	w.Write([]byte(response))
}
```

### Package
`go.mod`
- 이 파일을 작성하여 의존성을 관리합니다.

생성 방법
```bash
go mod init example.com/myfunction
go get github.com/brianvoe/gofakeit/v6
```

예시
```mod
module example.com/ncf

go 1.22

require github.com/brianvoe/gofakeit/v6 v6.28.0
```

> **[참고]**<br>
> Go 1.22 또는 1.23 버전을 사용합니다.

### Entry Point
- `Handler`
    - Go의 Entry Point는 `함수명`입니다.

## Java
`HelloWorld.java`
```java
package example;

import org.springframework.http.RequestEntity;
import org.springframework.http.ResponseEntity;

public class HelloWorld{

	public ResponseEntity<?> call(RequestEntity req) {
		return ResponseEntity.ok("Hello World!");
	}

}
```

### POST 예시
```java
package example;

import org.springframework.http.RequestEntity;
import org.springframework.http.ResponseEntity;
import java.util.Map;

public class HelloWorld{

	public ResponseEntity<?> call(RequestEntity<Map<String, Object>> req) {
		try {
			Map<String, Object> requestBody = req.getBody();

			String name = requestBody != null && requestBody.containsKey("name")
				? requestBody.get("name").toString()
				: "World";

			return ResponseEntity.ok("Hello, " + name + "!");
		} catch (Exception e) {
			return ResponseEntity.badRequest().body("Error: " + e.getMessage());
		}
	}

}
```

### Package
`pom.xml`
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

	<modelVersion>4.0.0</modelVersion>
	<groupId>example</groupId>
	<artifactId>hello-world</artifactId>
	<version>1.0-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>hello-world</name>
	<url>http://maven.apache.org</url>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
			<version>3.3.2</version>
			<scope>provided</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<artifactId>maven-assembly-plugin</artifactId>
				<configuration>
					<descriptorRefs>
						<descriptorRef>jar-with-dependencies</descriptorRef>
					</descriptorRefs>
				</configuration>
				<executions>
					<execution>
						<id>make-assembly</id> <!-- this is used for inheritance merges -->
						<phase>package</phase> <!-- bind to the packaging phase -->
						<goals>
							<goal>single</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-surefire-plugin</artifactId>
				<version>2.22.1</version>
				<configuration>
					<useSystemClassLoader>false</useSystemClassLoader>
				</configuration>
			</plugin>
		</plugins>
	</build>

</project>
```
- 이 파일을 작성하여 의존성을 관리합니다.

### Entry Point
- `example.HelloWorld`
    - Java의 Entry Point는 `패키지명.클래스명`입니다.
    - 메서드는 `public ResponseEntity<?> call(RequestEntity req)`로 지정해야 합니다.

> **[참고]**<br>
> Java의 경우 Template 구조 `src/main/java` 그대로 사용하여 사용자 함수를 작성하는 것을 권장합니다.
> <br>마찬가지로 Template `pom.xml`을 사용하여 의존성을 추가하세요.

## Ruby
`parse.rb`
```ruby
# frozen_string_literal: true

require 'nokogiri'

def handler(context)
  context.logger.info("Received request")

  doc = Nokogiri::XML(context.request.body.read)
  ele = doc.at_xpath('//message')
  msg = ele.nil? ? 'No Message' : ele.content

  Rack::Response.new([msg, "\n"]).finish
end
```

### POST 예시
```ruby
# frozen_string_literal: true

require 'json'

def handler(context)
  begin
    # JSON 형태의 request body 가져오기
    request_body = JSON.parse(context.request.body.read)

    name = request_body['name'] || 'World'

    response = "Hello, #{name}!"
    Rack::Response.new([response]).finish
  rescue JSON::ParserError => e
    Rack::Response.new(["Error: Invalid JSON - #{e.message}"], 400).finish
  rescue => e
    Rack::Response.new(["Error: #{e.message}"], 500).finish
  end
end
```

### Package
`Gemfile`
- 이 파일을 작성하여 의존성을 관리합니다.

예시
```Gemfile
# frozen_string_literal: true

source "https://rubygems.org"

git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

gem "nokogiri", ">= 1.12.5"
```

> **[참고]**<br>
> Gemfile.lock 파일은 `bundle install`을 실행하여 생성됩니다.
> <br> Bundler 버전은 1.17.3 이상(1.x 계열)

### Entry Point
- `handler`
    - Ruby의 Entry Point는 `함수명`입니다.

## .NET
`func.cs`
```cs
using System;
using System.IO;
using CsvHelper;
using System.Globalization;
using Nhn.DotNetCore.Api;

public class NhnFunction
{
    public string Execute(NhnContext context)
    {
        string respo = "initial value";
        try
        {
            context.Logger.WriteInfo("Starting..... ");

            // CsvHelper 예제: CSV 읽기
            var csvData = "Name,Age\nJohn,30\nJane,25";
            using (var reader = new StringReader(csvData))
            using (var csv = new CsvReader(reader, CultureInfo.InvariantCulture))
            {
                var records = csv.GetRecords<dynamic>();
                respo = "CSV Data Processed!";
            }
        }
        catch (Exception ex)
        {
            context.Logger.WriteError(ex.Message);
            respo = ex.Message;
        }
        context.Logger.WriteInfo("Done!");
        return respo;
    }
}
```

### POST 예시
```cs
using System;
using System.IO;
using Newtonsoft.Json.Linq;
using Nhn.DotNetCore.Api;

public class NhnFunction
{
    public string Execute(NhnContext context)
    {
        try
        {
            // JSON 형태의 request body 가져오기
            string requestBodyString = new StreamReader(context.Request.Body).ReadToEnd();

            JObject requestBody = JObject.Parse(requestBodyString);

            string name = requestBody["name"]?.ToString() ?? "World";

            return $"Hello, {name}!";
        }
        catch (Newtonsoft.Json.JsonException ex)
        {
            return $"Error: Invalid JSON - {ex.Message}";
        }
        catch (Exception ex)
        {
            return $"Error: {ex.Message}";
        }
    }
}
```

### Package
`nuget.txt`
- 이 파일을 작성하여 의존성을 관리합니다.

예시
```txt
CsvHelper

Newtonsoft.Json:9.0.1
```

> **[참고]**<br>
> `nuget.txt` Package 추가 시에는 실제 사용하는 Package만 추가해야 합니다.<br>
> 예를 들어 POST 예시에서는 `Newtonsoft.Json`만 추가합니다.<br>
> 의존성 추가 시 타입 충돌이 발생할 수 있으므로 가능한 .NET 기본 라이브러리를 사용하는 것을 권장합니다.

### Entry Point
- `func`
    - .NET의 Entry Point는 `파일명`입니다.