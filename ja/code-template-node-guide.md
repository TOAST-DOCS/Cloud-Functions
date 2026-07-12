<!-- pre-align:aligned sig=9f2d38073162 -->

<a id="compute-cloud-functions-code-template-guide-nodejs"></a>
## Compute > Cloud Functions > コードテンプレートガイド > Node.js { #compute-cloud-functions-code-template-guide-nodejs }

このドキュメントでは、NHN CloudのCloud FunctionsサービスでNode.jsを使用して関数を開発する方法を詳しく説明します。

<a id="template-information"></a>
## テンプレート情報 { #template-information }
| 項目       | 値                |
|-----------------|-----------------|
| **サポートバージョン** | 20.16.0, 22.5.0   |
| **ファイル名**    | hello.js           |
| **Entry Point** | hello            |

<a id="basic-template"></a>
## 基本テンプレート { #basic-template }
<a id="hello-world-example"></a>
### Hello Worldの例 { #hello-world-example }
最も基本的な関数の形式です。

```javascript
module.exports = async (context) => {
    return {
        status: 200,
        body: "Hello, World!\n"
    };
}
```

<a id="context-object"></a>
### Contextオブジェクト { #context-object }
関数に渡される`context`オブジェクトには、以下の情報が含まれます。

```javascript
module.exports = async (context) => {
    // HTTPリクエスト情報
    console.log('Method:', context.request.method);
    console.log('Headers:', context.request.headers);
    console.log('Query:', context.request.query);
    console.log('Body:', context.request.body);

    return {
        status: 200,
        body: JSON.stringify({
            message: "Context information logged"
        })
    };
}
```

<a id="download-and-use-template-file"></a>
## テンプレートファイルのダウンロードと活用 { #download-and-use-template-file }

<a id="template-download"></a>
### テンプレートのダウンロード { #template-download }
Cloud Functionsが提供するNode.jsテンプレートをダウンロードし、ローカル環境で開発できます。

**テンプレートのダウンロードリンク**: [nodejs.zip](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/templates/nodejs/nodejs.zip)

<a id="template-file-structure"></a>
### テンプレートのファイル構造 { #template-file-structure }
ダウンロードしたテンプレートファイルの構造は次のとおりです。

```
nodejs.zip
├── hello.js          # メイン関数ファイル
└── package.json      # 依存関係管理ファイル
```

<a id="template-file-structure-hellojs"></a>
#### hello.js
基本的なHello World関数が含まれています。
```javascript
module.exports = async (context) => {
    return {
        status: 200,
        body: "hello, world!\n"
    };
}
```

<a id="template-file-structure-packagejson"></a>
#### package.json
```json
{}
```

<a id="local-development-process"></a>
### ローカルでの開発プロセス { #local-development-process }

<a id="local-development-process-unzip"></a>
#### 1. 解凍
```bash
# 解凍
unzip nodejs.zip -d my-function

# 作業ディレクトリへ移動
cd my-function
```

<a id="local-development-process-modify-function-codes"></a>
#### 2. 関数コードの修正
`hello.js`ファイルを、目的のロジックに合わせて修正します。

```javascript
// hello.js - 簡単な修正例
module.exports = async (context) => {
    try {
        // クエリパラメータから名前を取得
        const { name = 'World' } = context.request.query;

        // POSTリクエストの場合、bodyからメッセージを取得
        let customMessage = '';
        if (context.request.method === 'POST' && context.request.body) {
            const body = context.request.body;
            customMessage = body.message || '';
        }

        return {
            status: 200,
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                greeting: `Hello, ${name}!`,
                message: customMessage,
                method: context.request.method,
                timestamp: new Date().toISOString()
            })
        };

    } catch (error) {
        return {
            status: 500,
            body: JSON.stringify({
                error: 'Internal Server Error',
                details: error.message
            })
        };
    }
}
```

<a id="local-development-process-compress-into-a-zip-file"></a>
#### 3. ZIPファイルへ圧縮
修正したコードを、再度ZIPファイルへ圧縮します。

```bash
# Windows (PowerShell)
Compress-Archive -Path .\hello.js, .\package.json -DestinationPath my-function.zip

# Windows (7-Zipを使用する場合)
7z a my-function.zip hello.js package.json

# macOS/Linux
zip my-function.zip hello.js package.json

# 全てのファイルを含める(追加ファイルがある場合)
zip -r my-function.zip . -x "*.git*" "node_modules/*" "test.js"
```

<a id="upload-from-cloud-functions-console"></a>
### Cloud Functionsコンソールでのアップロード { #upload-from-cloud-functions-console }
> 関数を作成または修正する際に、ユーザーのローカル環境にあるファイルをアップロードする場合に使用します。(コンソール利用ガイド参照)

<a id="cautions-for-upload"></a>
### アップロード時の注意事項 { #cautions-for-upload }

