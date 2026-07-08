<!-- pre-align:aligned sig=1dc54302160b -->

<a id="compute-cloud-functions-console-user-guide"></a>
## Compute > Cloud Functions > コンソール使用ガイド { #compute-cloud-functions-console-user-guide }
この文書では、NHN Cloud Functionsコンソールで関数を作成し、管理する方法について説明します。

<a id="manage-functions"></a>
## 関数管理 { #manage-functions }
関数を作成、修正、削除、コピーできます。

<a id="create-functions"></a>
### 関数作成 { #create-functions }
関数設定を行いコードを作成してビルドした後、**作成**ボタンをクリックすると、最後にビルドしたパッケージで関数が作成されます。

![console-guide-07](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-11-25/console-guide-jp-01.png)
![console-guide-08](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-11-25/console-guide-jp-02.png)

<a id="create-functions-function-settings"></a>
#### 関数設定
<table class="it">
    <tr>
        <th>分類</th>
        <th>No.</th>
        <th>項目</th>
        <th>説明</th>
    </tr>
    <tr>
        <td rowspan="2">基本情報</td>
        <td>1.</td>
        <td>名前</td>
        <td>関数の名前<br> 重複した関数名は許可されません。<br> 関数エンドポイントURLとして使用されます。</td>
    </tr>
    <tr>
        <td>2.</td>
        <td>説明</td>
        <td>関数の説明、最大250文字</td>
    </tr>
    <tr>
        <td rowspan="7">関数設定</td>
        <td>3.</td>
        <td>エンドポイントURL</td>
        <td>関数呼び出しHTTPエンドポイントURL<br>関数名を入力すると自動的に変更されます。</td>
    </tr>
    <tr>
        <td>4.</td>
        <td>タイプ</td>
        <td>Pool Manager<br> New Deployment</td>
    </tr>
    <tr>
        <td>5.</td>
        <td>リソース</td>
        <td>関数動作環境リソース選択<br>Pool Managerタイプの場合、指定されたリソースのみ選択可能<br>New Deploymentタイプの場合、カスタムリソース入力可能</td>
    </tr>
    <tr>
        <td>6.</td>
        <td>実行制限時間</td>
        <td>関数実行時間を指定(Time out)。関数が実行され、指定された時間を超過すると関数は強制的に中断され、エラーでレスポンス</td>
    </tr>
    <tr>
        <td>7.</td>
        <td>(Pool Manager)同時実行設定</td>
        <td>関数の同時実行数を指定。同時に関数呼び出しが発生した場合、呼び出し回数分のインスタンスが同時に実行され、最大インスタンス数は制限されます。</td>
    </tr>
    <tr>
        <td>8.</td>
        <td>(New Deployment)最小インスタンス数</td>
        <td>維持する最小インスタンス数を指定。</td>
    </tr>
    <tr>
        <td>9.</td>
        <td>(New Deployment)最大インスタンス数</td>
        <td>自動的に拡張可能な最大インスタンス数指定。</td>
    </tr>
    <tr>
        <td>ログ設定</td>
        <td>10.</td>
        <td>ログサービス連動</td>
        <td>Log & Crash Searchサービスを使用して連携するかどうかを選択<br>関数のログはLog & Crash Searchサービスで直接確認可能です。<br>ログは10ライン単位で転送されます。</td>
    </tr>
</table>

> **[参考]** <br>
> **(Pool Manager)同時実行設定**


![console-guide-05](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-07-29/console-guide-05.png)

<br>

> **[参考]** <br>
> **(New Deployment)インスタンス数** - リソース使用量に応じて、指定されたインスタンス数だけ生成されない場合があります。

<br>


<a id="create-functions-code-writing"></a>
#### コード作成

![console-guide-16](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn-origin/prod_cloud_functions/2026-07-14/console-guide-jp-16.png)
![console-guide-17](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn-origin/prod_cloud_functions/2026-07-14/console-guide-jp-17.png)
![console-guide-18](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn-origin/prod_cloud_functions/2026-07-14/console-guide-jp-18.png)

