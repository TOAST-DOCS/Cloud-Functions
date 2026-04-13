## Cloud Functions API v1.0 ガイド

**Compute > Cloud Functions > API ガイド > API v1.0 ガイド**

## Cloud Functions API v1.0 共通情報

### API エンドポイント

Cloud Functions APIを呼び出すためのリージョン別エンドポイントは以下のとおりです。

| リージョン | エンドポイント |
| --- |-----------------------------------------------------|
| 韓国(パンギョ)リージョン | https://kr1-cloud-functions.api.nhncloudservice.com |

### 認証及び権限

Cloud Functionsは、API呼び出し時の認証/認可にUser Access Keyトークンを使用します。
User Access Keyトークンは、User Access Keyをもとに発行されるBearerタイプの一時的なアクセストークンです。
User Access Keyトークンの発行手順や使用方法の詳細は、[User Access Keyトークン](/nhncloud/ko/public-api/user-access-key-token)をご参照ください。

### レスポンス共通情報

全てのAPIレスポンスは以下の共通形式に従います。

<details>
  <summary><strong>成功レスポンス</strong></summary>

```json
{
  "header": {
    "isSuccessful": true,
    "resultCode": 0,
    "resultMessage": "success"
  },
  "data": {
    ...
  }
}
```

</details>

<details>
  <summary><strong>失敗レスポンス</strong></summary>

```json
{
  "header": {
    "isSuccessful": false,
    "resultCode": -1,
    "resultMessage": "Error message"
  },
  "data": null
}
```

</details>

| 名前 | タイプ | 説明 |
| --- | --- | --- |
| header | Object | レスポンスヘッダ |
| header.isSuccessful | Boolean | API呼び出しの成否 |
| header.resultCode | Integer | 結果コード(成功: 0、失敗: -1) |
| header.resultMessage | String | 結果メッセージ |
| data | Object | レスポンスデータ(APIごとに異なる) |

---

## 環境一覧

使用可能なランタイム環境一覧を照会します。

### リクエスト

```
GET /v1.0/env/list
```

### リクエストパラメータ

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |

### リクエストボディ

このAPIはリクエストボディを必要としません。

### レスポンス

<details>
  <summary><strong>サンプルコード</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "success"
    },
    "data": [
        {
            "id": 1,
            "environment": "NodeJS",
            "version": "22.5.0",
            "entryPoint": "index.handler"
        },
        {
            "id": 2,
            "environment": "Python",
            "version": "3.9",
            "entryPoint": "main.handler"
        }
    ]
}
```

</details>

| 名前 | タイプ | 説明 |
| --- | --- | --- |
| data | Array | 環境一覧 |
| data[].id | Integer | 環境ID |
| data[].environment | String | ランタイム環境(例: NodeJS) |
| data[].version | String | ランタイムバージョン(例: 22.5.0) |
| data[].entryPoint | String | デフォルトのエントリーポイント |

---

## 関数一覧

関数一覧を照会します。

### リクエスト

```
GET /v1.0/functions
```

### リクエストパラメータ

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| page | Query | Integer | N | 現在のページ(デフォルト値: 0) |
| pageSize | Query | Integer | N | 1ページに表示する件数(デフォルト値: 5000) |

### リクエストボディ

このAPIはリクエストボディを必要としません。

### レスポンス

<details>
  <summary><strong>サンプルコード</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "success"
    },
    "data": {
        "totalCount": 2,
        "functions": [
            {
                "name": "my-function",
                "description": "サンプル関数",
                "runtime": "NodeJS 22.5.0",
                "executorType": "poolmgr",
                "memory": 256,
                "timeout": 60,
                "buildStatus": "SUCCEEDED",
                "createdAt": "1700000000",
                "updatedAt": "2026-03-09T14:59:44Z"
            }
        ]
    }
}
```

</details>

| 名前 | タイプ | 説明 |
| --- | --- | --- |
| data.totalCount | Integer | 関数の総数 |
| data.functions | Array | 関数一覧 |
| data.functions[].name | String | 関数名 |
| data.functions[].description | String | 関数の説明 |
| data.functions[].runtime | String | ランタイム(例: NodeJS 22.5.0) |
| data.functions[].executorType | String | 実行タイプ(poolmgrまたはnewdeploy) |
| data.functions[].memory | Integer | メモリ(MB) |
| data.functions[].timeout | Integer | タイムアウト(秒) |
| data.functions[].buildStatus | String | ビルド状態(PENDING、RUNNING、SUCCEEDED、FAILED) |
| data.functions[].createdAt | String | 作成時間(epoch seconds) |
| data.functions[].updatedAt | String | 更新時間(ISO 8601、例: 2026-03-09T14:59:44Z) |

