<!-- pre-align:aligned sig=92acb111e0a0 -->

<a id="compute-cloud-functions-overview"></a>
## Compute > Cloud Functions > 개요 { #compute-cloud-functions-overview }
사용자는 함수 단위로 코드를 작성할 수 있으며, 특정 이벤트 발생 시 정의된 함수가 자동으로 실행되어 필요한 작업을 처리합니다. 서버 관리나 인프라 설정 없이 애플리케이션 로직에만 집중할 수 있습니다.

<a id="features"></a>
### 특징 { #features }
- 비용 효율성
    - 사용한 만큼만 과금하여 비용을 절감합니다.
    - 필요할 때만 리소스를 할당하므로 관리 비용을 감소할 수 있습니다.
    - 요청에 따라 자동으로 확장 및 축소하여 효율적으로 운영할 수 있습니다.
- 빠른 개발 및 배포
    - 핵심 비즈니스 로직에 집중할 수 있습니다.
    - DevOps 운영 부담 감소로 개발 속도를 향상시킬 수 있습니다.
- 유연성과 확장성
    - 다양한 언어 및 런타임을 지원합니다.
    - 이벤트 기반 아키텍처로 다양한 활용이 가능합니다.

<a id="main-features"></a>
### 주요 기능 { #main-features }
- 다양한 언어(환경)를 제공합니다.
- **코드 에디터**를 통해 간단한 함수 단위 코드를 작성할 수 있습니다.
- 함수를 수행할 수 있는 HTTPS Endpoint를 기본 제공합니다.
- 함수를 일정 주기로 반복 수행할 수 있습니다.

<a id="two-modes-available"></a>
### 두 가지 모드 제공 { #two-modes-available }
- Pool Manager
- New Deployment
<a id="pool-manager"></a>
#### Pool Manager
- 함수가 수행될 때만 인스턴스가 생성되어 리소스를 사용합니다.
- 일정 기간 함수가 수행되지 않으면 인스턴스는 사라지고 리소스 사용량은 0이 됩니다.
- 함수 수행 요청량이 많지 않고 이벤트 발생 시에만 수행하고 싶을 때 사용합니다.

![overview-01](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-08-26/NHN%20Cloud_Guide%20overview_CloudFunctions_1_ko.png)

<a id="new-deployment"></a>
#### New Deployment
- 함수를 생성하면 바로 인스턴스가 생성되어 일정량의 리소스를 계속 사용합니다.
- 빠른 응답을 위해 인스턴스 생성을 유지합니다.
- 함수 수행 요청량이 많고 빠른 응답이 필요한 경우 사용합니다.

![overview-02](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-08-26/NHN%20Cloud_Guide%20overview_CloudFunctions_2_ko.png)

<a id="supported-languages"></a>
### 지원 언어 { #supported-languages }

| 언어     | 버전                | 지원 중단일   | 사용 중단일   |
|--------|-------------------|------------|------------|
| NodeJS | 22.5.0            | 2027-04-30 | 2027-10-30 |
|        | 20.16.0           | 2026-04-30 | 2026-10-30 |
|        | Debian(20.16.0)   | 2026-04-30 | 2026-10-30 |
| Python | 3.13              | 2029-10-10 | 2030-04-10 |
|        | 3.12              | 2028-10-02 | 2029-04-02 |
|        | 3.11              | 2027-10-24 | 2028-04-24 |
| Go     | 1.25              | -          | -          |
|        | 1.24              | -          | -          |
|        | 1.23              | 2026-02-21 | 2026-08-21 |
|        | 1.22              | 2026-01-28 | 2026-07-28 |
| Java   | 21                | 2029-12-31 | 2030-12-31 |
|        | 17                | 2027-10-31 | 2028-10-31 |
| Ruby   | 3.4.5             | 2028-03-31 | 2028-09-30 |
|        | 2.6.1(사용 중단)     | 2024-01-30 | 2025-01-30 |
| .NET   | 8                 | 2026-11-10 | 2027-11-10 |
|        | 7(사용 중단)         | 2024-05-14 | 2025-05-14 |

<a id="trigger"></a>
### Trigger { #trigger }
- HTTP Trigger
    - 기본 제공(GET, POST 지원)
- Timer Trigger
    - cron 표현식 형태로 추가할 수 있습니다.
- API Gateway Trigger
    - API Gateway 서비스와 연동하여 사용할 수 있습니다.