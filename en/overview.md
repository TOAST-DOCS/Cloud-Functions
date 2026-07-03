## Compute > Cloud Functions > Overview
Users can write codes by function unit. Functions defined on a specific event are automatically executed, processing the required tasks. Without server management or infrastructure configuration, you can focus only on application logic.

### Features
- Cost-effectiveness
    - Save money with pay-as-you-go pricing.
    - You can reduce management cost as it allocates resources only when necessary.
    - Scale up and down automatically on demand to keep operating efficiently.
- Rapid development and deployment
    - Focus on the core business logic.
    - Shorten the development speed by reducing the burden of DevOps operation.
- Flexibility and scalability
    - Support for multiple languages and runtimes.
    - The event-based architecture is versatile.

### Main features
- We offer multiple languages (environments).
- The **code editor** makes you write simple function unit code.
- Provide an HTTPS Endpoint as standard to perform the function.
- Iterate the function at regular cycles.

### Two modes available
- Pool Manager
- New Deployment
#### Pool Manager
- Instances are created and use resources only when the function is performed.
- If the function is not performed for a certain period, instances disappear, and the resource usage becomes 0.
- Use it only when the number of requests is small to perform the function and event occurs.

![overview-01](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-08-26/NHN%20Cloud_Guide%20overview_CloudFunctions_1_en.png)

#### New Deployment
- Once functions are created, instances are created, keeping a certain amount of resources being used.
- Instances are kept to reduce response latency.
- Use it when the number of requests is large to perform the function and a quick response is needed.

![overview-02](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-08-26/NHN%20Cloud_Guide%20overview_CloudFunctions_2_en.png)

### Supported languages
| Language     | Version                | End of Support   | End of Service   |
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
|        | 2.6.1 (end of service)     | 2024-01-30 | 2025-01-30 |
| .NET   | 8                 | 2026-11-10 | 2027-11-10 |
|        | 7 (end of service)         | 2024-05-14 | 2025-05-14 |

#### 런타임 지원 상태
각 런타임은 EOL(End of Life) 정책에 따라 다음 세 가지 상태로 구분됩니다.

| 상태 | 의미 | 함수 생성 | 함수 수정 |
| --- | --- | --- | --- |
| 정상 | 정상적으로 지원되는 상태 | 가능 | 가능 |
| 지원 중단 | 지원 중단일이 지나 런타임 커뮤니티 지원이 종료된 상태. 사용에는 제한이 없으나 최신 런타임으로 이전을 권고하는 기간 | 가능 | 가능 |
| 사용 중단 | 사용 중단일이 지난 상태. 함수 실행을 강제로 중단하지는 않으나 정상 동작을 보장하지 않으며, 신규 생성 및 수정이 제한됨 | 불가(목록에서 제외) | 불가(수정 버튼 비활성화) |

- **지원 중단일**: 해당 일자부터 런타임이 '지원 중단' 상태가 됩니다.
- **사용 중단일**: 해당 일자부터 런타임이 '사용 중단' 상태가 됩니다.

런타임의 상태는 함수 목록과 상세 정보, 함수 생성/수정 화면에서 확인할 수 있습니다. 자세한 동작은 콘솔 사용 가이드를 참고하세요.

### Trigger
- HTTP Trigger
    - Basic (support GET, POST)
- Timer Trigger
    - You can add it in the cron expression form.
- API Gateway Trigger
    - It can be integrated with the API Gateway service.