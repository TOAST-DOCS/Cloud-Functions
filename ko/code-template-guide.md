## Compute > Cloud Functions > 코드 템플릿 가이드
이 문서는 NHN Cloud Functions 함수의 코드 작성 시 제공되는 템플릿 코드에 대해 설명합니다.
- 각 언어마다 `Hello World` 함수를 작성하는 방법을 알아봅니다.

## 템플릿 코드 목록
| 언어     | 파일명            | Entry Point         |
|----------|-----------------|--------------------|
| NodeJS   | hello.js        | hello              |
| Python   | user.py         | user.main          |
| Go       | functions.go    | Handler            |
| Java     | HelloWorld.java | example.HelloWorld |
| Ruby     | parse.rb        | handler            |
| .NET     | func.cs         | func               |

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

> [참고]<br>
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

> [참고]<br>
> 현재 아래 특징이 있는 복잡한 Package는 지원하지 않습니다. <br> 예시) numpy, pandas
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

### Package
`go.mod`
- 이 파일을 작성하여 의존성을 관리합니다.

예시
```mod
module example.com/ncf

require github.com/brianvoe/gofakeit/v6 v6.28.0
```

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
> <br>마찬가지로 Template `pom.xml`을 사용하여 의존성을 추가해주세요.

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

>**[참고]**<br>
> Gemfile.lock 파일은 `bundle install`을 실행하여 생성됩니다.

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

### Package
`nuget.txt`
- 이 파일을 작성하여 의존성을 관리합니다.

예시
```txt
CsvHelper
```

### Entry Point
- `func`
    - .NET의 Entry Point는 `파일명`입니다.