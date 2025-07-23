## Compute > Cloud Functions > コードテンプレートガイド
この文書では、NHN Cloud Functions関数のコード作成時に提供されるテンプレートコードについて説明します。

## テンプレートコードリスト
| 言語   | ファイル名          | Entry Point         |
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

> [参考]<br>
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

### Package
`requirements.txt`
- このファイルを作成して依存関係を管理します。

### Entry Point
- `user.main`
    - PythonのEntry Pointは`ファイル名。関数名`です。

> [参考]<br>
> 現在のところ、以下の特徴を持つ複雑なPackageはサポートしません。
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

### Package
`go.mod`
- このファイルを作成して依存関係を管理します。

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
- このファイルを作成して依存関係を管理します。

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

### Package
`nuget.txt`
- このファイルを作成して依存関係を管理します。

### Entry Point
- `func`
    - .NETのEntry Pointは`ファイル名`です。
