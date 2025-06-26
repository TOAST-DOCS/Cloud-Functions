## Compute > Cloud Functions > 콘솔 사용 가이드
이 문서는 NHN Cloud Functions 콘솔에서 함수를 생성하고 관리하는 방법을 설명합니다.

## 함수 관리
함수를 생성/수정/삭제/복사할 수 있습니다.

### 함수 생성
함수 설정을 하고 코드를 작성한 뒤 생성 버튼을 클릭하면 코드가 빌드되고 함수가 생성됩니다.

![console-guide-03](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-07-29/console-guide-03.png)
#### 함수 설정
<table class="it">
    <tr>
        <th>분류</th>
        <th>항목</th>
        <th>설명</th>
    </tr>
    <tr>
        <td rowspan="2">기본 정보</td>
        <td>이름</td>
        <td>함수의 이름<br> 중복된 함수 이름은 허용하지 않습니다.<br> 함수 엔드포인트 URL로 사용됩니다.</td>
    </tr>
    <tr>
        <td>설명</td>
        <td>함수의 설명, 최대 250자</td>
    </tr>
    <tr>
        <td rowspan="7">함수 설정</td>
        <td>엔드포인트 URL</td>
        <td>함수 호출 HTTP Endpoint URL<br>함수 이름 입력 시 자동으로 변경됩니다.</td>
    </tr>
    <tr>
        <td>유형</td>
        <td>Pool Manager<br> New Deployment</td>
    </tr>
    <tr>
        <td>리소스</td>
        <td>함수 동작 환경 리소스 선택<br>Pool Manager 유형일 경우 지정된 리소스만 선택 가능<br>New Deployment 유형일 경우 커스텀 리소스 입력 가능</td>
    </tr>
    <tr>
        <td>실행 제한 시간</td>
        <td>함수 실행 시간을 지정(Time out). 함수가 실행되고 지정된 시간을 초과하면 함수는 강제 중단되고 오류로 응답</td>
    </tr>
    <tr>
        <td>(Pool Manager) 동시 실행 설정</td>
        <td>함수의 동시 실행 수 지정. 동시에 함수 호출이 되는 경우 호출 횟수만큼 인스턴스가 동시에 실행되며, 최대 인스턴스는 제한됩니다.</td>
    </tr>
    <tr>
        <td>(New Deployment) 최소 인스턴스 수</td>
        <td>최소한으로 유지할 인스턴스 수 지정.</td>
    </tr>
    <tr>
        <td>(New Deployment) 최대 인스턴스 수</td>
        <td>자동으로 확장 가능한 최대 인스턴스 수 지정.</td>
    </tr>
    <tr>
        <td>로그 설정</td>
        <td>로그 서비스 연동</td>
        <td>Log & Crash Search 서비스를 사용하여 연동할 것인지 선택<br>함수의 로그는 Log & Crash Search 서비스에서 직접 확인 가능합니다.</td>
    </tr>
</table>

> **[(Pool Manager) 동시 실행 설정]**
> ![console-guide-05](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-07-29/console-guide-05.png)

<br>

> **[참고]** <br>
> **(New Deployment) 인스턴스 수** - 리소스 사용량에 따라 지정된 인스턴스 수만큼 생성되지 않을 수 있습니다.

<br>

![console-guide-04](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-07-29/console-guide-04.png)




#### 코드 작성
<table class="it">
    <tr>
        <th>분류</th>
        <th>항목</th>
        <th>설명</th>
    </tr>
    <tr>
        <td rowspan="2">소스 코드</td>
        <td>런타임 환경</td>
        <td>함수의 런타임 환경을 선택</td>
    </tr>
    <tr>
        <td>Entry Point</td>
        <td>함수의 진입 지점을 지정. ex) 함수명 <br>런타임 환경의 템플릿에 맞춰 자동으로 완성됩니다.<br>임의로 수정 시 작성한 소스 코드의 Entry Point와 일치해야 합니다.</td>
    </tr>
    <tr>
        <td rowspan="3">코드</td>
        <td>코드 에디터</td>
        <td>런타임 환경의 템플릿 파일이 로드되고, 해당 파일을 수정하여 함수를 작성</td>
    </tr>
    <tr>
        <td>사용자 로컬 환경</td>
        <td>사용자 로컬 환경에서 함수 코드를 작성하여 zip 파일 형태로 업로드</td>
    </tr>
    <tr>
        <td>배포</td>
        <td>사용자가 작성하거나 업로드한 코드를 배포하여 빌드가 정상인지 확인하는 용도로 사용됩니다.<br>참고: 함수 생성 시 해당 배포 버전을 사용하지 않고 생성 시 최신 업데이트된 소스 코드로 재빌드됩니다.</td>
    </tr>
    <tr>
        <td rowspan="2">테스트</td>
        <td>테스트 이벤트</td>
        <td>함수에 전달할 JSON Body를 작성</td>
    </tr>
    <tr>
        <td>테스트</td>
        <td>이벤트에 작성한 JSON Body를 전달하여 함수를 테스트. (배포가 선행되어야 합니다.) <br>로그로 함수 동작을 확인할 수 있습니다.</td>
    </tr>
    <tr>
        <td></td>
        <td>생성</td>
        <td>생성 버튼을 이용해 함수 설정과 작성한 함수에 맞게 함수를 생성</td>
    </tr>
