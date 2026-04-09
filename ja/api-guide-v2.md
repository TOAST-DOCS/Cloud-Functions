## Network > DNS Plus > API v2.0 ガイド

DNS PlusサービスのAPI v2.0について説明します。


## API共通情報

### 認証及び権限

DNS Plus API v2.0は、APIの呼び出し及び認証のためにAppkeyとUser Access Keyトークンをサポートします。

Appkeyは、NHN Cloudの各サービスに発行される固有の認証キーであり、APIリクエスト時のサービス識別と有効性検証に使用されます。User Access Keyトークンは、User Access Keyを基に発行されるBearerタイプの一時的なアクセストークンです。
各認証方法の確認及び使用方法の詳細については、それぞれ[Appkey](/nhncloud/ja/public-api/appkey/)と[User Access Keyトークン](/nhncloud/ja/public-api/user-access-key-token)をご参照ください。

発行されたトークンは、リクエストのHeaderに含める必要があります。

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| X-NHN-AUTHORIZATION | Header | String | O | Public APIで発行されたBearerタイプのトークン |

### レスポンス共通情報

- 全てのAPIリクエストに対し「200 OK」で応答します。詳細なレスポンス結果は、レスポンス本文のヘッダをご参照ください。

[成功時のレスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    }
}
```

[失敗時のレスポンス本文]

```
{
    "header": {
        "isSuccessful": false,
        "resultCode": 4010001,
        "resultMessage": "Invalid appKey. "
    }
}
```


## DNS Zone API

### DNS Zoneの照会

- DNS Zone一覧を取得します。

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| GET | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/zones |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。

```
curl -X GET 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/zones'
```

[オプション]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| zoneIdList | List | 最大3,000個 | 任意 |  | DNS Zone ID一覧 |
| zoneStatusList | List | CREATING, <br>DELETING, <br>DELETING_FAIL, <br> USE | 任意 | | DNS Zoneのステータス一覧 <br>(CREATING: 作成中、<br>DELETING: 削除中、<br>DELETING_FAIL: 削除失敗、<br>USE: 使用中) |
| searchZoneName | String |  | 任意 |  | 検索するDNS Zone名 |
| engineId | String | | 任意 |  | DNSサーバーID |
| page | int | 最小1 | 任意 | 1 | ページ番号 |
| limit | int | 最小1、最大3,000 | 任意 | 50 | 取得数 |
| sortDirection | String | DESC, ASC | 任意 | DESC | ソート方向(DESC: 降順、ASC: 昇順) |
| sortKey | String | CREATED_AT, <br>UPDATED_AT, <br>ZONE_NAME, <br>ZONE_STATUS, <br>RECORDSET_COUNT | 任意 | CREATED_AT | ソート対象 <br>(CREATED_AT: 作成日時、<br>UPDATED_AT: 更新日時、<br>ZONE_NAME: DNS Zone名、<br>ZONE_STATUS: DNS Zoneステータス、<br>RECORDSET_COUNT: レコードセット数) |

#### レスポンス

[レスポンス本文]

```
{
    "header": {
        // 省略
    },
    "totalCount": 1,
    "zoneList": [
        {
            "engineId": "e13a1bcf0aa8e07f6a4fae94ed869c39",
            "zoneId": "bff20a9a-24cf-4670-8b34-007622ec010e",
            "zoneName": "test.dnsplus.com.",
            "zoneStatus": "USE",
            "description": "テスト",
            "createdAt": "2019-06-04T12:32:50.000+09:00",
            "updatedAt": "2019-06-04T12:32:50.000+09:00",
            "recordsetCount": 2
        }
    ]
}
```

[フィールド]

| 名前 | タイプ | 説明 |
|---|---|---|
| totalCount | long | DNS Zoneの総数 |
| zoneList | List | DNS Zone一覧 |
| zoneList[0].engineId | String | DNSサーバーID |
| zoneList[0].zoneId | String | DNS Zone ID |
| zoneList[0].zoneName | String | DNS Zone名 |
| zoneList[0].zoneStatus | String | DNS Zoneステータス |
| zoneList[0].description | String | 説明 |
| zoneList[0].createdAt | DateTime | 作成日時 |
| zoneList[0].updatedAt | DateTime | 更新日時 |
| zoneList[0].recordsetCount | long | レコードセット数 |


### DNS Zoneの作成

- DNS Zoneを作成します。
- **DNS Zone名**は、DNSサーバー内で一意である必要があります。
- 同一の**DNS Zone名**は、DNSサーバーの数だけ作成可能です。DNSサーバーは3台です。

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| POST | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/zones |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。

```
curl -X POST 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/zones' \
-H 'Content-Type: application/json' \
--data '{ "zone": { "zoneName": "test.dnsplus.com.", "description": "test" }}'
```

[フィールド]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| zone | Object |  | 必須 |  | DNS Zone |
| zone.zoneName | String | 最大254文字<br>半角英小文字、数字、'.'、'-'、'_'<br>末尾の文字は'.' | 必須 |  | 作成するDNS Zone名。<br>ドメインを[FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)で入力 |
| zone.description | String | 最大255文字 | 任意 |  | DNS Zoneの説明 |

#### レスポンス

[レスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "zone": {
        "engineId": "e13a1bcf0aa8e07f6a4fae94ed869c39",
        "zoneId": "bff20a9a-24cf-4670-8b34-007622ec010e",
        "zoneName": "test.dnsplus.com.",
        "zoneStatus": "USE",
        "description": "test",
        "createdAt": "2019-06-04T12:32:50.000+09:00",
        "updatedAt": "2019-06-04T12:32:50.000+09:00",
        "recordsetCount": 2
    }
}
```


### DNS Zoneの変更

