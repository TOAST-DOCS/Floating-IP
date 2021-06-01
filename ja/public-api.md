## Network > Floating IP > API v2ガイド

APIを使用するにはAPIエンドポイントとトークンなどが必要です。[API使用準備](https://docs.toast.com/ja/Compute/Compute/ja/identity-api/)を参照してAPIを使用するのに必要な情報を準備します。

Floating IP APIは`network`タイプエンドポイントを利用します。正確なエンドポイントはトークン発行レスポンスの`serviceCatalog`を参照します。

| タイプ | リージョン | エンドポイント |
|---|---|---|
| network | 韓国(パンギョ)リージョン<br>韓国(坪村)リージョン<br>日本リージョン | https://kr1-api-network.infrastructure.cloud.toast.com<br>https://kr2-api-network.infrastructure.cloud.toast.com<br>https://jp1-api-network.infrastructure.cloud.toast.com |

APIレスポンスにガイドに明示されていないフィールドが表示される場合があります。それらのフィールドは、TOAST内部用途で使用され、事前に告知せずに変更する場合があるため使用しないでください。

## Floating IP
### Floating IPリスト表示
Floating IPリストを返します。
```
GET /v2.0/floatingips
X-Auth-Token: {tokenId}
```

#### リクエスト
このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| id | Query | UUID | - | 照会するFloating IP ID |
| status | Query | Enum | - | 照会するFloating IPの状態<br>**ACTIVE**：インスタンスに接続<br>**DOWN**：インスタンスに未接続<br>**ERROR**：インスタンスに接続または割り当てに失敗 |
| tenant_id | Query | String | - | 照会するFloating IPのテナントID |
| floating_network_id  | Query | UUID | - | 照会するFloating IPが属している外部ネットワークID |
| fixed_ip_address | Query | String | - | 照会するFloating IPが接続された固定IPアドレス |
| floating_ip_address | Query | String | - | 照会するFloating IPアドレス |
| port_id | Query | UUID | - | 照会するFloating IPが接続されたポートID |
| sort_dir | Query | Enum | - | 照会するFloating IPのソート方向<br>`sort_key`で指定したフィールドを基準にソート<br>**asc**、**desc**のいずれか |
| sort_key | Query | String | - | 照会するFloating IPのソートキー<br>`sort_dir`で指定した方向通りにソート |
| fields | Query | String | - | 照会するFloating IPのフィールド名<br>例) `fields=id&fields=name` |

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| floatingips | Body | Array | Floating IP情報オブジェクトリスト |
| floatingips.floating_network_id | Body | UUID | Floating IPが属している外部ネットワークID |
| floatingips.router_id | Body | UUID | Floating IPが接続されたルーターID |
| floatingips.fixed_ip_address | Body | String | Floating IPが接続された固定IPアドレス |
| floatingips.floating_ip_address | Body | String | Floating IPアドレス|
| floatingips.tenant_id | Body | String | テナントID |
| floatingips.status | Body | Enum | Floating IPの状態<br>**ACTIVE**：インスタンスに接続<br>**DOWN**：インスタンスに未接続<br>**ERROR**：インスタンスに接続または割り当て失敗 |
| floatingips.port_id | Body | UUID | Floating IPが接続されたポートID |
| floatingips.id | Body | UUID | Floating IP ID |

<details><summary>例</summary>
<p>

```json
{
  "floatingips": [
    {
      "floating_network_id": "4b61db01-8183-4540-b2a3-47254a58298d",
      "router_id": null,
      "fixed_ip_address": null,
      "floating_ip_address": "133.186.242.214",
      "tenant_id": "19eeb40d58684543aef29cbb5ebfe8f0",
      "status": "DOWN",
      "port_id": null,
      "id": "fed3fcf6-59b1-4f43-93e5-23a47cb5452e"
    }
  ]
}
```

</p>
</details>

---

### Floating IP表示
指定したFloating IPの情報を返します。
```
GET /v2.0/floatingips/{floatingIpId}
X-Auth-Token: {tokenId}
```

#### リクエスト
このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| floatingIpId | URL | UUID | O | Floating IP ID |
| tokenId | Header | String | O | トークンID |

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| floatingip | Body | Object | Floating IP情報オブジェクト |
| floatingip.floating_network_id | Body | UUID | Floating IPが属している外部ネットワークID |
| floatingip.router_id | Body | UUID | Floating IPが接続されたルーターID |
| floatingip.fixed_ip_address | Body | String | Floating IPが接続された固定IPアドレス |
| floatingip.floating_ip_address | Body | String | Floating IPアドレス |
| floatingip.tenant_id | Body | String | テナントID |
| floatingip.status | Body | Enum | Floating IPの状態<br>**ACTIVE**：インスタンスに接続<br>**DOWN**：インスタンスに未接続<br>**ERROR**：インスタンスに接続または割り当て失敗 |
| floatingip.port_id | Body | UUID | Floating IPが接続されたポートID |
| floatingip.id | Body | UUID | Floating IP ID |