</table>

<br>

> **[참고]** <br> 배포 버튼은 사용자가 작성한 소스 코드 빌드 확인 용도로만 사용되며, 함수 생성 시 배포로 테스트한 빌드가 아닌 최신 소스 코드로 다시 재빌드되어 생성됩니다.

### 함수 수정
기존 함수의 함수 설정 및 코드를 수정하기 위해 수정 버튼을 이용해 함수를 수정합니다.
#### 수정 불가 항목
- 이름, 런타임 환경
    - 해당 항목을 제외한 모든 항목 수정 가능.
#### 소스 코드
- 코드 에디터를 사용하는 경우 기존 코드가 로드됩니다.
- 사용자 로컬 환경의 zip 파일을 업로드하여 함수를 생성했을 경우, 코드 에디터로 변경하면 zip 파일을 보여주지 않고 기본 템플릿 코드를 로드합니다.

### 함수 삭제
기존 함수를 선택하여 삭제합니다. 한 번에 여러 함수 삭제가 가능합니다.

### 함수 복사
기존 함수와 동일한 함수를 복사합니다. 이름은 중복이 불가능하여 복사 전에 이름을 새롭게 작성할 수 있습니다.

## 함수 정보
### 함수 목록
![console-guide-01](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-07-29/console-guide-01.png)
- 사용자가 생성한 함수 목록을 확인할 수 있습니다.
- 빌드 상태는 자동으로 업데이트되어 함수가 사용 가능한지 확인할 수 있습니다.
### 함수 기본 정보
![console-guide-02](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-07-29/console-guide-02.png)
- 함수의 기본 정보를 확인할 수 있습니다.
- 로그 관리 항목에서 Log & Crash Search 버튼을 통해 Log & Crash Search 서비스로 이동하여 로그를 확인할 수 있습니다.
- 빌드 상태 항목에서 빌드 로그 확인 버튼을 통해 빌드 로그를 확인할 수 있습니다.
- 사용자가 작성한 코드를 zip 파일 형태로 다운로드받을 수 있습니다.
### 함수 트리거 관리
함수를 수행할 수 있는 트리거를 관리할 수 있습니다. HTTP 트리거는 기본 제공됩니다.
#### 트리거 생성/수정
- Timer
    - Value: Cron 문자열로 주기를 입력
#### 트리거 삭제
- 여러 건의 트리거를 선택하여 삭제할 수 있습니다. 기본 트리거인 HTTP 트리거는 삭제 불가능합니다.

> **[참고]**
> <br>현재는 Timer 트리거만 지원합니다.
### 함수 모니터링
![console-guide-06](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-07-29/console-guide-06.png)
- 함수의 사용량을 확인할 수 있습니다.
- 함수 호출 횟수, 호출 거부 수, 오류 발생 횟수, 성공률, 함수 실행 시간 지표를 제공합니다.
- 설정된 시간 내의 지표를 제공합니다.
- 로그가 연동된 경우 Log & Crash Search 버튼을 이용해 Log & Crash Search 서비스로 이동할 수 있습니다.

| 항목 | 설명 |
| --- | --- |
| 함수 호출 횟수 | 총 함수 호출 횟수 (초당) |
| 호출 거부 수 | 인스턴스가 생성되지 않아 함수 호출 실패한 횟수 (초당) |
| 오류 발생 횟수 | 응답코드가 200이 아닌 응답 횟수 (초당) |
| 성공률 | 총 함수 호출 횟수 대비 성공한 호출 비율 |
| 함수 실행 시간 | 함수 호출에 대한 응답 시간 |

> **[참고]**
> <br>호출 거부 수는 Pool Manager 유형일 경우만 해당합니다.
