<!-- pre-align:aligned sig=a0129d78150c -->

<a id="compute-cloud-functions-release-notes"></a>
## Compute > Cloud Functions > 릴리스 노트 { #compute-cloud-functions-release-notes }

<a id="july-14-2026"></a>
### 2026. 07. 14. { #july-14-2026 }

<a id="july-14-2026-added-features"></a>
#### 기능 추가
- 환경 변수 기능 추가
  - 함수에 환경 변수를 설정하여 코드와 설정을 분리할 수 있습니다.
  - 인증 정보 등 민감한 값을 코드에 하드코딩하지 않고 관리할 수 있습니다.
- 런타임 EOL(end of life) 표시 기능 추가
  - 함수 목록과 상세 정보에 런타임의 지원 중단/사용 중단 상태를 표시합니다.
  - 함수 생성/수정 시 지원 중단 런타임을 안내하고, 사용 중단 런타임의 선택 및 수정을 제한합니다.

<a id="april-14-2026"></a>
### 2026. 04. 14. { #april-14-2026 }

<a id="april-14-2026-added-features"></a>
#### 기능 추가
- Public API v1.0 추가
  - API로 Cloud Functions를 이용할 수 있습니다.

<a id="january-27-2026"></a>
### 2026. 01. 27. { #january-27-2026 }

<a id="january-27-2026-added-features"></a>
#### 기능 추가
- 함수 버전 관리 기능 추가
  - 빌드한 패키지를 버전으로 관리하고 이전 버전으로 롤백할 수 있습니다.

<a id="november-25-2025"></a>
### 2025. 11. 25. { #november-25-2025 }

<a id="november-25-2025-added-features"></a>
#### 기능 추가
- API Gateway 트리거 추가
  - 동일 프로젝트의 API Gateway 서비스를 활용하여 API Gateway 트리거를 생성할 수 있습니다.

<a id="november-25-2025-feature-updates"></a>
#### 기능 개선
- 런타임 환경 최신 버전 추가 및 일부 버전 사용 중단
  - Go 1.24, 1.25 추가
    - Go 1.22, 1.23 사용 중단 예정
  - Python 3.12, 3.13 추가
  - Ruby 2.6.1 사용 중단, Ruby 3.4.5 추가
  - .NET 7 사용 중단, .NET 8 추가

<a id="july-29-2025"></a>
### 2025. 07. 29. { #july-29-2025 }

<a id="july-29-2025-launch-cloud-functions-service"></a>
#### Cloud Functions 서비스 출시
- 사용자는 함수 단위로 코드를 작성할 수 있으며, 특정 이벤트 발생 시 정의된 함수가 자동으로 실행되어 필요한 작업을 처리합니다. 서버 관리나 인프라 설정 없이 애플리케이션 로직에만 집중할 수 있습니다.