<details><summary>例</summary>
<p>

```json
{
  "floatingip": {
    "floating_network_id": "4b61db01-8183-4540-b2a3-47254a58298d",
    "router_id": null,
    "fixed_ip_address": null,
    "floating_ip_address": "133.186.242.214",
    "tenant_id": "19eeb40d58684543aef29cbb5ebfe8f0",
    "status": "DOWN",
    "port_id": null,
    "id": "fed3fcf6-59b1-4f43-93e5-23a47cb5452e"
  }
}
```

</p>
</details>

---

### Floating IPを作成する
Floating IPを作成します。
```
POST /v2.0/floatingips
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| floatingip | Body | Object | O | Floating IP作成リクエストオブジェクト |
| floatingip.floating_network_id | Body | UUID | O | Floating IPが属している外部ネットワークID |
| floatingip.port_id | Body | UUID | - | Floating IPが接続されるポートID |

<details><summary>例</summary>
<p>

```json
{
  "floatingip": {
    "floating_network_id": "4b61db01-8183-4540-b2a3-47254a58298d",
    "port_id": null
  }
}
```

</p>
</details>

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| floatingip | Body | Object | Floating IP情報オブジェクト |
| floatingip.floating_network_id | Body | UUID | Floating IPが属している外部ネットワークID |
| floatingip.router_id | Body | UUID | Floating IPが接続されたルーターID |
| floatingip.fixed_ip_address | Body | String | Floating IPが接続された固定IPアドレス |
| floatingip.floating_ip_address | Body | String | Floating IPアドレス |
| floatingip.tenant_id | Body | String | テナントID |
| floatingip.status | Body | Enum | Floating IPの状態<br>**ACTIVE**：インスタンスに接続<br>**DOWN**：インスタンスに未接続<br>**ERROR**：インスタンスに接続または割り当て失敗 |
| floatingip.port_id | Body | UUID | Floating IPが接続されたポートID |
| floatingip.id | Body | UUID | Floating IP ID |

<details><summary>例</summary>
<p>

```json
{
  "floatingip": {
    "floating_network_id": "4b61db01-8183-4540-b2a3-47254a58298d",
    "router_id": null,
    "fixed_ip_address": null,
    "floating_ip_address": "133.186.242.214",
    "tenant_id": "19eeb40d58684543aef29cbb5ebfe8f0",
    "status": "DOWN",
    "port_id": null,
    "id": "fed3fcf6-59b1-4f43-93e5-23a47cb5452e"
  }
}
```

</p>
</details>

---

### Floating IPを接続/解除する
```
PUT /v2.0/floatingips/{floatingIpId}
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| floatingIpId | URL | UUID | Floating IP ID |
| tokenId | Header | String | O | トークンID |
| floatingip | Body | Object | O | Floating IP修正リクエストオブジェクト |
| floatingip.port_id | Body | UUID | O | Floating IPを接続するポートID<br>解除するには`null`を入力 |
| floatingip.fixed_ip_address | Body | String | - | 固定IPアドレス<br>接続または解除するポートに複数のIPが割り当てられている場合、特定IPを指定するために使用 |

<details><summary>例</summary>
<p>

```json
{
    "floatingip": {
        "port_id": "af41e9f7-18ae-43c5-8b7e-7026f792bf3a"
    }
}
```

</p>
</details>

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| floatingip | Body | Object | Floating IP情報オブジェクト |
| floatingip.floating_network_id | Body | UUID | Floating IPが属している外部ネットワークID |
| floatingip.router_id | Body | UUID | Floating IPが接続されたルーターID |
| floatingip.fixed_ip_address | Body | String | Floating IPが接続された固定IPアドレス |
| floatingip.floating_ip_address | Body | String | Floating IPアドレス |
| floatingip.tenant_id | Body | String | テナントID |
| floatingip.status | Body | Enum | Floating IPの状態 |
| floatingip.port_id | Body | UUID | Floating IPが接続されたポートID |
| floatingip.id | Body | UUID | Floating IP ID |

<details><summary>例</summary>
<p>

