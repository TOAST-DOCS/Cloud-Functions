<!-- pre-align:aligned sig=015e63f62c78 -->

<a id="compute-cloud-functions-code-template-guide-ruby"></a>
## Compute > Cloud Functions > Code Template Guide   > Ruby { #compute-cloud-functions-code-template-guide-ruby }

This document details how to develop functions by using Ruby from NHN Cloud's Cloud Functions service.

<a id="template-information"></a>
## Template information { #template-information }
| Item         | Value        |
|-----------------|----------|
| **Supported version**      | 3.4.5    |
| **File name**         | parse.rb |
| **Entry Point** | handler |

<a id="basic-template"></a>
## Basic template { #basic-template }

<a id="hello-world-example"></a>
### Hello World example { #hello-world-example }
A basic form of function.

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

<a id="context-object"></a>
### Context object { #context-object }
You can access logger and request information through the `context` object passed to the function.

```ruby
# frozen_string_literal: true

require 'json'

def handler(context)
  # Use logger
  context.logger.info("Function execution started")

  # HTTP request information
  request = context.request
  method = request.request_method # GET, POST, etc.
  headers = request.env.select { |k, v| k.start_with?('HTTP_') }
  query_params = request.params
  body = request.body.read

  # Response data configuration
  response_data = {
    method: method,
    headers: headers,
    query_params: query_params,
    body: body
  }.to_json

  # Rack::return a response using Response
  response = Rack::Response.new([response_data])
  response['Content-Type'] = 'application/json'
  response.finish
end
```

<a id="download-and-use-template-file"></a>
## Download and use template file { #download-and-use-template-file }

<a id="template-download"></a>
### Template download { #template-download }
You can download the Ruby template provided by Cloud Functions to develop a local environment.

**Template download link**: [ruby.zip](../static/etc/ruby.zip)

<a id="template-file-structure"></a>
### Template file structure { #template-file-structure }
The structure of the downloaded template file is as follows:

```
ruby.zip
├── parse.rb
└── Gemfile
```

<a id="local-development-process"></a>
### Local development process { #local-development-process }

<a id="local-development-process-unzip"></a>
#### 1. Unzip
```bash
# Unzip
unzip ruby.zip -d my-function

# Move to task directory
cd my-function
```

<a id="local-development-process-modify-function-codes"></a>
#### 2. Modify function codes
Modify `parse.rb` file with the logic you want.

```ruby
# parse.rb - Simple modification example
# frozen_string_literal: true

require 'json'
require 'time'

def handler(context)
  begin
    # Import name from query parameter
    name = context.request.params['name'] || 'World'

    # Import message from body if POST request
    custom_message = ''
    if context.request.post? && context.request.body
      begin
        body = JSON.parse(context.request.body.read)
        custom_message = body['message'] || ''
      rescue JSON::ParserError
        # Ignore if JSON parsing fails
      end
    end

    response_data = {
      greeting: "Hello, #{name}!",
      message: custom_message,
      method: context.request.request_method,
      timestamp: Time.now.iso8601
    }.to_json

    Rack::Response.new([response_data], 200, { 'Content-Type' => 'application/json' }).finish
  rescue => e
    error_response = {
      error: 'Internal Server Error',
      details: e.message
    }.to_json
    Rack::Response.new([error_response], 500, { 'Content-Type' => 'application/json' }).finish
  end
end
```

<a id="local-development-process-compress-into-a-zip-file"></a>
#### 3. Compress into a ZIP file
Compress the modified source code into ZIP file. You must compress it so that `parse.rb` and `Gemfile` are included at the top level.

```bash
zip my-function.zip parse.rb Gemfile Gemfile.lock
```
**Note**: The `Gemfile.lock` file is automatically created during the deployment phase, but if you want to check or lock the exact versions of your dependencies in advance, you can create one yourself by running `bundle install` locally and then compress it together. If a `Gemfile.lock` file is included, the build will use the versions specified in it.

<a id="upload-from-cloud-functions-console"></a>
### Upload from Cloud Functions console { #upload-from-cloud-functions-console }
- When creating or modifying a function, select the **User Local Environment** method.
- Click **Select File** to upload the `my-function.zip` file you created.

<a id="cautions-for-upload"></a>
### Cautions for upload { #cautions-for-upload }
- **Upload File**: Upload **ZIP file** which includes `*.rb`, `Gemfile`.
  - `Gemfile.lock` file is optional. If included, it will be built with the version specified in the file, and if not present, it will be automatically created during the deployment phase.
- **ZIP Files Structure**: Files must be located directly in the root of the ZIP file.
- **File Size**: ZIP file size is limited to 100 MiB.

<a id="process-post-request"></a>
## Process POST request { #process-post-request }

