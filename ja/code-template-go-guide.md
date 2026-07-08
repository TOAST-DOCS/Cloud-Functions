<!-- pre-align:aligned sig=9b45d499c0b7 -->

<a id="compute-cloud-functions-code-template-guide-go"></a>
## Compute > Cloud Functions > コードテンプレートガイド > Go { #compute-cloud-functions-code-template-guide-go }

このドキュメントでは、NHN CloudのCloud FunctionsサービスでGoを使用して関数を開発する方法を詳しく説明します。

<a id="template-information"></a>
## テンプレート情報 { #template-information }
| 項目             | 値                                       |
|-----------------|------------------------------------------|
| **サポートバージョン**       | 1.22, 1.23, 1.24, 1.25 |
| **ファイル名**         | functions.go                             |
| **Entry Point** | Handler                                  |

<a id="basic-template"></a>
## 基本テンプレート { #basic-template }

<a id="hello-world-example"></a>
### Hello Worldの例 { #hello-world-example }
最も基本的な関数の形式です。

```go
package main

import (
	"fmt"
	"log"
	"net/http"

	"github.com/brianvoe/gofakeit/v6"
)

func Handler(w http.ResponseWriter, r *http.Request) {
	name := gofakeit.Name()
	email := gofakeit.Email()
	city := gofakeit.City()

	msg := fmt.Sprintf("Fake Name: %s\nEmail: %s\nCity: %s\n", name, email, city)
	_, err := w.Write([]byte(msg))
	if err != nil {
		log.Printf("Error writing response: %v", err)
	}
}
```

<a id="context-object"></a>
### Contextオブジェクト { #context-object }
Goの関数では、`http.ResponseWriter`と`*http.Request`を通じてHTTPのリクエストとレスポンスを処理します。

```go
package main

import (
	"encoding/json"
	"log"
	"net/http"
)

func Handler(w http.ResponseWriter, r *http.Request) {
	// HTTPリクエスト情報
	method := r.Method
	headers := r.Header
	query := r.URL.Query()

	// レスポンスデータの構成
	response := map[string]interface{}{
		"method":  method,
		"headers": headers,
		"query":   query,
		"path":    r.URL.Path,
	}

	// JSONレスポンスの設定
	w.Header().Set("Content-Type", "application/json")
	w.WriteHeader(http.StatusOK)

	if err := json.NewEncoder(w).Encode(response); err != nil {
		log.Printf("Error encoding response: %v", err)
	}
}
```

<a id="download-and-use-template-file"></a>
## テンプレートファイルのダウンロードと活用 { #download-and-use-template-file }

<a id="template-download"></a>
### テンプレートのダウンロード { #template-download }
Cloud Functionsが提供するGoテンプレートをダウンロードし、ローカル環境で開発できます。

**テンプレートのダウンロードリンク**: [go.zip](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/templates/go/go.zip)

<a id="template-file-structure"></a>
### テンプレートのファイル構造 { #template-file-structure }
ダウンロードしたテンプレートファイルの構造は次のとおりです。

```
go.zip
├── functions.go     # メイン関数ファイル
└── go.mod          # 依存関係管理ファイル
```

<a id="functionsgo"></a>
#### functions.go
基本的なfakeデータ生成関数が含まれています。
```go
package main

import (
	"fmt"
	"log"
	"net/http"

	"github.com/brianvoe/gofakeit/v6"
)

func Handler(w http.ResponseWriter, r *http.Request) {
	name := gofakeit.Name()
	email := gofakeit.Email()
	city := gofakeit.City()

	msg := fmt.Sprintf("Fake Name: %s\nEmail: %s\nCity: %s\n", name, email, city)
	_, err := w.Write([]byte(msg))
	if err != nil {
		log.Printf("Error writing response: %v", err)
	}
}
```

<a id="gomod"></a>
#### go.mod
```go
module example.com/ncf

go 1.22

require github.com/brianvoe/gofakeit/v6 v6.28.0
```

<a id="local-development-process"></a>
### ローカルでの開発プロセス { #local-development-process }

<a id="unzip"></a>
#### 1. 解凍
```bash
# 解凍
unzip go.zip -d my-function

# 作業ディレクトリへ移動
cd my-function
```