```json
{
  "floatingip": {
    "floating_network_id": "b04b1c31-f2e9-4ae0-a264-02b7d61ad618",
    "router_id": "4337119f-8c72-40bf-818a-21258ecb86db",
    "fixed_ip_address": "192.168.22.96",
    "floating_ip_address": "133.186.147.40",
    "tenant_id": "f5073eaa26b64cffbee89411df94ce01",
    "status": "DOWN",
    "port_id": "af41e9f7-18ae-43c5-8b7e-7026f792bf3a",
    "id": "5338b5b2-9d80-46b5-ba13-2fd13f5c498a"
  }
}
```

</p>
</details>

---

### Floating IPを削除する
指定したFloating IPを削除します。
```
DELETE /v2.0/floatingips/{floatingIpId}
X-Auth-Token: {tokenId}
```

#### リクエスト
このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| floatingIpId | URL | UUID | O | Floating IP ID |
| tokenId | Header | String | O | トークンID |

#### レスポンス
このAPIはレスポンス本文を返しません。

---

## セキュリティグループ
### セキュリティグループリスト表示
```
GET /v2.0/security-groups
X-Auth-Token: {tokenId}
```

#### リクエスト
このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| id | Query | UUID | - | 照会するセキュリティグループID |
| tenant_id | Query | String | - | 照会するセキュリティグループのテナントID |
| name | Query | String | - | 照会するセキュリティグループの名前 |
| sort_dir | Query | Enum | - | 照会するセキュリティグループのソート方向<br>`sort_key`で指定したフィールドを基準にソート<br>**asc**、**desc**のいずれか |
| sort_key | Query | String | - | 照会するセキュリティグループのソートキー<br>`sort_dir`で指定した方向通りにソート |
| fields | Query | String | - | 照会するセキュリティグループのフィールド名<br>例) `fields=id&fields=name` |

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| security_groups | Body | Array | セキュリティグループリストオブジェクト |
| security_groups.tenant_id | Body | String | テナントID |
| security_groups.description | Body | String | セキュリティグループの説明 |
| security_groups.id | Body | UUID | セキュリティグループID |
| security_groups.security_group_rules | Body | Array | セキュリティグループルールリスト |
| security_groups.name | Body | String | セキュリティグループ名 |

<details><summary>例</summary>
<p>

```json
{
  "security_groups": [
    {
      "tenant_id": "19eeb40d58684543aef29cbb5ebfe8f0",
      "description": "Default security group",
      "id": "877b092c-2a31-4509-8d23-deeb02d95633",
      "security_group_rules": [
        {
          "direction": "ingress",
          "protocol": null,
          "description": "",
          "port_range_max": null,
          "id": "0c7279cd-657e-43cd-a635-6886ca3a8acd",
          "remote_group_id": "877b092c-2a31-4509-8d23-deeb02d95633",
          "remote_ip_prefix": null,
          "security_group_id": "877b092c-2a31-4509-8d23-deeb02d95633",
          "tenant_id": "19eeb40d58684543aef29cbb5ebfe8f0",
          "port_range_min": null,
          "ethertype": "IPv4"
        },
        {
          "direction": "egress",
          "protocol": null,
          "description": "",
          "port_range_max": null,
          "id": "4c5ae06e-44f0-4ea9-a999-29473873bca2",
          "remote_group_id": null,
          "remote_ip_prefix": null,
          "security_group_id": "877b092c-2a31-4509-8d23-deeb02d95633",
          "tenant_id": "19eeb40d58684543aef29cbb5ebfe8f0",
          "port_range_min": null,
          "ethertype": "IPv6"
        },
        {
          "direction": "egress",
          "protocol": null,
          "description": "",
          "port_range_max": null,
          "id": "4e21389a-bb3c-469c-8139-29da582bc3c5",
          "remote_group_id": null,
          "remote_ip_prefix": null,
          "security_group_id": "877b092c-2a31-4509-8d23-deeb02d95633",
          "tenant_id": "19eeb40d58684543aef29cbb5ebfe8f0",
          "port_range_min": null,
          "ethertype": "IPv4"
        },
        {
          "direction": "ingress",
          "protocol": null,
          "description": "",
          "port_range_max": null,
          "id": "72af180f-c425-4ec4-b7a3-90f86d4ce145",
          "remote_group_id": "877b092c-2a31-4509-8d23-deeb02d95633",
          "remote_ip_prefix": null,
          "security_group_id": "877b092c-2a31-4509-8d23-deeb02d95633",
          "tenant_id": "19eeb40d58684543aef29cbb5ebfe8f0",
          "port_range_min": null,
          "ethertype": "IPv6"
        }
      ],
      "name": "default"
    }
  ]
}
```

</p>
</details>

---