<a id="process-get-request"></a>
### Process GET request { #process-get-request }
```ruby
# frozen_string_literal: true

require 'json'
require 'time'

def handler(context)
  unless context.request.get?
    return Rack::Response.new(['Method Not Allowed'], 405).finish
  end

  name = context.request.params['name'] || 'World'
  greeting = context.request.params['greeting'] || 'Hello'

  response_data = {
    message: "#{greeting}, #{name}!",
    timestamp: Time.now.iso8601
  }.to_json

  Rack::Response.new([response_data], 200, { 'Content-Type' => 'application/json' }).finish
end
```

<a id="process-post-request-2"></a>
### Process POST request { #process-post-request-2 }
```ruby
# frozen_string_literal: true

require 'json'
require 'time'
require 'securerandom'

def handler(context)
  unless context.request.post?
    return Rack::Response.new(['Method Not Allowed'], 405).finish
  end

  begin
    request_body = JSON.parse(context.request.body.read)
    name = request_body['name']

    unless name && !name.empty?
      return Rack::Response.new(['Missing required field: name'], 400).finish
    end

    response_data = {
      id: SecureRandom.hex(4),
      name: name,
      email: request_body['email'] || 'not provided',
      processed_at: Time.now.iso8601
    }.to_json

    Rack::Response.new([response_data], 201, { 'Content-Type' => 'application/json' }).finish
  rescue JSON::ParserError
    Rack::Response.new(['Invalid JSON format'], 400).finish
  end
end
```

<a id="manage-package-gemfile"></a>
## Manage package (`Gemfile`) { #manage-package-gemfile }

Use `Gemfile` to manage dependencies (gems). Add the required gems and run `bundle install` to generate `Gemfile.lock`.

```ruby
# Gemfile
# frozen_string_literal: true

source "https://rubygems.org"

gem "nokogiri", "~> 1.18" # XML/HTML parser
gem "httparty", "~> 0.23" # HTTP client
```

<a id="example-of-external-api-call"></a>
### Example of external API call { #example-of-external-api-call }
```ruby
# frozen_string_literal: true

require 'json'
require 'httparty'

def handler(context)
  begin
    user_id = context.request.params['userId']

    api_url = if user_id
                "https://jsonplaceholder.typicode.com/users/#{user_id}"
              else
                "https://jsonplaceholder.typicode.com/users"
              end

    response = HTTParty.get(api_url)

    unless response.success?
      error_message = {
        error: "External API Error",
        status_code: response.code,
        details: response.body
      }.to_json
      return Rack::Response.new([error_message], response.code, { 'Content-Type' => 'application/json' }).finish
    end

    Rack::Response.new([response.body], 200, { 'Content-Type' => 'application/json' }).finish

  rescue => e
    error_response = {
      error: 'Internal Server Error',
      details: e.message
    }.to_json
    Rack::Response.new([error_response], 500, { 'Content-Type' => 'application/json' }).finish
  end
end
```

<a id="use-environment-variables"></a>
## Use Environment Variables { #use-environment-variables }
Environment variables registered for a function can be accessed via `ENV`. Environment variables are registered during the code writing step of function creation and modification. For more information, see the console user guide.

```ruby
# frozen_string_literal: true

require 'json'

def handler(context)
  # Read environment variables
  db_host = ENV['DB_HOST']
  api_key = ENV['API_KEY']

  if api_key.nil? || api_key.empty?
    error_response = { error: 'API_KEY is not set' }.to_json
    return Rack::Response.new([error_response], 500, { 'Content-Type' => 'application/json' }).finish
  end

  response_data = { db_host: db_host }.to_json
  Rack::Response.new([response_data], 200, { 'Content-Type' => 'application/json' }).finish
end
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
    - Prefixes: `LD_`, `DYLD_`, `KUBERNETES_`, `FISSION_`, `CORECLR_`, `COMPLUS_`, `DOTNET_`, `ASPNETCORE_`, `PYTHON`, `NODE_`, `RUBY`

    Ruby

    - `RUBYOPT`, `RUBYLIB`, `GEM_HOME`, `GEM_PATH`

<a id="configure-entry-point"></a>
## Configure Entry Point { #configure-entry-point }

Entry Point uses the function name.

- File name: `parse.rb`
- Function name: `handler`
- Entry Point: `handler`

<a id="caution"></a>
### Caution { #caution }
- **Bundler version**: We recommend using Bundler 2.6.2 or later when generating `Gemfile.lock`.
- **Some Gem compatibility such as ActiveSupport**: Some gems that rely on C extensions, such as `ActiveSupport`, or have complex internal structures may have compatibility issues with the current Cloud Functions environment. The `activesupport` Gem internally depends on the `zeitwerk` Gem, and the way this Gem navigates the filesystem may conflict with the execution environment, causing it to malfunction. Therefore, we recommend using standard libraries or Gems with fewer external dependencies as much as possible.