---

## 関数詳細照会

関数の詳細情報とビルドログを同時に照会します。

### リクエスト

```
GET /v1.0/functions/{functionName}
```

### リクエストパラメータ

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| functionName | URL | String | Y | 関数名 |

### リクエストボディ

このAPIはリクエストボディを必要としません。

### レスポンス

<details>
  <summary><strong>サンプルコード</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "success"
    },
    "data": {
        "name": "my-function",
        "description": "サンプル関数",
        "runtime": "NodeJS 22.5.0",
        "executorType": "poolmgr",
        "memory": 256,
        "timeout": 60,
        "buildStatus": "SUCCEEDED",
        "createdAt": "1700000000",
        "updatedAt": "2026-03-09T14:59:44Z",
        "entryPoint": "index.handler",
        "requestPerPod": 1,
        "minInstance": null,
        "maxInstance": null,
        "buildLog": "Build succeeded...",
        "currentVersionName": "v1",
        "sourceFileName": "source.zip",
        "lncsAppkey": null
    }
}
```

</details>

| 名前 | タイプ | 説明 |
| --- | --- | --- |
| data.name | String | 関数名 |
| data.description | String | 関数の説明 |
| data.runtime | String | ランタイム(例: NodeJS 22.5.0) |
| data.executorType | String | 実行タイプ(poolmgrまたはnewdeploy) |
| data.memory | Integer | メモリ(MB) |
| data.timeout | Integer | タイムアウト(秒) |
| data.buildStatus | String | ビルド状態(PENDING、RUNNING、SUCCEEDED、FAILED) |
| data.createdAt | String | 作成時間(epoch seconds) |
| data.updatedAt | String | 更新時間(ISO 8601、例: 2026-03-09T14:59:44Z) |
| data.entryPoint | String | 関数のエントリーポイント |
| data.requestPerPod | Integer | Podあたりの同時リクエスト数 |
| data.minInstance | Integer | 最小インスタンス数 |
| data.maxInstance | Integer | 最大インスタンス数 |
| data.buildLog | String | ビルドログ |
| data.currentVersionName | String | 現在のバージョン名 |
| data.sourceFileName | String | ソースファイル名 |
| data.lncsAppkey | String | LnCS Appkey |

---

## 関数作成

新しい関数を作成します。multipart/form-dataでソースファイルをアップロードします。
runtimeは`{environment}-{version}`形式で入力する必要があります(例: NodeJS-22.5.0)。使用可能なランタイムは環境一覧照会APIで確認できます。

executorTypeによって必須パラメータが異なります。poolManagerの場合はrequestPerPodが、newDeploymentの場合はminInstanceとmaxInstanceが必須です。

### リクエスト

```
POST /v1.0/functions
```

### リクエストパラメータ

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |

### リクエストボディ

Content-Type: multipart/form-data

| 名前 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- |
| name | String | Y | 関数名 |
| description | String | N | 関数の説明 |
| executorType | String | Y | 実行タイプ(poolManagerまたはnewDeployment) |
| runtime | String | Y | ランタイム({environment}-{version}形式、例: NodeJS-22.5.0) |
| entryPoint | String | Y | 関数のエントリーポイント |
| memory | Integer | Y | メモリ(MB)。デフォルトのリソースセット値: 128、256、512、1024、2048、4096。newDeploymentの場合は64～4096の範囲のカスタム値も使用可能 |
| requestPerPod | Integer | Conditional | Podあたりの同時リクエスト数(poolManagerの場合は1～1000、デフォルト値: 1) |
| timeout | Integer | N | タイムアウト(秒、1～900、デフォルト値: 60) |
| minInstance | Integer | Conditional | 最小インスタンス数(newDeploymentの場合は必須、1～100、maxInstance以下) |
| maxInstance | Integer | Conditional | 最大インスタンス数(newDeploymentの場合は必須、1～100) |
| lncsAppkey | String | N | LnCS Appkey |
| sourceFile | Binary | Y | ソースコードファイル(ZIP) |

### レスポンス

<details>
  <summary><strong>サンプルコード</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Successfully created the function."
    },
    "data": null
}
```

</details>

---

## 関数修正

関数を修正します。multipart/form-dataでソースファイルをアップロードできます。

ソースファイル(sourceFile)は任意項目です。ソースファイルを指定しない場合、既存のソースコードが維持されます。

### リクエスト