<a id="cautions-for-upload-zip-file-structure"></a>
#### ZIPファイルの構造
- ZIPファイルのルートに、直接`.js`ファイルと`package.json`を配置する必要があります。
- 不要なフォルダ構造は避けることを推奨します。

**正しい構造:**
```
my-function.zip
├── hello.js
├── package.json
└── utils.js (追加ファイルがある場合)
```

**誤った構造:**
```
my-function.zip
└── my-function/
    ├── hello.js
    └── package.json
```

<a id="cautions-for-upload-file-size-limit"></a>
#### ファイルサイズの制限
- ZIPファイルのサイズは100MiB以下に制限されます。
- `node_modules`フォルダは含めないでください。(依存関係は`package.json`で管理)

<a id="cautions-for-upload-files-to-exclude"></a>
#### 除外するファイル
```bash
# .gitignoreと同様に、以下のファイルは除外
zip -r my-function.zip . -x \
  "node_modules/*" \
  ".git/*" \
  "*.log" \
  "test.js" \
  ".env" \
  "*.zip"
```

<a id="process-by-http-method"></a>
## HTTPメソッド別の処理 { #process-by-http-method }

<a id="process-get-request"></a>
### GETリクエストの処理 { #process-get-request }
```javascript
module.exports = async (context) => {
    if (context.request.method !== 'GET') {
        return {
            status: 405,
            body: JSON.stringify({ error: 'Method Not Allowed' })
        };
    }

    // クエリパラメータの取得
    const { name = 'World', greeting = 'Hello' } = context.request.query;

    return {
        status: 200,
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({
            message: `${greeting}, ${name}!`,
            timestamp: new Date().toISOString()
        })
    };
}
```

<a id="process-post-request"></a>
### POSTリクエストの処理 { #process-post-request }
```javascript
module.exports = async (context) => {
    if (context.request.method !== 'POST') {
        return {
            status: 405,
            body: JSON.stringify({ error: 'Method Not Allowed' })
        };
    }

    // JSON形式のrequest bodyを使用
    const requestBody = context.request.body;

    // 必須フィールドの検証
    if (!requestBody.name) {
        return {
            status: 400,
            body: JSON.stringify({
                error: 'Missing required field: name'
            })
        };
    }

    const { name, email, message } = requestBody;

    // 処理ロジック
    const response = {
        id: Math.random().toString(36).substr(2, 9),
        name: name,
        email: email || 'not provided',
        message: message || 'No message',
        processed_at: new Date().toISOString()
    };

    return {
        status: 201,
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify(response)
    };

}
```

<a id="manage-packages"></a>
## パッケージ管理 { #manage-packages }

<a id="write-packagejson"></a>
### package.jsonの作成 { #write-packagejson }
依存関係の管理には、`package.json`ファイルを作成します。

```json
{
  "name": "my-cloud-function",
  "version": "1.0.0",
  "description": "NHN Cloud Functions example",
  "main": "index.js",
  "dependencies": {
    "axios": "^1.6.0",
    "lodash": "^4.17.21",
    "moment": "^2.29.4",
    "uuid": "^9.0.1"
  }
}
```

<a id="example-of-external-api-call"></a>
### 外部API呼び出しの例 { #example-of-external-api-call }
```javascript
const axios = require('axios');

module.exports = async (context) => {
    try {
        const { userId } = context.request.query;

        if (!userId) {
            return {
                status: 400,
                body: JSON.stringify({ error: 'userId is required' })
            };
        }

        // 外部APIの呼び出し
        const response = await axios.get(`https://jsonplaceholder.typicode.com/users/${userId}`);

        return {
            status: 200,
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                user: response.data,
                fetched_at: new Date().toISOString()
            })
        };

    } catch (error) {
        console.error('API call failed:', error.message);

        if (error.response && error.response.status === 404) {
            return {
                status: 404,
                body: JSON.stringify({ error: 'User not found' })
            };
        }

        return {
            status: 500,
            body: JSON.stringify({
                error: 'Internal server error',
                details: error.message
            })
        };
    }
}
```

<a id="data-processing-example"></a>
### データ処理の例 { #data-processing-example }
```javascript
const _ = require('lodash');
const moment = require('moment');
const { v4: uuidv4 } = require('uuid');

