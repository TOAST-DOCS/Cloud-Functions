## Compute > Cloud Functions > Code Template Guide
This document describes the template code provided when writing code for functions of NHN Cloud Functions.
- Learn how to write the `Hello World` function for each language.

## Template code list
| Language     | Version       | Filename            | Entry Point         |
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
### POST example
``` js
module.exports = async (context) => {
    try {
        // get the request body in JSON format
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
- Create this file to manage dependencies.

### Entry Point
- `hello`
    - The entry point in NodeJS is the `filename`.

#### Specify entry points when using two functions
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
- The Entry Point In the example code above
    - `filename.entry1`
    - `filename.entry2`

> **[Note]**<br>
> Currently, we do not support using ES Modules; only CommonJS method is available.
> <br>(ES Modules, to be provided later)

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

### POST example
``` python
import json
from flask import request

def main():
    try:
        # get the request body in JSON format
        request_body = request.get_json()

        name = request_body.get('name', 'World')

        return f"Hello, {name}!"
    except Exception as e:
        return f"Error: {str(e)}"
```

### Package
`requirements.txt`
- Create this file to manage dependencies.

Example
```txt
pyyaml
```


### Entry Point
- `user.main`
    - The entry point in Python is `filename.functionname`.

> **[Note]**<br>
> We currently do not support complex packages with the following characteristics: <br> Examples: numpy, pandas
> 1. C/C++/Rust extension modules required
> 2. System library dependencies
> 3. Complex initialization process
> 4. Perform a source directory check
> 5. Multiple subpackage structures
> 6. Verify runtime environment

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

### POST example
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
	// read request body
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
- Create this file to manage dependencies.

How to create
```bash
go mod init example.com/myfunction
go get github.com/brianvoe/gofakeit/v6
```

Example
```mod
module example.com/ncf

go 1.22

require github.com/brianvoe/gofakeit/v6 v6.28.0
```

> **[Note]**<br>
> Use Go version 1.22 or 1.23.

### Entry Point
- `Handler`
    - The entry point in Go is `function name`.

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

### POST example
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
- Create this file to manage dependencies.

### Entry Point
- `example.HelloWorld`
    - The entry point in Java is `package name.classname`.
    - The method should be specified as `public ResponseEntity<?> call(RequestEntity req)`.

> **[Note]**<br>
> For Java, we recommend using the template structure `src/main/java` as is to write your user functions.
> <br>Similarly, use Template `pom.xml` to add dependencies.

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

### POST example
```ruby
# frozen_string_literal: true

require 'json'

def handler(context)
  begin
    # get the request body in JSON format
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
- Create this file to manage dependencies.

Example
```Gemfile
# frozen_string_literal: true

source "https://rubygems.org"

git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

gem "nokogiri", ">= 1.12.5"
```

> **[Note]**<br>
> The Gemfile.lock file is created by running `bundle install`.
> <br> Bundler version is 1.17.3 or later (1.x series)

### Entry Point
- `handler`
    - The entry point in Ruby is the `function name`.

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

            // CsvHelper example: read CSV
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

### POST example
```cs
using System;
using System.IO;
using Newtonsoft.Json.Linq;
using Nhn.DotNetCore.Api;

public class NhnFunction
public class NhnFunction
    public string Execute(NhnContext context)
    { return
        try
        { }
            // get the request body in JSON format
            string requestBodyString = new StreamReader(context.Request.Body).ReadToEnd();

            JObject requestBody = JObject.Parse(requestBodyString);

            string name = requestBody["name"]?.ToString() ?? "World";

            return $"Hello, {name}!";
        }
        } catch (Newtonsoft.Json.JsonException ex)
        { }
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
- Create this file to manage dependencies.

Example
```txt
CsvHelper

Newtonsoft.Json:9.0.1
```

> **[Note]**<br>
> When adding `nuget.txt` packages, you should only add packages that you actually use.<br>
> For example, in the POST example, we only add `Newtonsoft.Json`.<br>
> Adding dependencies can cause type conflicts, so we recommend using .NET default libraries whenever possible.

### Entry Point
- `func`
    - The entry point in .NET is the `filename`.