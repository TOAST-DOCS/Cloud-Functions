<!-- pre-align:aligned sig=f22611a38cd5 -->

<a id="cloud-functions-api-v10-guide"></a>
## Cloud Functions API v1.0 ガイド { #cloud-functions-api-v10-guide }

**Compute > Cloud Functions > API ガイド > API v1.0 ガイド**

<a id="cloud-functions-api-v10-common-information"></a>
## Cloud Functions API v1.0 共通情報 { #cloud-functions-api-v10-common-information }

<a id="api-endpoint"></a>
### API エンドポイント { #api-endpoint }

Cloud Functions APIを呼び出すためのリージョン別エンドポイントは以下のとおりです。

| リージョン | エンドポイント |
| --- |-----------------------------------------------------|
| 韓国(パンギョ)リージョン | https://kr1-cloud-functions.api.nhncloudservice.com |

<a id="authentication-and-authorization"></a>
### 認証及び権限 { #authentication-and-authorization }

Cloud Functionsは、API呼び出し時の認証/認可にUser Access Keyトークンを使用します。
User Access Keyトークンは、User Access Keyをもとに発行されるBearerタイプの一時的なアクセストークンです。
User Access Keyトークンの発行手順や使用方法の詳細は、[User Access Keyトークン](/nhncloud/ko/public-api/user-access-key-token)をご参照ください。

<a id="response-common-information"></a>
### レスポンス共通情報 { #response-common-information }

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

<a id="list-environments"></a>
## 環境一覧 { #list-environments }

使用可能なランタイム環境一覧を照会します。

<a id="request"></a>
### リクエスト { #request }

```
GET /v1.0/env/list
```

<a id="request-parameter"></a>
### リクエストパラメータ { #request-parameter }

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |

<a id="request-body"></a>
### リクエストボディ { #request-body }

このAPIはリクエストボディを必要としません。

<a id="response"></a>
### レスポンス { #response }

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

<a id="list-functions"></a>
## 関数一覧 { #list-functions }

関数一覧を照会します。

<a id="list-functions-request"></a>
### リクエスト { #list-functions-request }

```
GET /v1.0/functions
```

<a id="list-functions-request-parameter"></a>
### リクエストパラメータ { #list-functions-request-parameter }

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| page | Query | Integer | N | 現在のページ(デフォルト値: 0) |
| pageSize | Query | Integer | N | 1ページに表示する件数(デフォルト値: 5000) |

<a id="list-functions-request-body"></a>
### リクエストボディ { #list-functions-request-body }

このAPIはリクエストボディを必要としません。

<a id="list-functions-response"></a>
### レスポンス { #list-functions-response }

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

<a id="get-function"></a>
## 関数詳細照会 { #get-function }

関数の詳細情報とビルドログを同時に照会します。

<a id="get-function-request"></a>
### リクエスト { #get-function-request }

```
GET /v1.0/functions/{functionName}
```

<a id="get-function-request-parameter"></a>
### リクエストパラメータ { #get-function-request-parameter }

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| functionName | URL | String | Y | 関数名 |

<a id="get-function-request-body"></a>
### リクエストボディ { #get-function-request-body }

このAPIはリクエストボディを必要としません。

<a id="get-function-response"></a>
### レスポンス { #get-function-response }

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

<a id="create-function"></a>
## 関数作成 { #create-function }

新しい関数を作成します。multipart/form-dataでソースファイルをアップロードします。
runtimeは`{environment}-{version}`形式で入力する必要があります(例: NodeJS-22.5.0)。使用可能なランタイムは環境一覧照会APIで確認できます。

executorTypeによって必須パラメータが異なります。poolManagerの場合はrequestPerPodが、newDeploymentの場合はminInstanceとmaxInstanceが必須です。

<a id="create-function-request"></a>
### リクエスト { #create-function-request }

```
POST /v1.0/functions
```