<table class="it">
    <tr>
        <th>分類</th>
        <th>No.</th>
        <th>項目</th>
        <th>説明</th>
    </tr>
    <tr>
        <td rowspan="2">ソースコード</td>
        <td>1.</td>
        <td>ランタイム環境</td>
        <td>関数のランタイム環境を選択<br>ランタイムの選択リストには、サポートが終了したランタイム名の右側にサポート終了バッジが表示されます。<br>サポートが終了したランタイムを選択すると、下部に案内のコールアウトが表示されます。<br>使用中止となったランタイムはリストから除外され、選択できません。</td>
    </tr>
    <tr>
        <td>2.</td>
        <td>Entry Point</td>
        <td>関数のエントリーポイントを指定。例：関数名 <br>ランタイム環境のテンプレートに基づいて自動的に補完されます。<br>任意に修正する場合は、作成したソースコードのエントリーポイントと一致している必要があります。<br>誤って入力した場合、**ビルドでは確認できず**、**テスト実行時のログを通じて確認できます**。</td>
    </tr>
    <tr>
        <td rowspan="2">コード</td>
        <td>3.</td>
        <td>コードエディタ</td>
        <td>ランタイム環境のテンプレートファイルが読み込まれ、そのファイルを編集して関数を作成<br>コードエディタでのディレクトリ追加はできません。ディレクトリ構造を編集するには、テンプレートファイルをダウンロードしてローカルで直接修正した後、ZIPファイルアップロード方式を使用する必要があります。</td>
    </tr>
    <tr>
        <td>4.</td>
        <td>ユーザーローカル環境</td>
        <td>ユーザーローカル環境で関数コードを作成し、ZIPファイル形式でアップロード</td>
    </tr>
    <tr>
        <td rowspan="2">ビルド/テスト</td>
        <td>5.</td>
        <td>ビルド</td>
        <td>ユーザーが作成またはアップロードしたコードをビルドしてパッケージを作成します。<br>ビルドされたパッケージはテストに使用され、関数の作成/修正時に最後にビルドされたパッケージが関数バージョンとして連携されます。<br>ビルド/テストボタンは設定領域の下部にあるビルド/テスト領域に配置されており、ビルドが完了するとテストボタンが有効になります。</td>
    </tr>
    <tr>
        <td>6.</td>
        <td>テスト</td>
        <td><strong>テスト</strong>タブで関数に伝達するJSON Body(テストイベント)を作成し、下部の<strong>テスト</strong>ボタンで関数をテストします。(事前にビルドを行う必要があります。)<br>ログで関数の動作を確認できます。<br>GETメソッドで呼び出します。</td>
    </tr>
    <tr>
        <td>環境変数</td>
        <td>7.</td>
        <td>環境変数</td>
        <td>関数に設定する環境変数を登録/修正/削除し、キーで検索します。詳細については、以下の<strong>環境変数</strong>をご参照ください。</td>
    </tr>
    <tr>
        <td></td>
        <td>8.</td>
        <td>作成</td>
        <td>作成ボタンを利用して、関数設定と作成した関数に合わせて関数を作成</td>
    </tr>
</table>

<br>

> **[参考]** <br>ビルドボタンを通じて作成されたパッケージは、関数の作成/修正時に最後にビルドされたパッケージが関数バージョンとして連携されます。関数作成前に少なくとも1回以上ビルドを実行する必要があります。

> [参考] ビルド/テスト領域 <br>
> ビルドまたはテストを実行すると、ビルド/テスト領域でターミナルが展開され、ビルドログとテストログがタブで区切られて表示されます。折りたたみボタンで非表示にできます。

> [参考] ランタイムサポート状態 <br>
> サポートが終了(EOL)したランタイムを選択すると、下部に以下のようなコールアウトが表示されます。(日付はランタイムによって異なります。)<br>
> 「本ランタイムは2026年4月30日にサポートが終了しており、2026年10月31日に使用中止となる予定です。使用中止後は既存の関数の修正が不可能となるため、安定的な運用のために最新のランタイムを使用することを推奨します。」<br>
> 使用中止となったランタイムはリストから除外され、新しい関数の作成時に選択できません。ランタイムの状態に関する詳細については、概要の「ランタイムサポート状態」をご参照ください。