- DNS Zoneを変更します。

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| PUT | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/zones/{zoneId} |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。
- {zoneId}はDNS Zone IDであり、[DNS Zoneの照会](./api-guide/#dns-zone)で確認できます。

```
curl -X PUT 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/zones/{zoneId}' \
-H 'Content-Type: application/json' \
--data '{ "zone": { "description": "test" }}'
```

[フィールド]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| zone | Object |  | 必須 |  | DNS Zone |
| zone.description | String | 最大255文字 | 任意 |  | DNS Zoneの説明 |

#### レスポンス

[レスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "zone": {
        "engineId": "e13a1bcf0aa8e07f6a4fae94ed869c39",
        "zoneId": "bff20a9a-24cf-4670-8b34-007622ec010e",
        "zoneName": "test.dnsplus.com.",
        "zoneStatus": "USE",
        "description": "test",
        "createdAt": "2019-06-04T12:32:50.000+09:00",
        "updatedAt": "2019-06-04T12:42:00.000+09:00",
        "recordsetCount": 2
    }
}
```


### DNS Zoneの削除(非同期)

- 複数のDNS Zoneを一括削除します。DNS Zone内のレコードセットも同時に削除されます。
- 実際のデータ削除は非同期で処理されます。

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| DELETE | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/zones/async |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。
- DNS Zone IDは[DNS Zoneの照会](./api-guide/#dns-zone)で確認できます。

```
curl -X DELETE 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/zones/async?
zoneIdList=bff20a9a-24cf-4670-8b34-007622ec010e,52bc0031-37eb-4b82-b4d7-eaab24188dc4'
```

[フィールド]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| zoneIdList | List | 最小1個、最大3,000個 | 必須 |  | DNS Zone ID一覧 |

#### レスポンス

[レスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    }
}
```


## レコードセットAPI

### レコードセットの照会

- レコードセット一覧を取得します。

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| GET | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/zones/{zoneId}/recordsets |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。
- {zoneId}はDNS Zone IDであり、[DNS Zoneの照会](./api-guide/#dns-zone)で確認できます。

```
curl -X GET 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/zones/{zoneId}/recordsets'
```

[オプション]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| recordsetIdList | List | 最大3,000個 | 任意 |  | レコードセットID一覧 |
| recordsetTypeList | List | A, AAAA, CAA, CNAME, MX, <br>NAPTR, PTR, TXT, SRV, NS, SOA | 任意 | | レコードセットのタイプ一覧 |
| searchRecordsetName | String |  | 任意 |  | 検索するレコードセット名 |
| page | int | 最小1 | 任意 | 1 | ページ番号 |
| limit | int | 最小1、最大3,000 | 任意 | 50 | 取得数 |
| sortDirection | String | DESC, ASC | 任意 | DESC | ソート方向(DESC: 降順、ASC: 昇順) |
| sortKey | String | CREATED_AT, <br>UPDATED_AT, <br>RECORDSET_NAME, <br>RECORDSET_TYPE, <br>RECORDSET_TTL | 任意 | CREATED_AT | ソート対象 <br>(CREATED_AT: 作成日時、<br>UPDATED_AT: 更新日時、<br>RECORDSET_NAME: レコードセット名、<br>RECORDSET_TYPE: レコードセットタイプ、<br>RECORDSET_TTL: TTL(秒)) |

#### レスポンス

[レスポンス本文]

```
{
    "header": {
        // 省略
    },
    "totalCount": 2,
    "recordsetList": [
        {
            "recordsetId": "9e92b547-e2c1-4398-8904-552e0ca465e2",
            "recordsetName": "test.dnsplus.com.",
            "recordsetType": "SOA",
            "recordsetTtl": 1500,
            "recordsetStatus": "USE",
            "createdAt": "2019-06-04T12:32:50.000+09:00",
            "updatedAt": "2019-06-04T12:32:50.000+09:00",
            "recordList": [
                {
                    "recordDisabled": false,
                    "recordContent": "ns1.dnsplus.com. hostmaster.dnsplus.com. 2019060401 10800 3600 604800 1200",
                    // 省略: レコードセットのタイプによって異なる
                }
            ]
        },
        {
            "recordsetId": "edb9512b-6e62-409c-99ee-092d340e0adf",
            "recordsetName": "test.dnsplus.com.",
            "recordsetType": "NS",
            "recordsetTtl": 1500,
            "recordsetStatus": "USE",
            "createdAt": "2019-06-04T12:32:50.000+09:00",
            "updatedAt": "2019-06-04T12:32:50.000+09:00",
            "recordList": [
                {
                    "recordDisabled": false,
                    "recordContent": "ns.toastdns-jin.com.",
                    // 省略: レコードセットのタイプによって異なる
                },
                {
                    "recordDisabled": false,
                    "recordContent": "ns.toastdns-jin.net.",
                    // 省略: レコードセットのタイプによって異なる
                }
            ]
        }
    ]
}
```

[フィールド]

| 名前 | タイプ | 説明 |
|---|---|---|
| totalCount | long | レコードセットの総数 |
| recordsetList | List | レコードセット一覧 |
| recordsetList[0].recordsetId | String | レコードセットID |
| recordsetList[0].recordsetName | String | レコードセット名 |
| recordsetList[0].recordsetType | String | レコードセットタイプ |
| recordsetList[0].recordsetTtl | int | ネームサーバーでのレコードセット情報の更新間隔 |
| recordsetList[0].recordsetStatus | String | レコードセットのステータス |
| recordsetList[0].createdAt | DateTime | 作成日時 |
| recordsetList[0].updatedAt | DateTime | 更新日時 |
| recordsetList[0].recordList | List | レコード一覧 |
| recordsetList[0].recordList[0].recordDisabled | boolean | レコードの無効状態 |
| recordsetList[0].recordList[0].recordContent | String | レコード値。レコードセットのタイプに応じた詳細フィールドを1行で表示した内容 |


### レコードセットの作成

- レコードセットを作成します。
- **レコードセットのタイプ**は、A、AAAA、CAA、CNAME、MX、NAPTR、PTR、TXT、SRV、NS、SOAをサポートしています。
- SOAレコードセットは作成、変更、削除できません。また、NSレコードセットは**DNS Zone名**で作成、変更、削除できません。
- レコードセット内のレコード一覧の長さは最大512バイトです。
- TXTレコードセットは最大4096バイトです。
- DNS Zoneごとに最大5,000個のレコードセットを作成できます。
- レコードセットの作成数には上限があります。上限の引き上げをご希望の場合は、別途お問い合わせください。[お問い合わせ](https://www.nhncloud.com/jp/support/inquiry)

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| POST | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/zones/{zoneId}/recordsets |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。
- {zoneId}はDNS Zone IDであり、[DNS Zoneの照会](./api-guide/#dns-zone)で確認できます。
- レコード値は必須です。入力方法として、recordset.recordList[0].recordContentフィールドまたは詳細フィールドのいずれかを指定できます。
- recordContentフィールドは、半角スペースを区切り文字として詳細フィールドを1行で記述した内容です。詳細フィールドについては[レコードセットのタイプに応じた詳細フィールド]で確認できます。
- 詳細フィールドとrecordContentフィールドを同時に入力した場合、recordContentフィールドが優先して適用されます。

```
curl -X POST 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/zones/{zoneId}/recordsets' \
-H 'Content-Type: application/json' \
--data '{ "recordset": { "recordsetName": "sub.test.dnsplus.com.", "recordsetType": "A", "recordsetTtl": 86400, "recordList": [{ "recordDisabled": false, "recordContent": "1.1.1.1" }] }}'
```

[フィールド]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| recordset | Object |  | 必須 |  | レコードセット |
| recordset.recordsetName | String | 最大254文字<br>半角英小文字、数字、'.'、'-'、'_'<br>(DNS Zone名を含む) | 必須 |  | 作成するレコードセット名。<br>ドメインを[FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)で入力 |
| recordset.recordsetType | String | A, AAAA, CAA, CNAME, MX, <br>NAPTR, PTR, TXT, SRV, NS | 必須 |  | レコードセットのタイプ |
| recordset.recordsetTtl | int | 最小10、最大2147483647 | 必須 |  | ネームサーバーでのレコードセット情報の更新間隔 |
| recordset.recordList | List |  | 必須 |  | レコード一覧 |
| recordset.recordList[0].recordDisabled | boolean |  | 任意 | false | レコードの無効状態 |
| recordset.recordList[0].recordContent | String |  | 必須 |  | レコードセットのタイプに応じた詳細フィールドを1行で記述した内容 |

[レコードセットのタイプに応じた詳細フィールド]

- Aレコードセット
    - 複数のレコードを入力できます。
    - 1つのドメイン名に対して、複数のIPv4アドレスを登録できます。

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| recordset.recordList[0].ipV4 | String |  | 必須 |  | IPv4形式のアドレス |


- AAAAレコードセット
    - 複数のレコードを入力できます。
    - 1つのドメイン名に対して、複数のIPv6アドレスを登録できます。

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| recordset.recordList[0].ipV6 | String |  | 必須 |  | IPv6形式のアドレス |


- CAAレコードセット
    - 複数のレコードを入力できます。
    - ドメインに発行が許可された認証局(CA)を指定することで、許可されていない認証局(CA)による証明書の発行を防ぐことができます。
    - issueタグは、ドメインまたはサブドメインに対する証明書の発行権限を指定します。
    - issuewildタグは、ドメインまたはサブドメインに対するワイルドカード証明書の発行権限を指定します。
        - issueタグとissuewildタグの設定方法は同じです。
        - 証明書の発行許可: 認証局のアドレスを入力します。追加設定が必要な場合はセミコロン(;)で区切り、「名前=値」のペアで指定します。
        - 証明書の発行禁止: セミコロン(;)のみを入力します。
    - iodefタグは、認証局(CA)が不正なリクエストを受け取った際に、設定されたメールアドレスまたはURL宛てに通知を送るための設定です。
        - メールの入力形式: "mailto:*email-address*"
        - URLアドレスの入力形式: "http://*URL*" または "https://*URL*"
    - カスタムタグ(ユーザー指定タグ)は、認証局(CA)がRFC標準以外の追加機能をサポートしている場合に使用する設定です。

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| recordset.recordList[0].flags | int | 0または128 | 必須 |  | 定義済みタグの場合は0、<br>カスタムタグの場合は128 |
| recordset.recordList[0].tag | String | TAG_ISSUE, <br>TAG_ISSUEWILD, <br>TAG_IODEF, <br>ユーザー指定タグ最大15 | 必須 |  | TAG_ISSUE: issueタグ、<br>TAG_ISSUEWILD: issuewildタグ、<br>TAG_IODEF: iodefタグ、<br>カスタムタグ |
| recordset.recordList[0].stringValue | String | 最大512文字(引用符を含む) | 必須 |  | タグに応じた値 |


- CNAMEレコードセット
    - 1つのレコードのみ入力できます。
    - レコードセット名を正規名(Canonical Name)のエイリアスとして定義します。
    - 同一のレコードセット名に対して、他のタイプのレコードセットが存在しない場合にのみ、CNAMEレコードセットを作成できます。

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| recordset.recordList[0].domainName | String | 最大255文字 | 必須 |  | ドメインを[FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)で入力 |


- MXレコードセット
    - 複数のレコードを入力できます。
    - ドメインに対するメールサーバーを指定します。

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| recordset.recordList[0].priority | int | 最小0、最大65535 | 必須 |  | 優先順位 |
| recordset.recordList[0].domainName | String | 最大255文字 | 必須 |  | ドメインを[FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)で入力 |


- NAPTRレコードセット
    - 複数のレコードを入力できます。
    - DDDS(Dynamic Delegation Discovery System)アプリケーションで、ある値を別の値に変換したり置き換えたりするために使用します。
    - 順序(order)項目は、DDDSアプリケーションがレコードを評価する順序を指定します。
    - 優先順位(preference)項目は、2つ以上のレコードで順序(order)が同じ場合、優先して評価する順序を指定します。
    - 区分(flags)項目は、DDDSアプリケーションの設定として空文字、'S'、'A'、'U'、'P'を指定できます。その他の文字は予約されています。
    - サービス(service)項目はDDDSアプリケーションの設定です。詳細な定義は各RFCドキュメントで確認できます。
        - URI DDDSアプリケーション [RFC 3404#section-4.4](https://tools.ietf.org/html/rfc3404#section-4.4)
        - S-NAPTR DDDSアプリケーション [RFC 3958#section-6.5](https://tools.ietf.org/html/rfc3958#section-6.5)
        - U-NAPTR DDDSアプリケーション [RFC 4848#section-4.5](https://tools.ietf.org/html/rfc4848#section-4.5)
    - 正規表現(regexp)項目は、DDDSアプリケーションで入力値を出力値に変換するために使用します。詳細な定義は[RFC 3402#section-3.2](https://tools.ietf.org/html/rfc3402#section-3.2)で確認できます。
    - 代替値(replacement)項目は、DDDSアプリケーションがDNSクエリを送信するドメイン名で入力値を置き換えます。正規表現項目を設定する場合は'.'を指定します。

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| recordset.recordList[0].order | int | 最小0、最大65535 | 必須 |  | 順序 |
| recordset.recordList[0].preference | int | 最小0、最大65535 | 必須 |  | 優先順位 |
| recordset.recordList[0].flags | String | 最大3文字(引用符を含む) | 必須 |  | 区分 |
| recordset.recordList[0].service | String | 最大257文字(引用符を含む) | 必須 |  | サービス |
| recordset.recordList[0].regexp | String | 最大257文字(引用符を含む) | 必須 |  | 正規表現 |
| recordset.recordList[0].replacement | String | 最大255文字 | 必須 |  | 代替値として'.'またはドメインを[FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)で入力 |


- PTRレコードセット
    - 複数のレコードを入力できます。
    - IPアドレスを利用してドメイン情報を照会する逆引き機能です。ISP(インターネットプロバイダー)に依頼して設定する必要があります。
    - IPアドレスは逆順にしてレコードセット名に入力する必要があります。例: 127.0.0.1 -> 1.0.0.127.in-addr.arpa

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| recordset.recordList[0].domainName | String | 最大255文字 | 必須 |  | ドメインを[FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)で入力 |


- TXTレコードセット
    - 複数のレコードを入力できます。
    - レコードセット名に対するテキストの値を入力します。
    - TXTレコードセットとして、SPFレコードを作成できます。
        - SPFはメールの送信元ドメイン認証方式であり、受信側のメールサーバーが「送信元メールサーバー」と「メールアドレス」が一致しているかを確認する機能です。
        - 以下のような形式で入力します。詳細な定義は[RFC4408](https://tools.ietf.org/html/rfc4408)で確認できます。
        - 修飾子のデフォルト値は'+'です。メカニズムに応じてIPアドレスやドメイン名などを追加で指定します。
            - 形式: "v=spf1 {修飾子}{メカニズム}{内容} {モディファイア}={内容}"
            - 修飾子: '+'(Pass)、'-'(Fail)、'~'(Soft Fail)、'?'(Neutral)
            - メカニズム: all, include, a, mx, ptr, ip4, ip6, exists
            - モディファイア: redirect, exp, カスタム
            - 例:
                - "v=spf1 mx -all"
                - "v=spf1 ip4:192.168.0.1/16 -all"
                - "v=spf1 a:toast.com -all"
                - "v=spf1 redirect=toast.com"

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---------------------|---|---|---|
| recordset.recordList[0].stringValue | String | 最大4096バイト(引用符を含む) | 必須 |  | テキストの内容 |


- SRVレコードセット
    - 複数のレコードを入力できます。
    - 類似したTCP/IPベースのサービスを提供する複数のサーバーを、単一のDNSクエリで検出できるようになります。

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| recordset.recordList[0].priority | int | 最小0、最大65535 | 必須 |  | 優先順位 |
| recordset.recordList[0].weight | int | 最小0、最大65535 | 必須 |  | 重み |
| recordset.recordList[0].port | int | 最小0、最大65535 | 必須 |  | ポート |
| recordset.recordList[0].domainName | String | 最大255文字 | 必須 |  | ドメインを[FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)で入力 |


- NSレコードセット
    - 複数のレコードを入力できます。
    - レコードセット名に対するネームサーバーを指定します。
    - レコードセット名は、DNS Zone名のサブドメインとしてのみ作成及び変更が可能です。

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| recordset.recordList[0].domainName | String | 最大255文字 | 必須 |  | ドメインを[FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)で入力 |


#### レスポンス

[レスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "recordset": {
        "recordsetId": "d0b7ee57-8e41-438f-ad04-d4b316793d42",
        "recordsetName": "sub.test.dnsplus.com.",
        "recordsetType": "A",
        "recordsetTtl": 86400,
        "recordsetStatus": "USE",
        "createdAt": "2019-06-04T12:32:50.000+09:00",
        "updatedAt": "2019-06-04T12:32:50.000+09:00",
        "recordList": [
            {
                "recordDisabled": false,
                "recordContent": "1.1.1.1",
                "ipV4": "1.1.1.1"
            }
        ]
    }
}
```


### レコードセットの一括作成

- 複数のレコードセットを一括で作成します。1回のリクエストで最大2,000個まで作成できます。
- **レコードセットのタイプ**は、A、AAAA、CAA、CNAME、MX、NAPTR、PTR、TXT、SRV、NS、SOAをサポートしています。
- SOAレコードセットは作成、変更、削除できません。また、NSレコードセットは**DNS Zone名**で作成、変更、削除できません。
- レコードセット内のレコード一覧の長さは最大512バイトです。
- TXTレコードセットは最大4096バイトです。
- DNS Zoneごとに最大5,000個のレコードセットを作成できます。
- レコードセットの作成数には上限があります。上限の引き上げをご希望の場合は、別途お問い合わせください。[お問い合わせ](https://www.nhncloud.com/jp/support/inquiry)

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| POST | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/zones/{zoneId}/recordsets/list |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。
- {zoneId}はDNS Zone IDであり、[DNS Zoneの照会](./api-guide/#dns-zone)で確認できます。
- レコード値は必須です。入力方法として、recordset.recordList[0].recordContentフィールドまたは詳細フィールドのいずれかを指定できます。
- recordContentフィールドは、半角スペースを区切り文字として詳細フィールドを1行で記述した内容です。詳細フィールドについては、[レコードセットの作成](./api-guide/#_14)の[レコードセットのタイプに応じた詳細フィールド]で確認できます。
- 詳細フィールドとrecordContentフィールドを同時に入力した場合、recordContentフィールドが優先して適用されます。

```
curl -X POST 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/zones/{zoneId}/recordsets/list' \
-H 'Content-Type: application/json' \
--data '{ "recordsetList": [{ "recordsetName": "sub.test.dnsplus.com.", "recordsetType": "A", "recordsetTtl": 86400, "recordList": [{ "recordDisabled": false, "recordContent": "1.1.1.1" }] }]}'
```

[フィールド]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| recordsetList | List |  | 必須 |  | レコードセット一覧 |
| recordsetList[0].recordsetName | String | 最大254文字<br>半角英小文字、数字、'.'、'-'、'_'<br>(DNS Zone名を含む) | 必須 |  | 作成するレコードセット名。<br>ドメインを[FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)で入力 |
| recordsetList[0].recordsetType | String | A, AAAA, CAA, CNAME, MX, <br>NAPTR, PTR, TXT, SRV, NS | 必須 |  | レコードセットのタイプ |
| recordsetList[0].recordsetTtl | int | 最小10、最大2147483647 | 必須 |  | ネームサーバーでのレコードセット情報の更新間隔 |
| recordsetList[0].recordList | List |  | 必須 |  | レコード一覧 |
| recordsetList[0].recordList[0].recordDisabled | boolean |  | 任意 | false | レコードの無効状態 |
| recordsetList[0].recordList[0].recordContent | String |  | 必須 |  | レコードセットのタイプに応じた詳細フィールドを1行で記述した内容 |

#### レスポンス

[レスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    }
}
```


### レコードセットの変更

- レコードセットを変更します。
- **レコードセット名**は変更できませんが、**レコードセットのタイプ**、**TTL(秒)**、**レコード値**は変更可能です。
- SOAレコードセットは作成、変更、削除できません。また、NSレコードセットは**DNS Zone名**で作成、変更、削除できません。
- レコードセット内のレコード一覧の長さは最大512バイトです。
- TXTレコードセットは最大4096バイトです。

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| PUT | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/zones/{zoneId}/recordsets/{recordsetId} |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。
- {zoneId}はDNS Zone IDであり、[DNS Zoneの照会](./api-guide/#dns-zone)で確認できます。
- {recordsetId}はレコードセットIDであり、[レコードセットの照会](./api-guide/#_11)で確認できます。
- レコード値は必須です。入力方法として、recordset.recordList[0].recordContentフィールドまたは詳細フィールドのいずれかを指定できます。
- recordContentフィールドは、半角スペースを区切り文字として詳細フィールドを1行で記述した内容です。詳細フィールドについては、[レコードセットの作成](./api-guide/#_14)の[レコードセットのタイプに応じた詳細フィールド]で確認できます。
- 詳細フィールドとrecordContentフィールドを同時に入力した場合、recordContentフィールドが優先して適用されます。

```
curl -X PUT 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/zones/{zoneId}/recordsets/{recordsetId}' \
-H 'Content-Type: application/json' \
--data '{ "recordset": { "recordsetType": "A", "recordsetTtl": 86400, "recordList": [{ "recordDisabled": false, "recordContent": "1.1.1.1" }] }}'
```

[フィールド]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| recordset | Object |  | 必須 |  | レコードセット |
| recordset.recordsetType | String | A, AAAA, CAA, CNAME, MX, <br>NAPTR, PTR, TXT, SRV, NS | 必須 |  | レコードセットのタイプ |
| recordset.recordsetTtl | int | 最小10、最大2147483647 | 必須 |  | ネームサーバーでのレコードセット情報の更新間隔 |
| recordset.recordList | List |  | 必須 |  | レコード一覧 |
| recordset.recordList[0].recordDisabled | boolean |  | 必須 |  | レコードの無効状態 |
| recordset.recordList[0].recordContent | String |  | 必須 |  | レコードセットのタイプに応じた詳細フィールドを1行で記述した内容 |


#### レスポンス

[レスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "recordset": {
        "recordsetId": "d0b7ee57-8e41-438f-ad04-d4b316793d42",
        "recordsetName": "sub.test.dnsplus.com.",
        "recordsetType": "A",
        "recordsetTtl": 86400,
        "recordsetStatus": "USE",
        "createdAt": "2019-06-04T12:32:50.000+09:00",
        "updatedAt": "2019-06-04T12:42:00.000+09:00",
        "recordList": [
            {
                "recordDisabled": false,
                "recordContent": "1.1.1.1",
                "ipV4": "1.1.1.1"
            }
        ]
    }
}
```


### レコードセットの削除

- 複数のレコードセットを一括削除します。レコードセット内のレコードも同時に削除されます。
- SOAレコードセットは作成、変更、削除できません。また、NSレコードセットは**DNS Zone名**で作成、変更、削除できません。

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| DELETE | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/zones/{zoneId}/recordsets |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。
- {zoneId}はDNS Zone IDであり、[DNS Zoneの照会](./api-guide/#dns-zone)で確認できます。
- レコードセットIDは[レコードセットの照会](./api-guide/#_11)で確認できます。

```
curl -X DELETE 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/zones/{zoneId}/recordsets?
recordsetIdList=edb9512b-6e62-409c-99ee-092d340e0adf,edb9512b-6e62-409c-99ee-092d340e0adf'
```

[フィールド]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| recordsetIdList | List | 最小1個、最大3,000個 | 必須 |  | レコードセットID一覧 |

#### レスポンス

[レスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    }
}
```

## GSLB API

### GSLBの照会

- GSLB一覧を取得します。
- Poolにヘルスチェックが紐付けられている場合、GSLB、Pool、エンドポイントそれぞれの正常ステータスを確認できます。

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| GET | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/gslbs |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。

```
curl -X GET 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/gslbs?showHealthy=true'
```

[オプション]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| gslbIdList | List | 最大3,000個 | 任意 |  | GSLB ID一覧 |
| searchGslbName | String |  | 任意 |  | 検索するGSLB名 |
| gslbDomain | String |  | 任意 |  | GSLBドメイン |
| showHealthy | boolean |  | 任意 |  | ヘルスチェック結果の表示有無 |
| page | int | 最小1 | 任意 | 1 | ページ番号 |
| limit | int | 最小1、最大3,000 | 任意 | 50 | 取得数 |
| sortDirection | String | DESC, ASC | 任意 | DESC | ソート方向(DESC: 降順、ASC: 昇順) |
| sortKey | String | CREATED_AT, <br>UPDATED_AT, <br>GSLB_NAME, <br>GSLB_DOMAIN, <br>GSLB_TTL, <br>GSLB_ROUTING_RULE, <br>GSLB_DISABLED | 任意 | CREATED_AT | ソート対象 <br>(CREATED_AT: 作成日時、<br>UPDATED_AT: 更新日時、<br>GSLB_NAME: GSLB名、<br>GSLB_DOMAIN: GSLBドメイン、<br>GSLB_TTL: GSLBドメインの更新間隔、<br>GSLB_ROUTING_RULE: ルーティングルール、<br>GSLB_DISABLED: GSLBの無効状態) |

#### レスポンス

[レスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "totalCount": 1,
    "gslbList": [
        {
            "gslbId": "91de0c6f-aeaa-44ec-b361-822acfcd5921",
            "gslbName": "GSLB-test",
            "gslbDomain": "rgpac3e7q9onlipdfg.toastgslb.com",
            "gslbTtl": 300,
            "gslbRoutingRule": "GEOLOCATION",
            "gslbDisabled": false,
            "healthy": true,
            "connectedPoolList": [
                {
                    "poolId": "8e4326d4-3862-4b46-819e-83a786add570",
                    "connectedPoolOrder": 1,
                    "pool": {
                        // Pool情報は省略
                    }
                },
                {
                    "poolId": "2f89d3fe-03bc-4711-826e-db2c89c12818",
                    "connectedPoolOrder": 2,
                    "connectedPoolRegionContent": "NORTHEAST_ASIA,SOUTHEAST_ASIA",
                    "pool": {
                        // Pool情報は省略
                    }
                }
            ],
            "createdAt": "2019-12-18T20:44:02.000+09:00",
            "updatedAt": "2019-12-18T21:01:05.000+09:00"
        }
    ]
}
```

[フィールド]

| 名前 | タイプ | 説明 |
|---|---|---|
| totalCount | long | GSLBの総数 |
| gslbList | List | GSLB一覧 |
| gslbList[0].gslbId | String | GSLB ID |
| gslbList[0].gslbName | String | GSLB名 |
| gslbList[0].gslbDomain | String | GSLBドメイン |
| gslbList[0].gslbTtl | String | GSLBドメインの更新周期 |
| gslbList[0].gslbRoutingRule | String | ルーティングルール |
| gslbList[0].gslbDisabled | boolean | GSLBの無効化状態 |
| gslbList[0].healthy | boolean | GSLBが正常かどうか |
| gslbList[0].connectedPoolList | List | 接続されたPool一覧 |
| gslbList[0].connectedPoolList[0].poolId | String | 接続されたPool ID |
| gslbList[0].connectedPoolList[0].pool | Object | 接続されたPool情報 |
| gslbList[0].connectedPoolList[0].connectedPoolOrder | int | 接続されたPoolの優先順位 |
| gslbList[0].connectedPoolList[0].connectedPoolRegionContent | String | 接続されたPoolの地域を1行で表示した内容 |
| gslbList[0].createdAt | DateTime | 作成日 |
| gslbList[0].updatedAt | DateTime | 修正日 |


### GSLB作成

- GSLBとPoolの接続設定を作成します。
- **ルーティングルール**は、GSLBドメインに対するロードバランシング方式であり、FAILOVER、RANDOM、GEOLOCATIONから選択できます。
    - FAILOVER: 接続されたPoolの優先順位に従ってルーティングします。
    - RANDOM: 接続されたPoolの中から、使用可能なPoolを無作為に選択してルーティングします。
    - GEOLOCATION: 設定された地域のトラフィックを、該当する接続されたPoolへルーティングします。地域設定がない場合は、優先順位に従ってルーティングします。
- **接続されたPool**の**優先順位**は、値が小さいほどルーティングの順序が高くなります。なお、値を重複させることはできません。
- GSLBの作成数とPoolの接続数には上限があります。上限の引き上げが必要な場合は、別途お問い合わせください。[お問い合わせ](https://www.nhncloud.com/kr/support/inquiry)

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| POST | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/gslbs |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。
- connectedPoolRegionContentフィールドは、カンマ(,)を区切り文字として**地域**を1行で記述します。

```
curl -X POST 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/gslbs' \
-H 'Content-Type: application/json' \
--data '{ "gslb": { "gslbName": "GSLB-test", "gslbTtl": 300, "gslbRoutingRule": "FAILOVER", "connectedPoolList": [ { "poolId": "8e4326d4-3862-4b46-819e-83a786add570", "connectedPoolOrder": 1 }, { "poolId": "2f89d3fe-03bc-4711-826e-db2c89c12818", "connectedPoolOrder": 2 } ] }}'
```

[フィールド]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| gslb | Object |  | 必須 |  | GSLB |
| gslb.gslbName | String | 最大100文字、<br>半角英大文字・小文字と数字、'-'、'_' | 必須 |  | GSLB名 |
| gslb.gslbTtl | int |  | 必須 |  | GSLBドメインの更新周期 |
| gslb.gslbRoutingRule | String | FAILOVER, RANDOM, GEOLOCATION | 必須 |  | ルーティングルール |
| gslb.gslbDisabled | boolean |  | 任意 | false | GSLBの無効化状態 |
| gslb.connectedPoolList | List |  | 任意 |  | 接続されたPool一覧 |
| gslb.connectedPoolList[0].poolId | String |  | 必須 |  | 接続されたPool ID |
| gslb.connectedPoolList[0].connectedPoolOrder | int | 最小1、最大2,147,483,647 | 必須 |  | 接続されたPoolの優先順位 |
| gslb.connectedPoolList[0].connectedPoolRegionContent | String | WESTERN_NORTH_AMERICA,<br>EASTERN_NORTH_AMERICA,<br>WESTERN_EUROPE,<br>EASTERN_EUROPE,<br>NORTHERN_SOUTH_AMERICA,<br>SOUTHERN_SOUTH_AMERICA,<br>OCEANIA,<br>MIDDLE_EAST,<br>NORTHERN_AFRICA,<br>SOUTHERN_AFRICA,<br>INDIA,<br>SOUTHEAST_ASIA,<br>NORTHEAST_ASIA | 任意 |  | 接続されたPoolの地域設定 |

#### レスポンス

[レスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "gslb": {
        "gslbId": "91de0c6f-aeaa-44ec-b361-822acfcd5921",
        "gslbName": "GSLB-test",
        "gslbDomain": "rgpac3e7q9onlipdfg.toastgslb.com",
        "gslbTtl": 300,
        "gslbRoutingRule": "FAILOVER",
        "gslbDisabled": false,
        "connectedPoolList": [
            {
                "poolId": "8e4326d4-3862-4b46-819e-83a786add570",
                "connectedPoolOrder": 1,
                "pool": {
                    // Pool情報は省略
                }
            },
            {
                "poolId": "2f89d3fe-03bc-4711-826e-db2c89c12818",
                "connectedPoolOrder": 2,
                "pool": {
                    // Pool情報は省略
                }
            }
        ],
        "createdAt": "2019-12-18T20:44:02.000+09:00",
        "updatedAt": "2019-12-18T20:44:03.000+09:00"
    }
}
```


### GSLB修正

- GSLBとPoolの接続設定を修正します。
- [GSLB作成](./api-guide/#gslb_1)で入力した項目を修正します。

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| PUT | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/gslbs/{gslbId} |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。
- {gslbId}はGSLB IDであり、[GSLB照会](./api-guide/#gslb)で確認できます。
- connectedPoolRegionContentフィールドは、カンマ(,)を区切り文字として**地域**を1行で記述します。

```
curl -X PUT 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/gslbs/{gslbId}' \
-H 'Content-Type: application/json' \
--data '{ "gslb": { "gslbName": "GSLB-test", "gslbTtl": 300, "gslbDisabled": true, "gslbRoutingRule": "GEOLOCATION", "connectedPoolList": [ { "poolId": "8e4326d4-3862-4b46-819e-83a786add570", "connectedPoolOrder": 1 }, { "poolId": "2f89d3fe-03bc-4711-826e-db2c89c12818", "connectedPoolOrder": 2, "connectedPoolRegionContent": "NORTHEAST_ASIA,SOUTHEAST_ASIA" } ] }}'
```

[フィールド]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| gslb | Object |  | 必須 |  | GSLB |
| gslb.gslbName | String | 最大100文字、<br>半角英大文字・小文字と数字、'-'、'_' | 必須 |  | GSLB名 |
| gslb.gslbTtl | int |  | 必須 |  | GSLBドメインの更新周期 |
| gslb.gslbRoutingRule | String | FAILOVER, RANDOM, GEOLOCATION | 必須 |  | ルーティングルール |
| gslb.gslbDisabled | boolean |  | 任意 | false | GSLBの無効化状態 |
| gslb.connectedPoolList | List |  | 任意 |  | 接続されたPool一覧 |
| gslb.connectedPoolList[0].poolId | String |  | 必須 |  | 接続されたPool ID |
| gslb.connectedPoolList[0].connectedPoolOrder | int | 最小1、最大2,147,483,647 | 必須 |  | 接続されたPoolの優先順位 |
| gslb.connectedPoolList[0].connectedPoolRegionContent | String | WESTERN_NORTH_AMERICA,<br>EASTERN_NORTH_AMERICA,<br>WESTERN_EUROPE,<br>EASTERN_EUROPE,<br>NORTHERN_SOUTH_AMERICA,<br>SOUTHERN_SOUTH_AMERICA,<br>OCEANIA,<br>MIDDLE_EAST,<br>NORTHERN_AFRICA,<br>SOUTHERN_AFRICA,<br>INDIA,<br>SOUTHEAST_ASIA,<br>NORTHEAST_ASIA | 任意 |  | 接続されたPoolの地域設定 |

#### レスポンス

[レスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "gslb": {
        "gslbId": "91de0c6f-aeaa-44ec-b361-822acfcd5921",
        "gslbName": "GSLB-test",
        "gslbDomain": "rgpac3e7q9onlipdfg.toastgslb.com",
        "gslbTtl": 300,
        "gslbRoutingRule": "GEOLOCATION",
        "gslbDisabled": true,
        "connectedPoolList": [
            {
                "poolId": "8e4326d4-3862-4b46-819e-83a786add570",
                "connectedPoolOrder": 1,
                "pool": {
                    // Pool情報は省略
                }
            },
            {
                "poolId": "2f89d3fe-03bc-4711-826e-db2c89c12818",
                "connectedPoolOrder": 2,
                "connectedPoolRegionContent": "NORTHEAST_ASIA,SOUTHEAST_ASIA",
                "pool": {
                    // Pool情報は省略
                }
            }
        ],
        "createdAt": "2019-12-18T20:44:02.000+09:00",
        "updatedAt": "2019-12-18T20:59:49.000+09:00"
    }
}
```


### GSLB削除

- 複数のGSLBを削除します。

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| DELETE | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/gslbs |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。

```
curl -X DELETE 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/gslbs?
gslbIdList=91de0c6f-aeaa-44ec-b361-822acfcd5921,269eff10-f3c0-4b11-b072-ec53e7c604bf'
```

[フィールド]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| gslbIdList | List | 最小1個、最大3,000個 | 必須 |  | GSLB ID一覧 |

#### レスポンス

[レスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    }
}
```


### Pool接続

- GSLBにPoolを接続します。
- **接続されたPool**の**優先順位**は、値が小さいほどルーティングの順序が高くなります。既存のPoolと同じ優先順位を入力した場合、既存のPoolのルーティング順序は下がります。
- Poolの接続数には上限があります。上限の引き上げが必要な場合は、別途お問い合わせください。[お問い合わせ](https://www.nhncloud.com/kr/support/inquiry)

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| POST | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/gslbs/{gslbId}/connected-pools/{poolId} |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。
- {gslbId}はGSLB IDであり、[GSLB照会](./api-guide/#gslb)で確認できます。
- {poolId}はPool IDであり、[Pool照会](./api-guide/#pool_3)で確認できます。
- connectedPoolRegionContentフィールドは、カンマ(,)を区切り文字として**地域**を1行で記述します。

```
curl -X POST 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/gslbs/{gslbId}/connected-pools/{poolId}' \
-H 'Content-Type: application/json' \
--data '{ "connectedPool": { "connectedPoolOrder": 1 } }'
```

[フィールド]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| connectedPool | Object |  | 必須 |  | 接続されたPool |
| connectedPool.connectedPoolOrder | int | 最小1、最大2,147,483,647 | 必須 |  | 接続されたPoolの優先順位 |
| connectedPool.connectedPoolRegionContent | String | WESTERN_NORTH_AMERICA,<br>EASTERN_NORTH_AMERICA,<br>WESTERN_EUROPE,<br>EASTERN_EUROPE,<br>NORTHERN_SOUTH_AMERICA,<br>SOUTHERN_SOUTH_AMERICA,<br>OCEANIA,<br>MIDDLE_EAST,<br>NORTHERN_AFRICA,<br>SOUTHERN_AFRICA,<br>INDIA,<br>SOUTHEAST_ASIA,<br>NORTHEAST_ASIA | 任意 |  | 接続されたPoolの地域設定 |

#### レスポンス

[レスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "connectedPoolList": [
        {
            "poolId": "52da0e48-9062-43f7-bef8-8aec4b795bfe",
            "connectedPoolOrder": 1,
            "pool": {
                // Pool情報は省略
            }
        },
        {
            "poolId": "8e4326d4-3862-4b46-819e-83a786add570",
            "connectedPoolOrder": 2,
            "pool": {
                // Pool情報は省略
            }
        },
        {
            "poolId": "2f89d3fe-03bc-4711-826e-db2c89c12818",
            "connectedPoolOrder": 3,
            "connectedPoolRegionContent": "NORTHEAST_ASIA,SOUTHEAST_ASIA",
            "pool": {
                // Pool情報は省略
            }
        }
    ]
}
```

### Pool接続の修正

- GSLBに接続されたPoolの設定を修正します。
- [GSLB作成](./api-guide/#gslb_1)のPool設定、または[Pool接続](./api-guide/#pool)で入力した項目を修正します。

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| PUT | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/gslbs/{gslbId}/connected-pools/{poolId} |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。
- {gslbId}はGSLB IDであり、[GSLB照会](./api-guide/#gslb)で確認できます。
- {poolId}はPool IDであり、[Pool照会](./api-guide/#pool_3)で確認できます。
- connectedPoolRegionContentフィールドは、カンマ(,)を区切り文字として**地域**を1行で記述します。

```
curl -X PUT 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/gslbs/{gslbId}/connected-pools/{poolId}' \
-H 'Content-Type: application/json' \
--data '{ "connectedPool": { "connectedPoolOrder": 1, "connectedPoolRegionContent": "WESTERN_NORTH_AMERICA" } }'
```

[フィールド]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| connectedPool | Object |  | 必須 |  | 接続されたPool |
| connectedPool.connectedPoolOrder | int | 最小1、最大2,147,483,647 | 必須 |  | 接続されたPoolの優先順位 |
| connectedPool.connectedPoolRegionContent | String | WESTERN_NORTH_AMERICA,<br>EASTERN_NORTH_AMERICA,<br>WESTERN_EUROPE,<br>EASTERN_EUROPE,<br>NORTHERN_SOUTH_AMERICA,<br>SOUTHERN_SOUTH_AMERICA,<br>OCEANIA,<br>MIDDLE_EAST,<br>NORTHERN_AFRICA,<br>SOUTHERN_AFRICA,<br>INDIA,<br>SOUTHEAST_ASIA,<br>NORTHEAST_ASIA | 任意 |  | 接続されたPoolの地域設定 |

#### レスポンス

[レスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "connectedPoolList": [
        {
            "poolId": "52da0e48-9062-43f7-bef8-8aec4b795bfe",
            "connectedPoolOrder": 1,
            "connectedPoolRegionContent": "WESTERN_NORTH_AMERICA",
            "pool": {
                // Pool情報は省略
            }
        },
        {
            "poolId": "8e4326d4-3862-4b46-819e-83a786add570",
            "connectedPoolOrder": 2,
            "pool": {
                // Pool情報は省略
            }
        },
        {
            "poolId": "2f89d3fe-03bc-4711-826e-db2c89c12818",
            "connectedPoolOrder": 3,
            "connectedPoolRegionContent": "NORTHEAST_ASIA,SOUTHEAST_ASIA",
            "pool": {
                // Pool情報は省略
            }
        }
    ]
}
```

### Pool接続の解除

- GSLBに接続された複数のPoolを解除します。

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| DELETE | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/gslbs/{gslbId}/connected-pools |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。
- {gslbId}はGSLB IDであり、[GSLB照会](./api-guide/#gslb)で確認できます。

```
curl -X DELETE 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/gslbs/{gslbId}/connected-pools?
poolIdList=52da0e48-9062-43f7-bef8-8aec4b795bfe,12bc396a-eb97-4a6b-ab4c-73d1a1dfb093'
```

[フィールド]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| poolIdList | List | 最小1個、最大3,000個 | 必須 |  | Pool ID一覧 |

#### レスポンス

[レスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "connectedPoolList": [
        {
            "poolId": "8e4326d4-3862-4b46-819e-83a786add570",
            "connectedPoolOrder": 2,
            "pool": {
                // Pool情報は省略
            }
        },
        {
            "poolId": "2f89d3fe-03bc-4711-826e-db2c89c12818",
            "connectedPoolOrder": 3,
            "connectedPoolRegionContent": "NORTHEAST_ASIA,SOUTHEAST_ASIA",
            "pool": {
                // Pool情報は省略
            }
        }
    ]
}
```


## Pool API

### Pool照会

- Pool一覧を照会します。
- ヘルスチェックが接続されている場合、Pool及びエンドポイントの正常ステータスを確認できます。

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| GET | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/pools |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。

```
curl -X GET 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/pools?showHealthy=true'
```

[オプション]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| poolIdList | List | 最大3,000個 | 任意 |  | Pool ID一覧 |
| searchPoolName | String |  | 任意 |  | 検索するPool名 |
| healthCheckId | String |  | 任意 |  | 接続されたヘルスチェックID |
| showHealthy | boolean |  | 任意 |  | ヘルスチェック結果の表示有無 |
| page | int | 最小1 | 任意 | 1 | ページ番号 |
| limit | int | 最小1、最大3,000 | 任意 | 50 | 取得数 |
| sortDirection | String | DESC, ASC | 任意 | DESC | ソート方向(DESC: 降順、ASC: 昇順) |
| sortKey | String | CREATED_AT, <br>UPDATED_AT, <br>POOL_NAME, <br>POOL_DISABLED, <br>HEALTH_CHECK_ID | 任意 | CREATED_AT | ソート対象 <br>(CREATED_AT: 作成日、<br>UPDATED_AT: 修正日、<br>POOL_NAME: Pool名、<br>POOL_DISABLED: Poolの無効状態、<br>HEALTH_CHECK_ID: 接続されたヘルスチェックID) |

#### レスポンス

[レスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "totalCount": 1,
    "poolList": [
        {
            "poolId": "8e4326d4-3862-4b46-819e-83a786add570",
            "poolName": "POOL-test",
            "poolDisabled": false,
            "healthy": true,
            "healthCheckId": "b9165853-7859-4309-8059-48f12ebdbc17",
            "healthCheck": {
                // ヘルスチェック情報は省略
            },
            "endpointList": [
                {
                    "endpointAddress": "test.dnsplus.com",
                    "endpointWeight": 1.0,
                    "endpointDisabled": false,
                    "healthy": true,
                    "failureReason": "No failures"
                },
                {
                    "endpointAddress": "123.123.123.123",
                    "endpointWeight": 1.0,
                    "endpointDisabled": false,
                    "healthy": false,
                    "failureReason": "HTTP timeout occurred"
                },
                {
                    "endpointAddress": "test2.dnsplus.com",
                    "endpointWeight": 1.0,
                    "endpointDisabled": true
                }
            ],
            "createdAt": "2019-12-18T18:36:02.000+09:00",
            "updatedAt": "2019-12-18T18:43:31.000+09:00"
        }
    ]
}
```

[フィールド]

| 名前 | タイプ | 説明 |
|---|---|---|
| totalCount | long | 全Pool数 |
| poolList | List | Pool一覧 |
| poolList[0].poolId | String | Pool ID |
| poolList[0].poolName | String | Pool名 |
| poolList[0].poolDisabled | boolean | Poolの無効状態 |
| poolList[0].healthy | boolean | Poolが正常かどうか |
| poolList[0].healthCheckId | String | 接続されたヘルスチェックID |
| poolList[0].healthCheck | Object | 接続されたヘルスチェック情報 |
| poolList[0].endpointList | List | エンドポイント一覧 |
| poolList[0].endpointList[0].endpointAddress | String | エンドポイントアドレス |
| poolList[0].endpointList[0].endpointWeight | double | エンドポイントの重み付け |
| poolList[0].endpointList[0].healthy | boolean | エンドポイントが正常かどうか |
| poolList[0].endpointList[0].failureReason | String | エンドポイントが異常な理由 |
| poolList[0].createdAt | DateTime | 作成日 |
| poolList[0].updatedAt | DateTime | 修正日 |


### Pool作成

- Pool、及びPool内にエンドポイントを作成します。
- Pool内のエンドポイントへのアクセスを確認するための**ヘルスチェック**を設定できます。
- **エンドポイントアドレス**は、ドメインアドレスまたはIPv4で入力できますが、入力には以下の制限があります。
    - ハイフン(-)とピリオド(.)から開始することはできず、ハイフンで終了することもできません。また、ピリオドとハイフンを連続して入力することはできません。
    - [予約済みIPアドレス](https://en.wikipedia.org/wiki/Reserved_IP_addresses)は入力できません。
    - Pool内で値を重複させることはできません。
- エンドポイントの**重み付け**は、Pool内の他のエンドポイントの重み付けに対して相対的に機能します。同一の重み付けを設定した場合、Pool内で均等な比重を持ちます。
- Poolの作成数、Pool内のエンドポイント数、及び全体のエンドポイント数には上限があります。上限の引き上げが必要な場合は、別途お問い合わせください。[お問い合わせ](https://www.nhncloud.com/kr/support/inquiry)

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| POST | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/pools |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。

```
curl -X POST 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/pools' \
-H 'Content-Type: application/json' \
--data '{ "pool": { "poolName": "POOL-test", "endpointList": [ { "endpointAddress": "test.dnsplus.com" }, { "endpointAddress": "123.123.123.123" } ] }}'
```

[フィールド]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| pool | Object |  | 必須 |  | Pool |
| pool.poolName | String | 最大100文字、<br>半角英大文字・小文字と数字、'-'、'_' | 必須 |  | Pool名 |
| pool.poolDisabled | boolean |  | 任意 | false | Poolの無効状態 |
| pool.healthCheckId | String |  | 任意 |  | ヘルスチェックID |
| pool.endpointList | List |  | 必須 |  | エンドポイント一覧 |
| pool.endpointList[0].endpointAddress | String | 最大254文字、<br>半角小文字と数字、'.'、'-'、'_' | 必須 |  | エンドポイントアドレス |
| pool.endpointList[0].endpointWeight | double | 最小0、最大1.00 | 任意 | 1.00 | エンドポイントの重み付け |
| pool.endpointList[0].endpointDisabled | boolean |  | 任意 | false | エンドポイントの無効状態 |

#### レスポンス

[レスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "pool": {
        "poolId": "8e4326d4-3862-4b46-819e-83a786add570",
        "poolName": "POOL-test",
        "poolDisabled": false,
        "healthCheckId": "",
        "endpointList": [
            {
                "endpointAddress": "test.dnsplus.com",
                "endpointWeight": 1.0,
                "endpointDisabled": false
            },
            {
                "endpointAddress": "123.123.123.123",
                "endpointWeight": 1.0,
                "endpointDisabled": false
            }
        ],
        "createdAt": "2019-12-18T18:36:02.000+09:00",
        "updatedAt": "2019-12-18T18:36:02.000+09:00"
    }
}
```


### Pool修正

- Pool、及びPool内のエンドポイントを修正します。
- [Pool作成](./api-guide/#pool_4)で入力した項目を修正します。

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| PUT | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/pools/{poolId} |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。
- {poolId}はPool IDであり、[Pool照会](./api-guide/#pool_3)で確認できます。

```
curl -X PUT 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/pools/{poolId}' \
-H 'Content-Type: application/json' \
--data '{ "pool": { "poolName": "POOL-test", "poolDisabled": true, "healthCheckId": "b9165853-7859-4309-8059-48f12ebdbc17", "endpointList": [ { "endpointAddress": "test.dnsplus.com", "endpointWeight": 1.00, "endpointDisabled": true }, { "endpointAddress": "123.123.123.123", "endpointWeight": 0.5, "endpointDisabled": true } ] }}'
```

[フィールド]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| pool | Object |  | 必須 |  | Pool |
| pool.poolName | String | 最大100文字、<br>半角英大文字・小文字と数字、'-'、'_' | 必須 |  | Pool名 |
| pool.poolDisabled | boolean |  | 任意 | false | Poolの無効状態 |
| pool.healthCheckId | String |  | 任意 |  | ヘルスチェックID |
| pool.endpointList | List |  | 必須 |  | エンドポイント一覧 |
| pool.endpointList[0].endpointAddress | String | 最大254文字、<br>半角小文字と数字、'.'、'-'、'_' | 必須 |  | エンドポイントアドレス |
| pool.endpointList[0].endpointWeight | double | 最小0、最大1.00 | 任意 | 1.00 | エンドポイントの重み付け |
| pool.endpointList[0].endpointDisabled | boolean |  | 任意 | false | エンドポイントの無効状態 |

#### レスポンス

[レスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "pool": {
        "poolId": "8e4326d4-3862-4b46-819e-83a786add570",
        "poolName": "POOL-test",
        "poolDisabled": true,
        "healthCheckId": "b9165853-7859-4309-8059-48f12ebdbc17",
        "healthCheck": {
            // ヘルスチェック情報は省略
        },
        "endpointList": [
            {
                "endpointAddress": "test.dnsplus.com",
                "endpointWeight": 1.0,
                "endpointDisabled": true
            },
            {
                "endpointAddress": "123.123.123.123",
                "endpointWeight": 0.5,
                "endpointDisabled": true
            }
        ],
        "createdAt": "2019-12-18T18:36:02.000+09:00",
        "updatedAt": "2019-12-18T18:37:45.000+09:00"
    }
}
```


### Pool削除

- 複数のPoolを削除します。Pool内のエンドポイントも同時に削除されます。
- GSLBに接続されているPoolは削除できません。

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| DELETE | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/pools |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。

```
curl -X DELETE 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/pools?
poolIdList=8e4326d4-3862-4b46-819e-83a786add570,2f89d3fe-03bc-4711-826e-db2c89c12818'
```

[フィールド]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| poolIdList | List | 最小1個、最大3,000個 | 必須 |  | Pool ID一覧 |

#### レスポンス

[レスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    }
}
```


## ヘルスチェックAPI

### ヘルスチェック照会

- ヘルスチェック一覧を照会します。

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| GET | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/health-checks |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。

```
curl -X GET 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/health-checks'
```

[オプション]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| healthCheckIdList | List | 最大3,000個 | 任意 |  | ヘルスチェックID一覧 |
| searchHealthCheckName | String |  | 任意 |  | 検索するヘルスチェック名 |
| page | int | 最小1 | 任意 | 1 | ページ番号 |
| limit | int | 最小1、最大3,000 | 任意 | 50 | 取得数 |
| sortDirection | String | DESC, ASC | 任意 | DESC | ソート方向(DESC: 降順、ASC: 昇順) |
| sortKey | String | CREATED_AT, <br>UPDATED_AT, <br>HEALTH_CHECK_NAME, <br>PROTOCOL, <br>PORT | 任意 | CREATED_AT | ソート対象 <br>(CREATED_AT: 作成日、<br>UPDATED_AT: 修正日、<br>HEALTH_CHECK_NAME: ヘルスチェック名、<br>PROTOCOL: プロトコル、<br>PORT: ポート) |

#### レスポンス

[レスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "totalCount": 1,
    "healthCheckList": [
        {
            "healthCheckId": "b9165853-7859-4309-8059-48f12ebdbc17",
            "healthCheckName": "HTTPS-443",
            "protocol": "HTTPS",
            "port": 443,
            "interval": 60,
            "timeout": 5,
            "retries": 2,
            "path": "/",
            "expectedCodes": "2xx",
            "expectedBody": "OK",
            "allowInsecure": false,
            "requestHeaderList": [
                { "Host": "nhncloud.com" }
            ],
            "createdAt": "2019-12-18T12:31:34.000+09:00",
            "updatedAt": "2019-12-18T14:19:20.000+09:00"
        }
    ]
}
```

[フィールド]

| 名前 | タイプ | 説明 |
|---|---|---|
| totalCount | long | 全ヘルスチェック数 |
| healthCheckList | List | ヘルスチェック一覧 |
| healthCheckList[0].healthCheckId | String | ヘルスチェックID |
| healthCheckList[0].healthCheckName | String | ヘルスチェック名 |
| healthCheckList[0].protocol | String | プロトコル |
| healthCheckList[0].port | int | ポート |
| healthCheckList[0].interval | int | ヘルスチェック周期 |
| healthCheckList[0].timeout | int | 最大応答待機時間 |
| healthCheckList[0].retries | int | 最大再試行回数 |
| healthCheckList[0].path | String | パス |
| healthCheckList[0].expectedCodes | String | 予想ステータスコード |
| healthCheckList[0].expectedBody | String | 予想レスポンス本文 |
| healthCheckList[0].allowInsecure | boolean | 証明書の検証なし |
| healthCheckList[0].requestHeaderList | List | リクエストヘッダ一覧 |
| healthCheckList[0].requestHeaderList[0] | Object | リクエストヘッダ名、値のオブジェクト |
| healthCheckList[0].createdAt | DateTime | 作成日 |
| healthCheckList[0].updatedAt | DateTime | 修正日 |


### ヘルスチェック作成

- ヘルスチェックを作成します。
- ヘルスチェックの**プロトコル**は、HTTPS、HTTP、TCPをサポートしています。選択したプロトコルによって、入力可能な項目が異なります。
    - HTTPSで入力可能な項目: 証明書の検証なし、ポート、ヘルスチェック周期、最大応答待機時間、最大再試行回数、パス、予想ステータスコード、予想レスポンス本文、リクエストヘッダ
    - HTTPで入力可能な項目: ポート、ヘルスチェック周期、最大応答待機時間、最大再試行回数、パス、予想ステータスコード、予想レスポンス本文、リクエストヘッダ
    - TCPで入力可能な項目: ポート、ヘルスチェック周期、最大応答待機時間、最大再試行回数
- **証明書の検証なし**を有効にすると、ヘルスチェック実行時にエンドポイントのTLS/SSL証明書が無効であっても無視できます。
- **予想ステータスコード**及び**予想レスポンス本文**の判定において、エンドポイントからリダイレクトされたページはサポートされません。
- ヘルスチェックの作成数には上限があります。上限の引き上げが必要な場合は、別途お問い合わせください。[お問い合わせ](https://www.nhncloud.com/kr/support/inquiry)

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| POST | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/health-checks |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。

```
curl -X POST 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/health-checks' \
-H 'Content-Type: application/json' \
--data '{ "healthCheck": { "healthCheckName": "HTTPS-443", "protocol": "HTTPS", "port": 443, "interval": 60, "timeout": 5, "retries": 2, "path": "/", "expectedCodes": "2xx", "allowInsecure": false, "requestHeaderList": [{ "Host": "nhncloud.com" }] }}'
```

[フィールド]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| healthCheck | Object |  | 必須 |  | ヘルスチェック |
| healthCheck.healthCheckName | String | 最大100文字、<br>半角英大文字・小文字と数字、'-'、'_' | 必須 |  | ヘルスチェック名 |
| healthCheck.protocol | String | HTTPS, HTTP, TCP | 必須 |  | ヘルスチェック実行プロトコル |
| healthCheck.port | int | 最小1、最大65535 | 必須 |  | ヘルスチェック実行ポート |
| healthCheck.interval | int | 最小10、または(retries+1)*timeout、最大3600 | 任意 | 60 | ヘルスチェック周期 |
| healthCheck.timeout | int | 最小1、最大10 | 任意 | 5 | 最大応答待機時間 |
| healthCheck.retries | int | 最小0、最大5 | 任意 | 2 | 最大再試行回数 |
| healthCheck.path | String | 最大254文字、<br>開始文字は'/' | 任意 |  | ヘルスチェック実行パス、<br>HTTPS、HTTPの際に使用 |
| healthCheck.expectedCodes | String | 数字とワイルドカード 'x' | 任意 |  | ヘルスチェック予想ステータスコード、<br>HTTPS、HTTPの際に使用<br>例: 2xx、20x、200 |
| healthCheck.expectedBody | String | 最大10KB | 任意 |  | ヘルスチェック予想レスポンス本文、<br>HTTPS、HTTPの際に使用 |
| healthCheck.allowInsecure | boolean |  | 任意 |  | ヘルスチェック証明書の検証なし、<br>HTTPSの際に使用 |
| healthCheck.requestHeaderList | List |  | 任意 |  | リクエストヘッダ一覧、<br>HTTPS、HTTPの際に使用。<br> 一覧内の項目は `{ "ヘッダ名": "ヘッダ値" }` の形式でリクエストします。 |

#### レスポンス

[レスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "healthCheck": {
        "healthCheckId": "b9165853-7859-4309-8059-48f12ebdbc17",
        "healthCheckName": "HTTPS-443",
        "protocol": "HTTPS",
        "port": 443,
        "interval": 60,
        "timeout": 5,
        "retries": 2,
        "path": "/",
        "expectedCodes": "2xx",
        "allowInsecure": false,
        "requestHeaderList": [
            { "Host": "nhncloud.com" }
        ],
        "createdAt": "2019-12-18T12:31:34.000+09:00",
        "updatedAt": "2019-12-18T12:31:34.000+09:00"
    }
}
```


### ヘルスチェック修正

- ヘルスチェックを修正します。
- [ヘルスチェック作成](./api-guide/#_48)で入力した項目を修正します。

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| PUT | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/health-checks/{healthCheckId} |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。
- {healthCheckId}はヘルスチェックIDであり、[ヘルスチェック照会](./api-guide/#_45)で確認できます。

```
curl -X PUT 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/health-checks/{healthCheckId}' \
-H 'Content-Type: application/json' \
--data '{ "healthCheck": { "healthCheckName": "HTTPS-443", "protocol": "HTTPS", "port": 443, "interval": 60, "timeout": 5, "retries": 2, "path": "/", "expectedCodes": "3xx", "allowInsecure": false }}'
```

[フィールド]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| healthCheck | Object |  | 必須 |  | ヘルスチェック |
| healthCheck.healthCheckName | String | 最大100文字、<br>半角英大文字・小文字と数字、'-'、'_' | 必須 |  | ヘルスチェック名 |
| healthCheck.protocol | String | HTTPS, HTTP, TCP | 必須 |  | ヘルスチェック実行プロトコル |
| healthCheck.port | int | 最小1、最大65535 | 必須 |  | ヘルスチェック実行ポート |
| healthCheck.interval | int | 最小10、または(retries+1)*timeout、最大3600 | 任意 | | ヘルスチェック周期 |
| healthCheck.timeout | int | 最小1、最大10 | 任意 | | 最大応答待機時間 |
| healthCheck.retries | int | 最小0、最大5 | 任意 | | 最大再試行回数 |
| healthCheck.path | String | 最大254文字、<br>開始文字は'/' | 任意 |  | ヘルスチェック実行パス、<br>HTTPS、HTTPの際に使用 |
| healthCheck.expectedCodes | String | 数字とワイルドカード 'x' | 任意 |  | ヘルスチェック予想ステータスコード、<br>HTTPS、HTTPの際に使用<br>例: 2xx、20x、200 |
| healthCheck.expectedBody | String | 最大10KB | 任意 |  | ヘルスチェック予想レスポンス本文、<br>HTTPS、HTTPの際に使用 |
| healthCheck.allowInsecure | boolean |  | 任意 |  | ヘルスチェック証明書の検証なし、<br>HTTPSの際に使用 |
| healthCheck.requestHeaderList | List |  | 任意 |  | リクエストヘッダ一覧、<br>HTTPS、HTTPの際に使用。<br> 一覧内の項目は `{ "ヘッダ名": "ヘッダ値" }` の形式でリクエストします。 |

#### レスポンス

[レスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "healthCheck": {
        "healthCheckId": "b9165853-7859-4309-8059-48f12ebdbc17",
        "healthCheckName": "HTTPS-443",
        "protocol": "HTTPS",
        "port": 443,
        "interval": 60,
        "timeout": 5,
        "retries": 2,
        "path": "/",
        "expectedCodes": "3xx",
        "allowInsecure": false,
        "requestHeaderList": [
            { "Host": "nhncloud.com" }
        ],
        "createdAt": "2019-12-18T12:31:34.000+09:00",
        "updatedAt": "2019-12-18T12:36:20.000+09:00"
    }
}
```


### ヘルスチェック削除

- 複数のヘルスチェックを削除します。
- Poolに接続されているヘルスチェックは削除できません。

#### リクエスト

[URI]

| メソッド | URI |
|---|---|
| DELETE | https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/health-checks |

[リクエスト本文]

- {appkey}はコンソールで確認した値に置き換えます。

```
curl -X DELETE 'https://dnsplus.api.nhncloudservice.com/dnsplus/v2.0/appkeys/{appkey}/health-checks?
healthCheckIdList=b9165853-7859-4309-8059-48f12ebdbc17,d2629d6b-9381-4645-9cf3-43d7ad491e2b'
```

[フィールド]

| 名前 | タイプ | 有効範囲 | 必須 | デフォルト値 | 説明 |
|---|---|---|---|---|---|
| healthCheckIdList | List | 最小1個、最大3,000個 | 必須 |  | ヘルスチェックID一覧 |

#### レスポンス

[レスポンス本文]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    }
}
```
