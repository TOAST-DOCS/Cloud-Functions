## Compute > Cloud Functions > Code Template Guide
This document describes the template code provided when writing code for functions of NHN Cloud Functions.

## Template code list
| Language     | Filename            | Entry Point         |
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

> [Note]<br>
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

### Package
`requirements.txt`
- Create this file to manage dependencies.

### Entry Point
- `user.main`
    - The entry point in Python is `filename.functionname`.

> [Note]<br>
> We currently do not support complex packages with the following characteristics:
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

### Package
`go.mod`
- Create this file to manage dependencies.

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
- Create this file to manage dependencies.

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

### Package
`nuget.txt`
- Create this file to manage dependencies.

### Entry Point
- `func`
    - The entry point in .NET is the `filename`.