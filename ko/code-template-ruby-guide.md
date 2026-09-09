<!-- pre-align:aligned sig=015e63f62c78 -->

<a id="compute-cloud-functions-code-template-guide-ruby"></a>
## Compute > Cloud Functions > 코드 템플릿 가이드 > Ruby { #compute-cloud-functions-code-template-guide-ruby }

이 문서는 NHN Cloud의 Cloud Functions 서비스에서 Ruby를 사용하여 함수를 개발하는 방법을 상세히 설명합니다.

<a id="template-information"></a>
## 템플릿 정보 { #template-information }
| 항목              | 값        |
|-----------------|----------|
| 지원 버전       | 3.4.5    |
| 파일명         | parse.rb |
| Entry Point | handler  |

<a id="basic-template"></a>
## 기본 템플릿 { #basic-template }

<a id="hello-world-example"></a>
### Hello World 예시 { #hello-world-example }
가장 기본적인 함수 형태입니다.

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
### Context 객체 { #context-object }
함수에 전달되는 `context` 객체로 로거와 요청 정보에 접근할 수 있습니다.

```ruby
# frozen_string_literal: true

require 'json'

def handler(context)
  # 로거 사용
  context.logger.info("Function execution started")

  # HTTP 요청 정보
  request = context.request
  method = request.request_method # GET, POST 등
  headers = request.env.select { |k, v| k.start_with?('HTTP_') }
  query_params = request.params
  body = request.body.read

  # 응답 데이터 구성
  response_data = {
    method: method,
    headers: headers,
    query_params: query_params,
    body: body
  }.to_json

  # Rack::Response를 사용하여 응답 반환
  response = Rack::Response.new([response_data])
  response['Content-Type'] = 'application/json'
  response.finish
end
```

<a id="download-and-use-template-file"></a>
## 템플릿 파일 다운로드 및 활용 { #download-and-use-template-file }

<a id="template-download"></a>
### 템플릿 다운로드 { #template-download }
Cloud Functions에서 제공하는 Ruby 템플릿을 다운로드하여 로컬 환경에서 개발할 수 있습니다.

템플릿 다운로드 링크: [ruby.zip](../static/etc/ruby.zip)

<a id="template-file-structure"></a>
### 템플릿 파일 구조 { #template-file-structure }
다운로드한 템플릿 파일의 구조는 다음과 같습니다.

```
ruby.zip
├── parse.rb
└── Gemfile
```

<a id="local-development-process"></a>
### 로컬 개발 과정 { #local-development-process }

<a id="local-development-process-unzip"></a>
#### 1. 압축 해제
```bash
# 압축 해제
unzip ruby.zip -d my-function

# 작업 디렉터리 이동
cd my-function
```

<a id="local-development-process-modify-function-codes"></a>
#### 2. 함수 코드 수정
`parse.rb` 파일을 원하는 로직으로 수정합니다.

```ruby
# parse.rb - 간단한 수정 예시
# frozen_string_literal: true

require 'json'
require 'time'

def handler(context)
  begin
    # 쿼리 파라미터에서 이름 가져오기
    name = context.request.params['name'] || 'World'

    # POST 요청인 경우 body에서 메시지 가져오기
    custom_message = ''
    if context.request.post? && context.request.body
      begin
        body = JSON.parse(context.request.body.read)
        custom_message = body['message'] || ''
      rescue JSON::ParserError
        # JSON 파싱 실패 시 무시
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
#### 3. ZIP 파일로 압축
수정된 소스 코드를 다시 ZIP 파일로 압축합니다. `parse.rb`와 `Gemfile`이 최상위에 포함되도록 압축해야 합니다.

```bash
zip my-function.zip parse.rb Gemfile Gemfile.lock
```
!!! tip "참고"
    `Gemfile.lock` 파일은 배포 단계에서 자동으로 생성되지만, 의존성의 정확한 버전을 미리 확인하거나 고정하고 싶은 경우 로컬에서 `bundle install`을 실행하여 직접 생성한 후 함께 압축할 수 있습니다. `Gemfile.lock` 파일이 포함된 경우 해당 파일에 명시된 버전을 사용하여 빌드됩니다.

<a id="upload-from-cloud-functions-console"></a>
### Cloud Functions 콘솔에서 업로드 { #upload-from-cloud-functions-console }
- 함수 생성 또는 수정 시, **사용자 로컬 환경** 방식을 선택합니다.
- **파일 선택**을 클릭하여 생성한 `my-function.zip` 파일을 업로드합니다.

<a id="cautions-for-upload"></a>
### 업로드 시 주의사항 { #cautions-for-upload }
- 업로드 파일: `*.rb`, `Gemfile`이 포함된 ZIP 파일을 업로드해야 합니다.
  - `Gemfile.lock` 파일은 선택 사항입니다. 포함된 경우 해당 파일에 명시된 버전으로 빌드되며, 없을 경우 배포 단계에서 자동으로 생성됩니다.
- ZIP 파일 구조: ZIP 파일의 루트에 파일들이 위치해야 합니다.
- 파일 크기: ZIP 파일 크기는 100MiB 이하로 제한됩니다.

<a id="process-post-request"></a>
## HTTP 메서드별 처리 { #process-post-request }

<a id="process-get-request"></a>
### GET 요청 처리 { #process-get-request }
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
### POST 요청 처리 { #process-post-request-2 }
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
## 패키지 관리(`Gemfile`) { #manage-package-gemfile }

의존성(Gem) 관리를 위해 `Gemfile`을 사용합니다. 필요한 Gem을 추가하고 `bundle install`을 실행하여 `Gemfile.lock`을 생성합니다.

```ruby
# Gemfile
# frozen_string_literal: true