<a id="modify-function-codes"></a>
#### 2. 関数コードの修正
`functions.go`ファイルを、目的のロジックに合わせて修正します。

```go
// functions.go - 簡単な修正例
package main

import (
	"encoding/json"
	"net/http"
	"time"
)

func Handler(w http.ResponseWriter, r *http.Request) {
	// クエリパラメータから名前を取得
	name := r.URL.Query().Get("name")
	if name == "" {
		name = "World"
	}

	// POSTリクエストの場合、bodyからメッセージを取得
	var customMessage string
	if r.Method == "POST" {
		var data map[string]interface{}
		if err := json.NewDecoder(r.Body).Decode(&data); err == nil {
			if msg, ok := data["message"].(string); ok {
				customMessage = msg
			}
		}
	}

	// レスポンスデータの構成
	response := map[string]interface{}{
		"greeting":  "Hello, " + name + "!",
		"message":   customMessage,
		"method":    r.Method,
		"timestamp": time.Now().Format(time.RFC3339),
	}

	// JSONレスポンス
	w.Header().Set("Content-Type", "application/json")
	w.WriteHeader(http.StatusOK)

	if err := json.NewEncoder(w).Encode(response); err != nil {
		http.Error(w, "Internal Server Error", http.StatusInternalServerError)
	}
}
```

<a id="compress-into-a-zip-file"></a>
#### 3. ZIPファイルへ圧縮
修正したコードを、再度ZIPファイルへ圧縮します。

```bash
# Windows (PowerShell)
Compress-Archive -Path .\functions.go, .\go.mod -DestinationPath my-function.zip

# Windows (7-Zipを使用する場合)
7z a my-function.zip functions.go go.mod

# macOS/Linux
zip my-function.zip functions.go go.mod

# 全てのファイルを含める(追加ファイルがある場合)
zip -r my-function.zip . -x "*.git*" "go.sum" "test.go"
```

<a id="upload-from-cloud-functions-console"></a>
### Cloud Functionsコンソールでのアップロード { #upload-from-cloud-functions-console }
> 関数を作成または修正する際に、ユーザーのローカル環境にあるファイルをアップロードする場合に使用します。(コンソール利用ガイド参照)

<a id="cautions-for-upload"></a>
### アップロード時の注意事項 { #cautions-for-upload }

<a id="zip-file-structure"></a>
#### ZIPファイルの構造
- ZIPファイルのルートに、直接`.go`ファイルと`go.mod`を配置する必要があります。
- 不要なフォルダ構造は避けることを推奨します。

**正しい構造:**
```
my-function.zip
├── functions.go
├── go.mod
└── utils.go (追加ファイルがある場合)
```

**誤った構造:**
```
my-function.zip
└── my-function/
    ├── functions.go
    └── go.mod
```

<a id="file-size-limit"></a>
#### ファイルサイズの制限
- ZIPファイルのサイズは100MiB以下に制限されます。
- `go.sum`ファイルは含めないでください。

<a id="files-to-exclude"></a>
#### 除外するファイル
```bash
# .gitignoreと同様に、以下のファイルは除外
zip -r my-function.zip . -x \
  "go.sum" \
  ".git/*" \
  "*.log" \
  "test.go" \
  ".env" \
  "*.zip"
```

<a id="process-by-http-method"></a>
## HTTPメソッド別の処理 { #process-by-http-method }

<a id="process-get-request"></a>
### GETリクエストの処理 { #process-get-request }
```go
package main

import (
	"encoding/json"
	"net/http"
	"time"
)

func Handler(w http.ResponseWriter, r *http.Request) {
	if r.Method != "GET" {
		http.Error(w, "Method Not Allowed", http.StatusMethodNotAllowed)
		return
	}

	// クエリパラメータの取得
	name := r.URL.Query().Get("name")
	if name == "" {
		name = "World"
	}

	greeting := r.URL.Query().Get("greeting")
	if greeting == "" {
		greeting = "Hello"
	}

	response := map[string]interface{}{
		"message":   greeting + ", " + name + "!",
		"timestamp": time.Now().Format(time.RFC3339),
		"method":    "GET",
	}

	w.Header().Set("Content-Type", "application/json")
	json.NewEncoder(w).Encode(response)
}
```