<a id="create-functions-environment-variable"></a>
#### 環境変数
関数が外部サービスの連携に必要な認証情報(APIキー、DB接続情報など)を、コードにハードコーディングせずに環境変数として分離して管理できます。

- 関数の作成及び修正のコード作成段階で、ソースコードの下にある環境変数管理領域にて環境変数を登録/修正/削除できます。
    - 関数につき最大100個まで登録でき、100個に達すると**変数の追加**ボタンが無効になります。
- 登録した環境変数は関数のランタイムに渡され、コード内で各言語の標準的な環境変数へのアクセス方式で値を使用できます。詳細については、コードテンプレートガイドをご参照ください。
- キーを基準に検索でき、一致する項目が登録順に表示されます。
- 環境変数の作成/修正/削除はビルドとは無関係に動作します。ただし、変更した値を実行中の関数に反映させるには、関数を再起動する必要があります。

##### 環境変数の制約事項
<table class="it">
    <tr>
        <th>項目</th>
        <th>制約</th>
    </tr>
    <tr>
        <td>個数</td>
        <td>関数につき最大100個</td>
    </tr>
    <tr>
        <td>キー</td>
        <td>英字またはアンダースコア(_)で始まり、英数字・アンダースコア(_)のみ使用可能<br>最大128文字、重複不可<br>(正規表現: <code>^[A-Za-z_][A-Za-z0-9_]*$</code>)</td>
    </tr>
    <tr>
        <td>値</td>
        <td>最大4,096文字</td>
    </tr>
</table>

> [参考] 予約キーの制限 <br>
> システム・ランタイムが特別に扱う一部のキーは、セキュリティ上の理由から環境変数として登録できません。以下はブロックされるキーの代表例であり、これ以外にもブロックされるキーが存在します。<br>
> - 共通: `PATH`、`HOME`、`LD_PRELOAD`、`LD_LIBRARY_PATH`<br>
> - 言語/ランタイム: `PYTHONPATH`(Python)、`NODE_OPTIONS`(Node.js)、`JAVA_TOOL_OPTIONS`・`CLASSPATH`(Java)<br>
> - プレフィックス `LD_`、`DYLD_`、`KUBERNETES_`、`FISSION_`で始まるキーは全てブロックされます。

<a id="modify-functions"></a>
### 関数修正 { #modify-functions }
既存の関数の設定とコードを修正するために、**修正**ボタンを クリックして 関数を修正します。

<a id="modify-functions-non-modifiable-item"></a>
#### 修正不可項目
- 名前、ランタイム環境
    - 該当項目を除く全ての項目を修正できます。
    
<a id="modify-functions-source-code"></a>
#### ソースコード
- コードエディタを使用する場合既存コードが読み込まれます。
- ユーザーローカル環境のZIPファイルをアップロードして関数を生成した場合、コードエディタに変更するとZIPファイルを表示せず、基本テンプレートコードが読み込まれます。

<a id="modify-functions-modify-function-restrictions-for-discontinued-runtimes"></a>
#### 使用中止ランタイムにおける関数修正の制限
- 使用中止となったランタイムを使用する関数は修正できません。
- 関数一覧で使用中止ランタイムを使用する関数を1件選択すると、**修正**ボタンが無効になり、関数の修正画面に進むことができません。
- 無効になった**修正**ボタンにマウスを合わせると、以下のようなツールチップが表示されます。
    - 「使用中止されたランタイムで関数を修正することはできません。最新のランタイムで関数を新しく作成してください。」
- サポート終了(使用中止前)のランタイムを使用する関数は修正可能であり、関数の作成時と同様に案内のコールアウトが表示されます。

<a id="delete-a-function"></a>
### 関数削除 { #delete-a-function }
![console-guide-14](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-11-25/console-guide-jp-06.png)
既存の関数を選択して削除します。一度に複数の関数を削除可能です。

