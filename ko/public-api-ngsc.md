## Network > Floating IP > API v2 가이드

API를 사용하려면 API 엔드포인트와 토큰 등이 필요합니다. [API 사용 준비](/Compute/Compute/ko/identity-api-gov/)를 참고하여 API 사용에 필요한 정보를 준비합니다.

플로팅 IP API는 `network` 타입 엔드포인트를 이용합니다. 정확한 엔드포인트는 토큰 발급 응답의 `serviceCatalog`를 참조합니다.

| 타입 | 리전 | 엔드포인트 |
|---|---|---|
| network | 한국(판교) 리전 | https://kr1-api-network-infrastructure.gov-nhncloudservice.com |

API 응답에 가이드에 명시되지 않은 필드가 나타날 수 있습니다. 이런 필드는 NHN Cloud 내부 용도로 사용되며 사전 공지 없이 변경될 수 있으므로 사용하지 않습니다.

## 플로팅 IP
### 플로팅 IP 목록 보기
플로팅 IP 목록을 반환합니다.
```
GET /v2.0/floatingips
X-Auth-Token: {tokenId}
```

#### 요청
이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| id | Query | UUID | - | 조회할 플로팅 IP ID |
| status | Query | Enum | - | 조회할 플로팅 IP의 상태<br>**ACTIVE**: 인스턴스에 연결<br>**DOWN**: 인스턴스에 미연결<br>**ERROR**: 인스턴스에 연결 또는 할당 실패 |
| tenant_id | Query | String | - | 조회할 플로팅 IP의 테넌트 ID |
| floating_network_id  | Query | UUID | - | 조회할 플로팅 IP가 속한 외부 네트워크 ID |
| fixed_ip_address | Query | String | - | 조회할 플로팅 IP가 연결된 고정 IP 주소 |
| floating_ip_address | Query | String | - | 조회할 플로팅 IP 주소 |
| port_id | Query | UUID | - | 조회할 플로팅 IP가 연결된 포트 ID |
| sort_dir | Query | Enum | - | 조회할 플로팅 IP의 정렬 방향<br>`sort_key`에서 지정한 필드를 기준으로 정렬<br>**asc**, **desc** 중 하나 |
| sort_key | Query | String | - | 조회할 플로팅 IP의 정렬 키<br>`sort_dir`에서 지정한 방향대로 정렬 |
| fields | Query | String | - | 조회할 플로팅 IP의 필드 이름<br>예) `fields=id&fields=name` |

#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| floatingips | Body | Array | 플로팅 IP 정보 객체 목록 |
| floatingips.floating_network_id | Body | UUID | 플로팅 IP가 속한 외부 네트워크 ID |
| floatingips.router_id | Body | UUID | 플로팅 IP가 연결된 라우터 ID |
| floatingips.fixed_ip_address | Body | String | 플로팅 IP가 연결된 고정 IP 주소 |
| floatingips.floating_ip_address | Body | String | 플로팅 IP 주소|
| floatingips.tenant_id | Body | String | 테넌트 ID |
| floatingips.status | Body | Enum | 플로팅 IP의 상태<br>**ACTIVE**: 인스턴스에 연결<br>**DOWN**: 인스턴스에 미연결<br>**ERROR**: 인스턴스에 연결 또는 할당 실패 |
| floatingips.port_id | Body | UUID | 플로팅 IP가 연결된 포트 ID |
| floatingips.id | Body | UUID | 플로팅 IP ID |

<details><summary>예시</summary>
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

### 플로팅 IP 보기
지정한 플로팅 IP에 대한 정보를 반환합니다.
```
GET /v2.0/floatingips/{floatingIpId}
X-Auth-Token: {tokenId}
```

#### 요청
이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| floatingIpId | URL | UUID | O | 플로팅 IP ID |
| tokenId | Header | String | O | 토큰 ID |

