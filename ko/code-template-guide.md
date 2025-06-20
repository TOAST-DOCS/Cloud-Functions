## Compute > Cloud Functions > 코드 Template 가이드
이 문서는 NHN Cloud Functions 함수의 코드 작성시 제공되는 Template 코드에 대해 설명합니다.

## Template 코드 목록
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
- 해당 파일을 작성하여 의존성을 관리합니다.

### Entry Point
- `hello`
    - NodeJs의 Entry Point는 `파일명`입니다.

#### 두개의 함수를 사용하는 경우 Entry Point 지정
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
- 위 예시의 코드의 Entry Point
    - `파일명.entry1`
    - `파일명.entry2`

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
- 해당 파일을 작성하여 의존성을 관리합니다.

### Entry Point
- `user.main`
    - Python의 Entry Point는 `파일명.함수명`입니다.

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
- 해당 파일을 작성하여 의존성을 관리합니다.

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
- 해당 파일을 작성하여 의존성을 관리합니다.

### Entry Point
- `example.HelloWorld`
    - Java의 Entry Point는 `package명.함수명`입니다.

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
- 해당 파일을 작성하여 의존성을 관리합니다.

### Entry Point
- `handler`
    - ruby의 Entry Point는 `함수명`입니다.

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
- 해당 파일을 작성하여 의존성을 관리합니다.

### Entry Point
- `func`
    - .NET의 Entry Point는 `파일명`입니다.