```
PUT /v1.0/functions/{functionName}
```

### リクエストパラメータ

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| functionName | URL | String | Y | 修正する関数名 |

### リクエストボディ

Content-Type: multipart/form-data

| 名前 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- |
| description | String | N | 関数の説明 |
| executorType | String | Y | 実行タイプ(poolManagerまたはnewDeployment) |
| runtime | String | Y | ランタイム({environment}-{version}形式、例: NodeJS-22.5.0) |
| entryPoint | String | Y | 関数のエントリーポイント |
| memory | Integer | Y | メモリ(MB)。デフォルトのリソースセット値: 128、256、512、1024、2048、4096。newDeploymentの場合は64～4096の範囲のカスタム値も使用可能 |
| requestPerPod | Integer | Conditional | Podあたりの同時リクエスト数(poolManagerの場合は1～1000) |
| timeout | Integer | N | タイムアウト(秒、1～900、デフォルト値: 60) |
| minInstance | Integer | Conditional | 最小インスタンス数(newDeploymentの場合は必須、1～100、maxInstance以下) |
| maxInstance | Integer | Conditional | 最大インスタンス数(newDeploymentの場合は必須、1～100) |
| lncsAppkey | String | N | LnCS Appkey |
| sourceFile | Binary | N | ソースコードファイル(ZIP) |

### レスポンス

<details>
  <summary><strong>サンプルコード</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Successfully updated the function."
    },
    "data": null
}
```

</details>

---

## 関数削除

関数を一括削除します。

### リクエスト

```
DELETE /v1.0/functions
```

### リクエストパラメータ

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |

### リクエストボディ

<details>
  <summary><strong>サンプルコード</strong></summary>

```json
{
    "names": ["function-1", "function-2"]
}
```

</details>

| 名前 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- |
| names | Array | Y | 削除する関数名一覧 |

### レスポンス

<details>
  <summary><strong>サンプルコード</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Successfully deleted the function."
    },
    "data": null
}
```

</details>

---

## 関数実行(GET)

GETメソッドで関数を実行します。

### リクエスト

```
GET /v1.0/functions/{functionName}/invoke
```

### リクエストパラメータ

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| functionName | URL | String | Y | 実行する関数名 |

### リクエストボディ

このAPIはリクエストボディを必要としません。

### レスポンス

<details>
  <summary><strong>サンプルコード</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "success"
    },
    "data": "Hello, World!"
}
```

</details>

| 名前 | タイプ | 説明 |
| --- | --- | --- |
| data | String | 関数実行結果 |

---

## 関数実行(POST)

POSTメソッドで関数を実行します。bodyを送信できます。

### リクエスト

```
POST /v1.0/functions/{functionName}/invoke
```

### リクエストパラメータ

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| functionName | URL | String | Y | 実行する関数名 |

### リクエストボディ

<details>
  <summary><strong>サンプルコード</strong></summary>

```json
{
    "key": "value"
}
```

</details>

| 名前 | タイプ  | 必須 | 説明 |
| --- |------| --- | --- |
| body | JSON | N | 関数に渡すbody |

### レスポンス

<details>
  <summary><strong>サンプルコード</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "success"
    },
    "data": "Function output"
}
```

</details>

| 名前 | タイプ | 説明 |
| --- | --- | --- |
| data | String | 関数実行結果 |

---

## バージョン一覧

関数のバージョン(パッケージ)一覧を照会します。

### リクエスト

```
GET /v1.0/functions/{functionName}/versions
```

### リクエストパラメータ

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| functionName | URL | String | Y | 関数名 |

### リクエストボディ

このAPIはリクエストボディを必要としません。

### レスポンス

<details>
  <summary><strong>サンプルコード</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "success"
    },
    "data": [
        {
            "versionId": 1,
            "versionName": "v1",
            "sourceFileName": "source.zip",
            "buildStatus": "SUCCEEDED",
            "createdAt": "2026-03-09T14:59:44Z",
            "isCurrent": true
        },
        {
            "versionId": 2,
            "versionName": "v2",
            "sourceFileName": "source-v2.zip",
            "buildStatus": "SUCCEEDED",
            "createdAt": "2026-03-10T18:45:00Z",
            "isCurrent": false
        }
    ]
}
```

</details>

| 名前 | タイプ | 説明 |
| --- | --- | --- |
| data | Array | バージョン一覧 |
| data[].versionId | Integer | バージョンID |
| data[].versionName | String | バージョン名 |
| data[].sourceFileName | String | ソースファイル名 |
| data[].buildStatus | String | ビルド状態(PENDING、RUNNING、SUCCEEDED、FAILED) |
| data[].createdAt | String | 作成時間(ISO 8601、例: 2026-03-09T14:59:44Z) |
| data[].isCurrent | Boolean | 現在のアクティブバージョンかどうか |

---

## バージョン切り替え

関数の現在のアクティブバージョンを変更します。

### リクエスト

```
PUT /v1.0/functions/{functionName}/versions
```

### リクエストパラメータ

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| functionName | URL | String | Y | 関数名 |

### リクエストボディ

<details>
  <summary><strong>サンプルコード</strong></summary>

```json
{
    "versionId": 12345
}
```

</details>

| 名前 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- |
| versionId | Integer | Y | 切り替えるバージョンID |

### レスポンス

<details>
  <summary><strong>サンプルコード</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "success"
    },
    "data": null
}
```

