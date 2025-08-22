## Compute > Cloud Functions > 概要
ユーザーは関数単位でコードを作成でき、特定のイベントが発生した際に定義された関数が自動で実行され、必要な処理を行います。サーバー管理やインフラ設定を行うことなく、アプリケーションロジックに専念することができます。

### 特徴
- コスト効率性
    - 使用した分だけ課金するため、コストを削減できます。
    - 必要な時にのみリソースを割り当てるため、管理コストを削減できます。
    - リクエストに応じて自動的にスケールアウト/スケールインし、効率的に運用できます。
- 迅速な開発とデプロイ
    - 核心的なビジネスロジックに集中できます。
    - DevOps運用負担の軽減により、開発速度を向上させることができます。
- 柔軟性と拡張性
    - 様々な言語とランタイムをサポートします。
    - イベントベースのアーキテクチャにより、多様な活用が可能です。

### 主な機能
- 様々な言語(環境)を提供します。
- **コードエディタ**を使用して、簡単な関数単位コードを作成できます。
- 関数を実行できるHTTPSエンドポイントを標準で提供します。
- 関数を一定間隔で繰り返し実行できます。

### 2つのモードを提供
- Pool Manager
- New Deployment
#### Pool Manager
- 関数が実行される際にのみインスタンスが作成され、リソースを使用します。
- 一定期間関数が実行されない場合、インスタンスは消滅し、リソース使用量は0になります。
- 関数実行リクエスト量が少なく、イベント発生時のみ実行したい場合に使用します。
![overview-01](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-08-26/NHN%20Cloud_Guide%20overview_CloudFunctions_1_ja.png)
#### New Deployment
- 関数を作成すると、すぐにインスタンスが作成され、一定量のリソースを継続して使用します。
- 高速な応答のためにインスタンス作成を維持します。
- 関数実行リクエストが多く、迅速な応答が必要な場合に使用します。
![overview-02](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-08-26/NHN%20Cloud_Guide%20overview_CloudFunctions_2_ja.png)

### サポート言語
| 言語   | バージョン     |
|----------|------------|
| NodeJS   | 20.16.0    |
|          | 22.5.0     |
|          | Debian(20.16.0)     |
| Python   | 3.11       |
| Go       | 1.22       |
|          | 1.23       |
| Java     | 17         |
|          | 21         |
| Ruby     | 2.6.1      |
| .NET     | 7          |

### Trigger
- HTTP Trigger
    - 基本提供(GET、POSTサポート)
- Timer Trigger
    - cron式で追加可能です。