<a id="copy-a-function"></a>
### 関数コピー { #copy-a-function }
![console-guide-13](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-11-25/console-guide-jp-07.png)
既存の関数と同一の関数をコピーします。名前は重複不可のため、コピー前に新しい名前を指定できます。
- トリガーはコピーされません。(HTTPトリガーはデフォルトで提供)
- バージョンは現在適用されているバージョンのみコピーされます。
- 元の関数に登録された環境変数はそのままコピーされます。

<a id="about-functions"></a>
## 関数情報 { #about-functions }
<a id="list-of-functions"></a>
### 関数リスト { #list-of-functions }
![console-guide-01](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-11-25/console-guide-jp-08.png)
- ユーザーが作成した関数リストを確認できます。
- ビルドの状態は 現在のバージョンのビルド状態を表示します。
- ランタイム情報の右側に、ランタイムのサポート状態(サポート終了/使用中止)のバッジが表示されます。

<a id="basic-information-of-functions"></a>
### 関数基本情報 { #basic-information-of-functions }
![console-guide-19](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn-origin/prod_cloud_functions/2026-07-14/console-guide-jp-19.png)
- 関数の基本情報を確認できます。
- ログ管理項目で **Log & Crash Search** ボタンをクリックし、Log & Crash Searchサービスへ移動してログを確認できます。

<a id="function-environment-variables"></a>
### 関数の環境変数 { #function-environment-variables }
関数の詳細情報の環境変数タブで、該当する関数に登録された環境変数の一覧を確認できます。

![console-guide-20](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn-origin/prod_cloud_functions/2026-07-14/console-guide-jp-20.png)

<table class="it">
    <tr>
        <th>No.</th>
        <th>項目</th>
        <th>説明</th>
    </tr>
    <tr>
        <td>1.</td>
        <td>検索</td>
        <td>環境変数のキーを基準に検索します。</td>
    </tr>
    <tr>
        <td>2.</td>
        <td>値のマスキング</td>
        <td>値は基本的にマスキングされて表示されます。<br>各行の目のアイコンをクリックして値を確認でき、複数の値を同時に確認できます。</td>
    </tr>
</table>

- 環境変数の登録/修正/削除は、関数の作成及び修正のコード作成段階で行うことができます。詳細については、関数の作成 > 環境変数をご参照ください。

<a id="function-versioning"></a>
### 関数バージョン管理 { #function-versioning }
![console-guide-15](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2026-01-27/console-guide-jp-15.png)
- 関数のバージョンを管理できます。
- 作成された全てのバージョンの履歴を確認し、以前のバージョンにロールバックできます。

<a id="function-versioning-versioning-overview"></a>
#### バージョン管理の概要
関数作成/修正画面で **ビルド** ボタンをクリックし、コードをビルドするとパッケージが作成されます。
- 関数の作成/修正時に最後にビルドしたパッケージが関数バージョンとして連携されます。
- 各バージョンは独立して管理され、テストしたバージョンをそのまま関数に適用できます。
- 関数作成時、少なくとも1回以上ビルドを実行する必要があります。

<a id="function-versioning-version-information"></a>
#### バージョン情報
<table class="it">
    <tr>
        <th>No.</th>
        <th>項目</th>
        <th>説明</th>
    </tr>
    <tr>
        <td>1.</td>
        <td>バージョンリスト</td>
        <td>関数の全てのバージョン履歴を確認できます。<br>バージョン名、ランタイム、ソースコード、ビルド状態などの情報が表示されます。<br>ランタイム情報の右側には、ランタイムのサポート状態(サポート終了/使用中止)バッジが表示されます。</td>
    </tr>
    <tr>
        <td>2.</td>
        <td>現在適用されているバージョン</td>
        <td>現在関数に適用されているバージョンが表示されます。</td>
    </tr>
    <tr>
        <td>3.</td>
        <td>ソースコードのダウンロード</td>
        <td>選択したバージョンのソースコードをZIPファイルでダウンロードできます。</td>
    </tr>
    <tr>
        <td>4.</td>
        <td>ログ確認</td>
        <td>選択したバージョンのビルドログを確認できます。<br>基本情報タブのビルドログ確認機能と同じです。</td>
    </tr>
