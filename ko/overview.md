## Compute > Cloud Functions > 개요
사용자는 함수 단위로 코드를 작성하고, 특정 이벤트가 발생할 때마다 정의된 함수를 자동으로 실행하여 필요한 작업을 처리할 수 있습니다. 사용자는 서버 관리나 인프라 설정이 필요하지 않으며, 오직 애플리케이션 로직에만 집중할 수 있습니다.

### 특징
1. 비용 효율성
    - 사용한 만큼만 과금하여 비용 절감
    - 필요할 때만 리소스를 할당하여 관리 비용 감소
    - 요청에 따라 자동으로 확장 및 축소
2. 빠른 개발 및 배포
    - 핵심 비즈니스 로직에 집중 가능
    - DevOps 부담 감소로 개발 속도 향상
3. 유연성과 확장성
    - 다양한 언어 및 런타임 지원
    - 이벤트 기반 아키텍처로 다양한 활용 가능

### 주요 기능
- 다양한 언어(환경)를 제공합니다.
- **코드 에디터**를 통해 간단한 함수 단위 코드를 작성 할 수 있습니다.
- 함수를 수행할 수 있는 https Endpoint를 기본 제공합니다.
- 함수를 일정 주기로 반복 수행할 수 있습니다.

### 두가지 모드 제공
- Pool Manager
- New Deployment
#### Pool Manager
- 함수가 수행될 때만 인스턴스가 생성되어 리소스를 사용합니다.
- 일정 기간 함수가 수행되지 않으면 인스턴스는 사라지고 리소스 사용량은 0이 됩니다.
- 함수 수행 요청량이 많지 않고 이벤트 발생시에만 수행하고 싶을 때 사용합니다.
![overview-01](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-07-29/overview-01.png)
#### New Deployment
- 함수를 생성하면 바로 인스턴스가 생성되어 일정량의 리소스를 계속 사용합니다.
- 빠른 응답 대기를 위해 인스턴스가 유지됩니다.
- 함수 수행 요청량이 많고 빠른 응답이 필요한 경우 사용합니다.
![overview-02](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-07-29/overview-02.png)

### 지원 언어
| 언어     | 버전       |
|----------|------------|
| NodeJS   | 20.16.0    |
|          | 22.5.0     |
|          | debian     |
| Python   | 3.11       |
| Go       | 1.22       |
|          | 1.23       |
| Java     | 17         |
|          | 21         |
| Ruby     | 2.6.1   |
| .NET     | 7          |

### Trigger
- Http Trigger
    - 기본 제공 (GET, POST 지원)
- Timer Trigger
    - cron 형태로 추가 가능