#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| floatingip | Body | Object | 플로팅 IP 정보 객체 |
| floatingip.floating_network_id | Body | UUID | 플로팅 IP가 속한 외부 네트워크 ID |
| floatingip.router_id | Body | UUID | 플로팅 IP가 연결된 라우터 ID |
| floatingip.fixed_ip_address | Body | String | 플로팅 IP가 연결된 고정 IP 주소 |
| floatingip.floating_ip_address | Body | String | 플로팅 IP 주소 |
| floatingip.tenant_id | Body | String | 테넌트 ID |
| floatingip.status | Body | Enum | 플로팅 IP의 상태<br>**ACTIVE**: 인스턴스에 연결<br>**DOWN**: 인스턴스에 미연결<br>**ERROR**: 인스턴스에 연결 또는 할당 실패 |
| floatingip.port_id | Body | UUID | 플로팅 IP가 연결된 포트 ID |
| floatingip.id | Body | UUID | 플로팅 IP ID |

<details><summary>예시</summary>
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

### 플로팅 IP 생성하기
플로팅 IP를 생성합니다.
```
POST /v2.0/floatingips
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| floatingip | Body | Object | O | 플로팅 IP 생성 요청 객체 |
| floatingip.floating_network_id | Body | UUID | O | 플로팅 IP가 속한 외부 네트워크 ID |
| floatingip.port_id | Body | UUID | - | 플로팅 IP가 연결될 포트 ID |

<details><summary>예시</summary>
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

#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| floatingip | Body | Object | 플로팅 IP 정보 객체 |
| floatingip.floating_network_id | Body | UUID | 플로팅 IP가 속한 외부 네트워크 ID |
| floatingip.router_id | Body | UUID | 플로팅 IP가 연결된 라우터 ID |
| floatingip.fixed_ip_address | Body | String | 플로팅 IP가 연결된 고정 IP 주소 |
| floatingip.floating_ip_address | Body | String | 플로팅 IP 주소 |
| floatingip.tenant_id | Body | String | 테넌트 ID |
| floatingip.status | Body | Enum | 플로팅 IP의 상태<br>**ACTIVE**: 인스턴스에 연결<br>**DOWN**: 인스턴스에 미연결<br>**ERROR**: 인스턴스에 연결 또는 할당 실패 |
| floatingip.port_id | Body | UUID | 플로팅 IP가 연결된 포트 ID |
| floatingip.id | Body | UUID | 플로팅 IP ID |

<details><summary>예시</summary>
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

### 플로팅 IP 연결/해제하기
```
PUT /v2.0/floatingips/{floatingIpId}
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| floatingIpId | URL | UUID | 플로팅 IP ID |
| tokenId | Header | String | O | 토큰 ID |
| floatingip | Body | Object | O | 플로팅 IP 수정 요청 객체 |
| floatingip.port_id | Body | UUID | O | 플로팅 IP를 연결할 포트 ID<br>해제하려면 `null`을 입력 |
| floatingip.fixed_ip_address | Body | String | - | 고정 IP 주소<br>연결 또는 해제할 포트에 여러 IP가 할당된 경우, 특정 IP를 지정하기 위해 사용 |

<details><summary>예시</summary>
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

#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| floatingip | Body | Object | 플로팅 IP 정보 객체 |
| floatingip.floating_network_id | Body | UUID | 플로팅 IP가 속한 외부 네트워크 ID |
| floatingip.router_id | Body | UUID | 플로팅 IP가 연결된 라우터 ID |
| floatingip.fixed_ip_address | Body | String | 플로팅 IP가 연결된 고정 IP 주소 |
| floatingip.floating_ip_address | Body | String | 플로팅 IP 주소 |
| floatingip.tenant_id | Body | String | 테넌트 ID |
| floatingip.status | Body | Enum | 플로팅 IP의 상태 |
| floatingip.port_id | Body | UUID | 플로팅 IP가 연결된 포트 ID |
| floatingip.id | Body | UUID | 플로팅 IP ID |

<details><summary>예시</summary>
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

### 플로팅 IP 삭제하기
지정한 플로팅 IP를 삭제합니다.
```
DELETE /v2.0/floatingips/{floatingIpId}
X-Auth-Token: {tokenId}
```

#### 요청
이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| floatingIpId | URL | UUID | O | 플로팅 IP ID |
| tokenId | Header | String | O | 토큰 ID |

#### 응답
이 API는 응답 본문을 반환하지 않습니다.

---