<a id="process-post-request"></a>
### POSTリクエストの処理 { #process-post-request }
```go
package main

import (
	"encoding/json"
	"io"
	"net/http"
	"time"
)

type RequestBody struct {
	Name    string `json:"name"`
	Email   string `json:"email"`
	Message string `json:"message"`
}

func Handler(w http.ResponseWriter, r *http.Request) {
	if r.Method != "POST" {
		http.Error(w, "Method Not Allowed", http.StatusMethodNotAllowed)
		return
	}

	// request bodyの読み取り
	body, err := io.ReadAll(r.Body)
	if err != nil {
		http.Error(w, "Error reading body", http.StatusBadRequest)
		return
	}
	defer r.Body.Close()

	var requestBody RequestBody
	err = json.Unmarshal(body, &requestBody)
	if err != nil {
		http.Error(w, "Invalid JSON format", http.StatusBadRequest)
		return
	}

	// 必須フィールドの検証
	if requestBody.Name == "" {
		http.Error(w, "Missing required field: name", http.StatusBadRequest)
		return
	}

	// レスポンスデータの生成
	response := map[string]interface{}{
		"id":           generateID(),
		"name":         requestBody.Name,
		"email":        getOrDefault(requestBody.Email, "not provided"),
		"message":      getOrDefault(requestBody.Message, "No message"),
		"processed_at": time.Now().Format(time.RFC3339),
	}

	w.Header().Set("Content-Type", "application/json")
	w.WriteHeader(http.StatusCreated)
	json.NewEncoder(w).Encode(response)
}

func generateID() string {
	// 簡単なIDの生成(実際にはUUIDなどを使用)
	return time.Now().Format("20060102150405")
}

func getOrDefault(value, defaultValue string) string {
	if value == "" {
		return defaultValue
	}
	return value
}
```

<a id="manage-packages"></a>
## パッケージ管理 { #manage-packages }

<a id="write-gomod"></a>
### go.modの作成 { #write-gomod }
依存関係の管理には、`go.mod`ファイルを作成します。

```go
module example.com/myfunction

go 1.25

require (
    github.com/google/uuid v1.6.0
    github.com/gorilla/mux v1.8.1
)
```

<a id="example-of-external-api-call"></a>
### 外部API呼び出しの例 { #example-of-external-api-call }
```go
package main

import (
	"encoding/json"
	"fmt"
	"net/http"
	"time"
)

func Handler(w http.ResponseWriter, r *http.Request) {
	userID := r.URL.Query().Get("userId")
	if userID == "" {
		http.Error(w, "userId is required", http.StatusBadRequest)
		return
	}

	// 外部APIの呼び出し
	resp, err := http.Get(fmt.Sprintf("https://jsonplaceholder.typicode.com/users/%s", userID))
	if err != nil {
		http.Error(w, "External API error", http.StatusInternalServerError)
		return
	}
	defer resp.Body.Close()

	if resp.StatusCode == 404 {
		http.Error(w, "User not found", http.StatusNotFound)
		return
	}

	if resp.StatusCode != 200 {
		http.Error(w, "External API error", http.StatusInternalServerError)
		return
	}

	var userData map[string]interface{}
	if err := json.NewDecoder(resp.Body).Decode(&userData); err != nil {
		http.Error(w, "Failed to parse response", http.StatusInternalServerError)
		return
	}

	// レスポンスの構成
	response := map[string]interface{}{
		"user":       userData,
		"fetched_at": time.Now().Format(time.RFC3339),
	}

	w.Header().Set("Content-Type", "application/json")
	json.NewEncoder(w).Encode(response)
}
```