</table>

<a id="function-versioning-deploy-versions"></a>
#### バージョン配布
- バージョンリストから配布するバージョンを選択し、**バージョン配布**ボタンをクリックすると、該当バージョンで関数が更新されます。
- 現在適用されているバージョンは選択できません。
- 複数のバージョンを同時に選択することはできず、1つのバージョンのみ配布可能です。
- ビルドに失敗したバージョンも配布できます。

> **[参考]**
> <br>バージョン配布時に確認ポップアップが表示され、成功するとバージョンリストが自動的に更新されます。
<a id="function-versioning-delete-versions"></a>
#### バージョン削除
- バージョンリストから削除するバージョンを選択し、**バージョン削除**ボタンをクリックすると、該当バージョンが削除されます。
- 現在適用されているバージョンは削除できません。
- 複数のバージョンを選択して一度に削除できます。
- バージョン削除時、該当バージョンのソースコードも一緒に削除されます。

> **[参考]**
> <br>バージョン削除時に確認ポップアップが表示され、削除されたバージョンは復元できません。
<a id="function-versioning-constraints"></a>
#### 制約事項
- 関数作成/修正画面でビルドしたパッケージは、関数の作成/修正をキャンセルすると関数バージョンとして連携されません。
- 関数削除時、該当関数と連携された全てのバージョンも一緒に削除されます。
- 関数コピー時、元の関数の現在適用されているバージョンのみコピーされ、全てのバージョン履歴はコピーされません。
- 関数作成時には複数のランタイムでビルドできますが、関数修正時には作成時に選択したランタイムでのみビルドできます。

<a id="manage-function-triggers"></a>
### 関数トリガー管理 { #manage-function-triggers }
- 関数を実行できるトリガーを管理できます。
- HTTPトリガーは関数の作成時にデフォルトで提供されます。
    - 有効化/無効化により、使用するかどうかを設定できます。
    
![console-guid-12](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-11-25/console-guide-jp-10.png)

- 指定されたHTTPトリガーを使用して作成した関数を実行できます。
    - 例: `https://{userdomain}/{関数名}`
    - Method : GET, POST

<a id="manage-function-triggers-createedit-triggers"></a>
#### トリガー作成/修正
- Timer
    - Value: Cron文字列で周期を入力します。
- API Gateway
    - API Gatewayサービスを利用してHTTPエンドポイントを追加できます。
        
<a id="manage-function-triggers-delete-triggers"></a>
#### トリガー削除
- 複数のトリガーを選択して削除できます。デフォルトのトリガーであるHTTPトリガーは削除できません。

<a id="monitor-functions"></a>
### 関数モニタリング { #monitor-functions }
![console-guide-06](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-11-25/console-guide-jp-11.png)
- 関数の使用量を確認できます。
- 関数呼び出し回数、呼び出し拒否数、エラー発生回数、成功率、関数実行時間指標を提供します。
- 設定された時間内の指標を提供します。
- ログが連携されている場合、Log & Crash Searchボタンを利用してLog & Crash Searchサービスに移動できます。

| 項目 | 説明 |
| --- | --- |
| 関数呼び出し回数 | 関数の総呼び出し回数(1秒あたり) |
| 呼び出し拒否数 | インスタンスが作成されずに関数呼び出しに失敗した回数（1秒あたり） |
| エラー発生回数 | レスポンスコードが200でないレスポンス回数(1秒あたり) |
| 成功率 | 関数の総呼び出し回数に対する成功した呼び出しの割合 |
| 関数実行時間 | 関数呼び出しに対するレスポンス時間 |

> **[参考]**
> <br>呼び出し拒否数は、Pool Managerタイプの場合のみ該当します。
> <br>関数呼び出し回数、呼び出し拒否回数、エラー発生回数は、指標のステップ範囲内における1秒あたりの平均回数として表示されます。例えば、ステップが15秒で、その期間に1件発生した場合、0.0667 (1 ÷ 15)と表示されます。
> <br>関数実行時間の平均は、全体の累積平均です。最大値は、該当期間中における最大の実行時間です。