<a id="create-function-request-parameter"></a>
### リクエストパラメータ { #create-function-request-parameter }

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |

<a id="create-function-request-body"></a>
### リクエストボディ { #create-function-request-body }

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

<a id="create-function-response"></a>
### レスポンス { #create-function-response }

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

<a id="modify-function"></a>
## 関数修正 { #modify-function }

関数を修正します。multipart/form-dataでソースファイルをアップロードできます。

ソースファイル(sourceFile)は任意項目です。ソースファイルを指定しない場合、既存のソースコードが維持されます。

<a id="modify-function-request"></a>
### リクエスト { #modify-function-request }

```
PUT /v1.0/functions/{functionName}
```

<a id="modify-function-request-parameter"></a>
### リクエストパラメータ { #modify-function-request-parameter }

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| functionName | URL | String | Y | 修正する関数名 |

<a id="modify-function-request-body"></a>
### リクエストボディ { #modify-function-request-body }

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

<a id="modify-function-response"></a>
### レスポンス { #modify-function-response }

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

<a id="delete-functions"></a>
## 関数削除 { #delete-functions }

関数を一括削除します。

<a id="delete-functions-request"></a>
### リクエスト { #delete-functions-request }

```
DELETE /v1.0/functions
```

<a id="delete-functions-request-parameter"></a>
### リクエストパラメータ { #delete-functions-request-parameter }

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |

<a id="delete-functions-request-body"></a>
### リクエストボディ { #delete-functions-request-body }

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

<a id="delete-functions-response"></a>
### レスポンス { #delete-functions-response }

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

<a id="execute-function-get"></a>
## 関数実行(GET) { #execute-function-get }

GETメソッドで関数を実行します。

<a id="execute-function-get-request"></a>
### リクエスト { #execute-function-get-request }

```
GET /v1.0/functions/{functionName}/invoke
```

<a id="execute-function-get-request-parameter"></a>
### リクエストパラメータ { #execute-function-get-request-parameter }

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| functionName | URL | String | Y | 実行する関数名 |

<a id="execute-function-get-request-body"></a>
### リクエストボディ { #execute-function-get-request-body }

このAPIはリクエストボディを必要としません。

<a id="execute-function-get-response"></a>
### レスポンス { #execute-function-get-response }

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

<a id="execute-function-post"></a>
## 関数実行(POST) { #execute-function-post }

POSTメソッドで関数を実行します。bodyを送信できます。

<a id="execute-function-post-request"></a>
### リクエスト { #execute-function-post-request }

```
POST /v1.0/functions/{functionName}/invoke
```

<a id="execute-function-post-request-parameter"></a>
### リクエストパラメータ { #execute-function-post-request-parameter }

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| functionName | URL | String | Y | 実行する関数名 |

<a id="execute-function-post-request-body"></a>
### リクエストボディ { #execute-function-post-request-body }

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

<a id="execute-function-post-response"></a>
### レスポンス { #execute-function-post-response }

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

<a id="list-versions"></a>
## バージョン一覧 { #list-versions }

関数のバージョン(パッケージ)一覧を照会します。

<a id="list-versions-request"></a>
### リクエスト { #list-versions-request }

```
GET /v1.0/functions/{functionName}/versions
```

<a id="list-versions-request-parameter"></a>
### リクエストパラメータ { #list-versions-request-parameter }

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| functionName | URL | String | Y | 関数名 |

<a id="list-versions-request-body"></a>
### リクエストボディ { #list-versions-request-body }

このAPIはリクエストボディを必要としません。

<a id="list-versions-response"></a>
### レスポンス { #list-versions-response }

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

<a id="switch-version"></a>
## バージョン切り替え { #switch-version }

関数の現在のアクティブバージョンを変更します。

<a id="switch-version-request"></a>
### リクエスト { #switch-version-request }

```
PUT /v1.0/functions/{functionName}/versions
```

