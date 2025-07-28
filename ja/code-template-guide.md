## Compute > Cloud Functions > コードテンプレートガイド
この文書では、NHN Cloud Functions関数のコード作成時に提供されるテンプレートコードについて説明します。
- 言語ごとに`Hello World`関数を作成する方法を学びます。

## テンプレートコードリスト
| 言語   | バージョン     | ファイル名          | Entry Point         |
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
### POST例
``` js
module.exports = async (context) => {
    try {
        // 形式のrequest bodyを取得する
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
- このファイルを作成して依存関係を管理します。

### Entry Point
- `hello`
    - NodeJSのEntry Pointは`ファイル名`です。

#### 2つの関数を使用する場合のEntry Point指定
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
- 上記の例コードのEntry Point
    - `ファイル名.entry1`
    - `ファイル名.entry2`

> **[参考]**<br>
> 現在ES Modulesの使用はサポートされていません。CommonJS方式のみ使用可能です。
> <br>(ES Modulesは今後サポート予定)

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

### POST例
``` python
import json
from flask import request

def main():
    try:
        # 形式のrequest bodyを取得する
        request_body = request.get_json()

        name = request_body.get('name', 'World')

        return f"Hello, {name}!"
    except Exception as e:
        return f"Error: {str(e)}"
```

### Package
`requirements.txt`
- このファイルを作成して依存関係を管理します。

例
```txt
pyyaml
```


### Entry Point
- `user.main`
    - PythonのEntry Pointは`ファイル名。関数名`です。

> **[参考]**<br>
> 現在のところ、以下の特徴を持つ複雑なPackageはサポートしません。 <br> 例：numpy, pandas
> 1. C/C++/Rust拡張モジュール必須
> 2. システムライブラリ依存関係
> 3. 複雑な初期化プロセス
> 4. ソースディレクトリ検査実行
> 5. 複数のサブパッケージ構造
> 6. ランタイム環境の検証

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

### POST例
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
	// request body読み取り
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
- このファイルを作成して依存関係を管理します。

作成方法
```bash
go mod init example.com/myfunction
go get github.com/brianvoe/gofakeit/v6
```

例
```mod
module example.com/ncf

go 1.22

require github.com/brianvoe/gofakeit/v6 v6.28.0
```

> **[参考]**<br>
> Go 1.22または1.23バージョンを使用します。

### Entry Point
- `Handler`
    - GoのEntry Pointは`関数名`です。

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

### POST例
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
- このファイルを作成して依存関係を管理します。

### Entry Point
- `example.HelloWorld`
    - JavaのEntry Pointは`パッケージ名。クラス名`です。
    - メソッドは`public ResponseEntity<?> call(RequestEntity req)`と指定する必要があります。

> **[参考]**<br>
> Javaの場合、Template構造`src/main/java`のまま使用してユーザー関数を作成することを推奨します。
> <br>同様に、Template `pom.xml`を使用して依存関係を追加してください。

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

### POST例
```ruby
# frozen_string_literal: true

require 'json'

def handler(context)
  begin
    # 形式のrequest bodyを取得する
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
- このファイルを作成して依存関係を管理します。

例
```Gemfile
# frozen_string_literal: true

source "https://rubygems.org"

git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

gem "nokogiri", ">= 1.12.5"
```

> **[参考]**<br>
> Gemfile.lockファイルは`bundle install`を実行して作成されます。
> <br> Bundlerバージョンは1.17.3以上(1.x系列)

### Entry Point
- `handler`
    - RubyのEntry Pointは`関数名`です。

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

            // CsvHelper例: CSV読み取り
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

### POST例
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
            // 形式のrequest bodyを取得する
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
- このファイルを作成して依存関係を管理します。

例
```txt
CsvHelper

Newtonsoft.Json:9.0.1
```

> **[参考]**<br>
> `nuget.txt` Packageを追加する際は、実際に使用するPackageのみを追加する必要があります。<br>
> 例えばPOST例では`Newtonsoft.Json`のみを追加します。<br>
> 依存関係を追加する際に型の衝突が発生する可能性があるため、可能であれば .NETの標準ライブラリを使用することを推奨します。

### Entry Point
- `func`
    - .NETのEntry Pointは`ファイル名`です。
