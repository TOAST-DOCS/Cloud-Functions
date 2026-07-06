## Compute > Cloud Functions > Release Notes

### 2026. 07. 14.

#### 기능 추가
- 환경 변수 기능 추가
  - 함수에 환경 변수를 설정하여 코드와 설정을 분리할 수 있습니다.
  - 인증 정보 등 민감한 값을 코드에 하드코딩하지 않고 관리할 수 있습니다.
- 런타임 EOL(end of life) 표시 기능 추가
  - 함수 목록과 상세 정보에 런타임의 지원 중단/사용 중단 상태를 표시합니다.
  - 함수 생성/수정 시 지원 중단 런타임을 안내하고, 사용 중단 런타임의 선택 및 수정을 제한합니다.

### April 14, 2026

#### Added Features
- Added Public API v1.0
  - Added support for using Cloud Functions via API.

### January 27, 2026

#### Added Features
- Added function versioning
  - Manage build packages by version and rollback to previous versions.

### November 25, 2025

#### Added Features
- Added API Gateway trigger
  - Added the feature to create API Gateway triggers by utilizing the API Gateway service in the same project.

#### Feature Updates
- Added the latest runtime environment and deprecated some versions
  - Added Go 1.24, 1.25
    - Go 1.22, 1.23 scheduled for deprecation
  - Added Python 3.12, 3.13
  - Deprecated Ruby 2.6.1, added Ruby 3.4.5
  - Deprecated .NET 7, added .NET 8

### July 29. 2025

#### Launch Cloud Functions Service
- Users can write codes by function unit. Functions defined on a specific event are automatically executed, processing the required tasks. Without server management or infrastructure configuration, you can focus only on application logic.