<a id="switch-version-request-parameter"></a>
### リクエストパラメータ { #switch-version-request-parameter }

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| functionName | URL | String | Y | 関数名 |

<a id="switch-version-request-body"></a>
### リクエストボディ { #switch-version-request-body }

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

<a id="switch-version-response"></a>
### レスポンス { #switch-version-response }

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

<a id="delete-versions"></a>
## バージョン削除 { #delete-versions }

関数のバージョンを一括削除します。

現在のアクティブバージョンは削除できません。

<a id="delete-versions-request"></a>
### リクエスト { #delete-versions-request }

```
DELETE /v1.0/functions/{functionName}/versions
```

<a id="delete-versions-request-parameter"></a>
### リクエストパラメータ { #delete-versions-request-parameter }

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| functionName | URL | String | Y | 関数名 |

<a id="delete-versions-request-body"></a>
### リクエストボディ { #delete-versions-request-body }

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

<a id="delete-versions-response"></a>
### レスポンス { #delete-versions-response }

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

<a id="list-triggers"></a>
## トリガー一覧 { #list-triggers }

関数のトリガー一覧を照会します。

<a id="list-triggers-request"></a>
### リクエスト { #list-triggers-request }

```
GET /v1.0/triggers/{functionName}
```

<a id="list-triggers-request-parameter"></a>
### リクエストパラメータ { #list-triggers-request-parameter }

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| functionName | URL | String | Y | 関数名 |

<a id="list-triggers-request-body"></a>
### リクエストボディ { #list-triggers-request-body }

このAPIはリクエストボディを必要としません。

<a id="list-triggers-response"></a>
### レスポンス { #list-triggers-response }

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

<a id="create-time-trigger"></a>
## タイムトリガー作成 { #create-time-trigger }

関数にタイムトリガーを作成します。

<a id="create-time-trigger-request"></a>
### リクエスト { #create-time-trigger-request }

```
POST /v1.0/triggers/{functionName}/time
```

<a id="create-time-trigger-request-parameter"></a>
### リクエストパラメータ { #create-time-trigger-request-parameter }

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| functionName | URL | String | Y | 関数名 |

<a id="create-time-trigger-request-body"></a>
### リクエストボディ { #create-time-trigger-request-body }

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

<a id="create-time-trigger-response"></a>
### レスポンス { #create-time-trigger-response }

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

<a id="modify-time-trigger"></a>
## タイムトリガー修正 { #modify-time-trigger }

タイムトリガーのcron式を修正します。

<a id="modify-time-trigger-request"></a>
### リクエスト { #modify-time-trigger-request }

```
PUT /v1.0/triggers/{functionName}/time
```

<a id="modify-time-trigger-request-parameter"></a>
### リクエストパラメータ { #modify-time-trigger-request-parameter }

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| functionName | URL | String | Y | 関数名 |

<a id="modify-time-trigger-request-body"></a>
### リクエストボディ { #modify-time-trigger-request-body }

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

<a id="modify-time-trigger-response"></a>
### レスポンス { #modify-time-trigger-response }

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

<a id="delete-time-triggers"></a>
## タイムトリガー削除 { #delete-time-triggers }

タイムトリガーを一括削除します。

<a id="delete-time-triggers-request"></a>
### リクエスト { #delete-time-triggers-request }

```
DELETE /v1.0/triggers/{functionName}/time
```

<a id="delete-time-triggers-request-parameter"></a>
### リクエストパラメータ { #delete-time-triggers-request-parameter }

| 名前 | 区分 | タイプ | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-NHN-appkey | Header | String | Y | Appkey |
| X-NHN-authorization | Header | String | Y | ユーザートークン(Bearer {token}) |
| functionName | URL | String | Y | 関数名 |

<a id="delete-time-triggers-request-body"></a>
### リクエストボディ { #delete-time-triggers-request-body }

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

<a id="delete-time-triggers-response"></a>
### レスポンス { #delete-time-triggers-response }

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