source "https://rubygems.org"

gem "nokogiri", "~> 1.18" # XML/HTML 파서
gem "httparty", "~> 0.23" # HTTP 클라이언트
```

<a id="example-of-external-api-call"></a>
### 외부 API 호출 예시 { #example-of-external-api-call }
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
## 환경 변수 사용 { #use-environment-variables }
함수에 등록한 환경 변수는 `ENV`로 접근할 수 있습니다. 환경 변수는 함수 생성 및 수정의 코드 작성 단계에서 등록합니다. 자세한 내용은 콘솔 사용 가이드를 참고하세요.

```ruby
# frozen_string_literal: true

require 'json'

def handler(context)
  # 환경 변수 읽기
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

!!! tip "참고"
    보안상 다음 키·접두사는 환경 변수로 등록할 수 없습니다.

    공통(모든 런타임)

    - 셸/subprocess: `PATH`, `IFS`, `HOME`, `BASH_ENV`, `ENV`, `SHELLOPTS`
    - 로더/라이브러리: `LD_PRELOAD`, `LD_LIBRARY_PATH`, `LD_AUDIT`
    - glibc 동적 로딩: `GCONV_PATH`, `LOCPATH`, `HOSTALIASES`
    - 프록시: `HTTP_PROXY`, `HTTPS_PROXY`, `ALL_PROXY`(소문자 포함)
    - TLS 신뢰 저장소: `SSL_CERT_FILE`, `SSL_CERT_DIR`, `REQUESTS_CA_BUNDLE`, `CURL_CA_BUNDLE`
    - 플랫폼 내부: `RUNTIME_PORT`, `USERFUNCVOL`, `WSGI_FRAMEWORK`, `SENTRY_DSN`, `SENTRY_RELEASE`, `TIMEOUT`, `BODY_PARSER_LIMIT`
    - 접두사: `LD_`, `DYLD_`, `KUBERNETES_`, `FISSION_`, `CORECLR_`, `COMPLUS_`, `DOTNET_`, `ASPNETCORE_`, `PYTHON`, `NODE_`, `RUBY`

    Ruby

    - `RUBYOPT`, `RUBYLIB`, `GEM_HOME`, `GEM_PATH`

<a id="configure-entry-point"></a>
## Entry Point 설정 { #configure-entry-point }

Entry Point는 함수명을 사용합니다.

- 파일명: `parse.rb`
- 함수명: `handler`
- Entry Point: `handler`

<a id="caution"></a>
### 주의사항 { #caution }
- Bundler 버전: `Gemfile.lock` 생성 시 Bundler 2.6.2 이상 버전 사용을 권장합니다.
- ActiveSupport 등 일부 Gem 호환성: `ActiveSupport`와 같이 C 확장(C extension)에 의존하거나 내부 구조가 복잡한 일부 Gem은 현재 Cloud Functions 환경과 호환성 문제가 발생할 수 있습니다. `activesupport` Gem은 내부적으로 `zeitwerk` Gem에 의존하며, 이 Gem이 파일 시스템을 탐색하는 방식이 실행 환경과 충돌하여 정상적으로 동작하지 않을 수 있습니다. 따라서 가급적 표준 라이브러리나 외부 의존성이 적은 Gem을 사용하는 것을 권장합니다.