module.exports = async (context) => {
    try {
        const requestBody = context.request.body;
        const { data } = requestBody;

        if (!Array.isArray(data)) {
            return {
                status: 400,
                body: JSON.stringify({ error: 'Data must be an array' })
            };
        }

        // データ処理
        const processedData = data.map(item => ({
            id: uuidv4(),
            ...item,
            processed_at: moment().format('YYYY-MM-DD HH:mm:ss'),
            normalized_name: _.capitalize(_.trim(item.name))
        }));

        // データのソートとフィルタリング
        const sortedData = _.orderBy(processedData, ['normalized_name'], ['asc']);
        const validData = sortedData.filter(item => item.normalized_name);

        return {
            status: 200,
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                total_processed: processedData.length,
                valid_items: validData.length,
                data: validData
            })
        };

    } catch (error) {
        return {
            status: 400,
            body: JSON.stringify({
                error: 'Data processing failed',
                details: error.message
            })
        };
    }
}
```

<a id="use-environment-variables"></a>
## 環境変数の使用 { #use-environment-variables }
関数に登録した環境変数は、`process.env`でアクセスできます。環境変数は、関数の作成及び修正のコード作成段階で登録します。詳細については、コンソール使用ガイドをご参照ください。

```javascript
module.exports = async (context) => {
    // 環境変数の読み込み
    const dbHost = process.env.DB_HOST;
    const apiKey = process.env.API_KEY;

    if (!apiKey) {
        return {
            status: 500,
            body: JSON.stringify({ error: 'API_KEY is not set' })
        };
    }

    return {
        status: 200,
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ dbHost })
    };
}
```

!!! tip "ポイント"
    セキュリティ上、以下のキー・プレフィックスは環境変数として登録できません。

    共通(全てのランタイム)

    - シェル/subprocess: `PATH`、`IFS`、`HOME`、`BASH_ENV`、`ENV`、`SHELLOPTS`
    - ローダー/ライブラリ: `LD_PRELOAD`、`LD_LIBRARY_PATH`、`LD_AUDIT`
    - glibc動的ローディング: `GCONV_PATH`、`LOCPATH`、`HOSTALIASES`
    - プロキシ: `HTTP_PROXY`、`HTTPS_PROXY`、`ALL_PROXY`(小文字を含む)
    - TLSトラストストア: `SSL_CERT_FILE`、`SSL_CERT_DIR`、`REQUESTS_CA_BUNDLE`、`CURL_CA_BUNDLE`
    - プラットフォーム内部: `RUNTIME_PORT`、`USERFUNCVOL`、`WSGI_FRAMEWORK`、`SENTRY_DSN`、`SENTRY_RELEASE`、`TIMEOUT`、`BODY_PARSER_LIMIT`
    - プレフィックス: `LD_`、`DYLD_`、`KUBERNETES_`、`FISSION_`、`CORECLR_`、`COMPLUS_`、`DOTNET_`、`ASPNETCORE_`、`PYTHON`、`NODE_`、`RUBY`

    Node.js

    - `NODE_PATH`, `NODE_OPTIONS`, `NODE_EXTRA_CA_CERTS`

<a id="configure-entry-point"></a>
## エントリーポイントの設定 { #configure-entry-point }

<a id="single-function"></a>
### 単一関数 { #single-function }
ファイル名をエントリーポイントとして使用します。

ファイル名: `hello.js`
Entry Point: `hello`

<a id="multiple-functions"></a>
### 複数関数 { #multiple-functions }
1つのファイルで複数の関数をエクスポートできます。

```javascript
// users.js
module.exports.getUser = async (context) => {
    // ユーザー照会ロジック
    return {
        status: 200,
        body: JSON.stringify({ message: "Get user" })
    };
}

module.exports.createUser = async (context) => {
    // ユーザー作成ロジック
    return {
        status: 201,
        body: JSON.stringify({ message: "User created" })
    };
}

module.exports.updateUser = async (context) => {
    // ユーザー修正ロジック
    return {
        status: 200,
        body: JSON.stringify({ message: "User updated" })
    };
}
```

エントリーポイントの設定:
- `users.getUser`
- `users.createUser`
- `users.updateUser`

<a id="entry-point-restriction"></a>
### エントリーポイントの制限事項 { #entry-point-restriction }
- **サブディレクトリの指定不可**:ルートディレクトリのサブディレクトリにあるファイルは、エントリーポイントとして指定できません。

**正しいエントリーポイント:**
```
hello.js → hello
users.js → users.getUser
```

**誤ったエントリーポイント:**
```
lib/utils.js → lib.utils ❌
src/handlers.js → src.handlers ❌
modules/auth.js → modules.auth ❌
```

全ての関数ファイルは、ZIPファイルのルートレベルに配置する必要があります。

<a id="caution"></a>
## 注意事項 { #caution }

<a id="commonjs-vs-es-modules"></a>
### CommonJS vs ES Modules { #commonjs-vs-es-modules }
現在、Cloud FunctionsはCommonJS方式のみをサポートしています。

**使用可能(CommonJS):**
```javascript
const axios = require('axios');
module.exports = async (context) => {
    // 関数ロジック
};
```

**使用不可(ES Modules):**
```javascript
import axios from 'axios'; // ❌サポートしていません
export default async (context) => { // ❌サポートしていません
    // 関数ロジック
};
```

<a id="considerations-for-memory-and-execution-time"></a>
### メモリ及び実行時間に関する考慮事項 { #considerations-for-memory-and-execution-time }
- 関数は、限られたメモリと実行時間内で動作する必要があります。
- 大容量データを処理する際は、ストリーム処理を検討してください。
- 長時間実行されるタスクは、適切に分割してください。