</details>

---

## バージョン削除

関数のバージョンを一括削除します。

現在のアクティブバージョンは削除できません。

### リクエスト

```
DELETE /v1.0/functions/{functionName}/versions
```

### リクエストパラメータ

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| functionName | URL | String | Y | 関数名 |

### リクエストボディ

<details>
  <summary><strong>サンプルコード</strong></summary>

```json
{
    "versionIds": [123, 456]
}
```

</details>

| 名前 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- |
| versionIds | Array | Y | 削除するバージョンID一覧 |

### レスポンス

<details>
  <summary><strong>サンプルコード</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "success"
    },
    "data": null
}
```

</details>

---

## トリガー一覧

関数のトリガー一覧を照会します。

### リクエスト

```
GET /v1.0/triggers/{functionName}
```

### リクエストパラメータ

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| functionName | URL | String | Y | 関数名 |

### リクエストボディ

このAPIはリクエストボディを必要としません。

### レスポンス

<details>
  <summary><strong>サンプルコード</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "success"
    },
    "data": [
        {
            "type": "http",
            "name": "http-trigger-1",
            "value": "https://example.com/trigger/path",
            "isActivated": true
        },
        {
            "type": "time",
            "name": "time-trigger-1",
            "value": "0 0 * * *",
            "isActivated": true
        }
    ]
}
```

</details>

| 名前 | タイプ | 説明 |
| --- | --- | --- |
| data | Array | トリガー一覧 |
| data[].type | String | トリガータイプ(http、time、api-gateway) |
| data[].name | String | トリガー名 |
| data[].value | String | トリガー値(URLまたはcron式) |
| data[].isActivated | Boolean | 有効状態 |

---

## タイムトリガー作成

関数にタイムトリガーを作成します。

### リクエスト

```
POST /v1.0/triggers/{functionName}/time
```

### リクエストパラメータ

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| functionName | URL | String | Y | 関数名 |

### リクエストボディ

<details>
  <summary><strong>サンプルコード</strong></summary>

```json
{
    "cron": "0 0 * * *"
}
```

</details>

| 名前 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- |
| cron | String | Y | cron式 |

### レスポンス

<details>
  <summary><strong>サンプルコード</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Successfully created the trigger."
    },
    "data": null
}
```

</details>

---

## タイムトリガー修正

タイムトリガーのcron式を修正します。

### リクエスト

```
PUT /v1.0/triggers/{functionName}/time
```

### リクエストパラメータ

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| functionName | URL | String | Y | 関数名 |

### リクエストボディ

<details>
  <summary><strong>サンプルコード</strong></summary>

```json
{
    "name": "time-trigger-1",
    "cron": "0 0 * * *"
}
```

</details>

| 名前 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- |
| name | String | Y | トリガー名 |
| cron | String | Y | cron式 |

### レスポンス

<details>
  <summary><strong>サンプルコード</strong></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Successfully modified the trigger."
    },
    "data": null
}
```

</details>

---

## タイムトリガー削除

タイムトリガーを一括削除します。

### リクエスト

```
DELETE /v1.0/triggers/{functionName}/time
```

### リクエストパラメータ

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| functionName | URL | String | Y | 関数名 |

### リクエストボディ

<details>
  <summary><strong>サンプルコード</strong></summary>

```json
{
  "names": ["time-trigger-1", "time-trigger-2"]
}
```

</details>

| 名前 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- |
| names | Array | Y | 削除するトリガー名一覧 |

### レスポンス

<details>
  <summary><strong>サンプルコード</strong></summary>

```json
{
  "header": {
    "isSuccessful": true,
    "resultCode": 0,
    "resultMessage": "Successfully deleted the trigger."
  },
  "data": null
}
```

</details>