<a id="data-processing-example"></a>
### データ処理の例 { #data-processing-example }
```go
package main

import (
	"encoding/json"
	"io"
	"net/http"
	"sort"
	"strings"
	"time"

	"github.com/google/uuid"
)

type DataItem struct {
	Name string `json:"name"`
}

type ProcessedItem struct {
	ID             string `json:"id"`
	Name           string `json:"name"`
	ProcessedAt    string `json:"processed_at"`
	NormalizedName string `json:"normalized_name"`
}

func Handler(w http.ResponseWriter, r *http.Request) {
	body, err := io.ReadAll(r.Body)
	if err != nil {
		http.Error(w, "Error reading body", http.StatusBadRequest)
		return
	}
	defer r.Body.Close()

	var request struct {
		Data []DataItem `json:"data"`
	}

	if err := json.Unmarshal(body, &request); err != nil {
		http.Error(w, "Invalid JSON format", http.StatusBadRequest)
		return
	}

	if request.Data == nil {
		http.Error(w, "Data must be an array", http.StatusBadRequest)
		return
	}

	// データ処理
	var processedData []ProcessedItem
	for _, item := range request.Data {
		if item.Name != "" {
			processed := ProcessedItem{
				ID:             uuid.New().String()[:8],
				Name:           item.Name,
				ProcessedAt:    time.Now().Format(time.RFC3339),
				NormalizedName: normalizeName(item.Name),
			}
			processedData = append(processedData, processed)
		}
	}

	// データのソート
	sort.Slice(processedData, func(i, j int) bool {
		return processedData[i].NormalizedName < processedData[j].NormalizedName
	})

	// 有効なデータのみをフィルタリング
	validData := make([]ProcessedItem, 0)
	for _, item := range processedData {
		if item.NormalizedName != "" {
			validData = append(validData, item)
		}
	}

	response := map[string]interface{}{
		"total_processed": len(processedData),
		"valid_items":     len(validData),
		"data":           validData,
	}

	w.Header().Set("Content-Type", "application/json")
	json.NewEncoder(w).Encode(response)
}

func normalizeName(name string) string {
	if name == "" {
		return ""
	}
	return strings.Title(strings.TrimSpace(name))
}
```

<a id="configure-entry-point"></a>
## エントリーポイントの設定 { #configure-entry-point }

<a id="single-function"></a>
### 単一関数 { #single-function }
関数名をエントリーポイントとして使用します。

関数名: `Handler`
Entry Point: `Handler`

<a id="multiple-functions"></a>
### 複数関数 { #multiple-functions }
1つのファイルで複数の関数を定義できます。

```go
// handlers.go
package main

import (
	"encoding/json"
	"net/http"
)

func GetUser(w http.ResponseWriter, r *http.Request) {
	// ユーザー照会ロジック
	response := map[string]string{"message": "Get user"}
	w.Header().Set("Content-Type", "application/json")
	json.NewEncoder(w).Encode(response)
}

func CreateUser(w http.ResponseWriter, r *http.Request) {
	// ユーザー作成ロジック
	response := map[string]string{"message": "User created"}
	w.Header().Set("Content-Type", "application/json")
	w.WriteHeader(http.StatusCreated)
	json.NewEncoder(w).Encode(response)
}

func UpdateUser(w http.ResponseWriter, r *http.Request) {
	// ユーザー修正ロジック
	response := map[string]string{"message": "User updated"}
	w.Header().Set("Content-Type", "application/json")
	json.NewEncoder(w).Encode(response)
}
```

エントリーポイントの設定:
- `GetUser`
- `CreateUser`
- `UpdateUser`

<a id="caution"></a>
## 注意事項 { #caution }

<a id="go-version"></a>
### Goのバージョン { #go-version }
| バージョン | サポート終了日 | 利用終了日 |
|-----|-----------|-----------|
| 1.25 | - | - |
| 1.24 | - | - |
| 1.23 | 2026-02-21 | 2026-08-21 |
| 1.22 | 2026-01-28 | 2026-07-28 |

<a id="go-version-support-policy"></a>
#### Goバージョンサポートポリシー
Cloud Functionsは、Goの公式リリース方針に従います。Goは年2回(1月、7月)新しいメジャーバージョンをリリースしており、各バージョンは最新の2つのメジャーバージョンまでサポートされます。

**推奨事項**: セキュリティパッチやバグ修正を引き続き適用できるよう、最新のサポートバージョン(1.24または1.25)を使用することを推奨します。
