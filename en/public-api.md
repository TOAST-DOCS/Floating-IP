<!-- pre-align:aligned sig=1afc401bf75b -->

<a id="network-floating-ip-api-v2-guide"></a>
## Network > Floating IP > API v2 Guide { #network-floating-ip-api-v2-guide }

NHN Cloud Network services use IaaS tokens for authentication and authorization when making API calls. The IaaS token is an authentication token used for NHN Cloud's OpenStack-based infrastructure services (IaaS). For more information on issuing and using IaaS tokens, please refer to the [IaaS Token](/nhncloud/en/public-api/iaas-token).

Floating IP API uses the `network`-type endpoint. To see the exact endpoint, refer to `serviceCatalog` of the token issuance response.

| Type | Region | Endpoint |
|---|---|---|
| network | Korea(Pangyo) Region<br>Korea(Pyeongchon) Region<br>Korea(Gwangju) Region<br>Japan(Tokyo) Region | https://kr1-api-network-infrastructure.nhncloudservice.com<br>https://kr2-api-network-infrastructure.nhncloudservice.com<br>https://kr3-api-network-infrastructure.nhncloudservice.com<br>https://jp1-api-network-infrastructure.nhncloudservice.com |

API response may show the fields not specified by the guide. These fields are internally used by NHN Cloud, and not used because they are subject to change without prior notice.

<a id="floating-ip"></a>
## Floating IP { #floating-ip }

<a id="view-external-network-id"></a>
### View External Network ID { #view-external-network-id }
You must specify the ID of an external network when creating a floating IP, because the external network assigns floating IPs.
The available external networks can be retrieved by specifying the query `router:external=true` in the [View VPC List](/Network/VPC/en/public-api/#vpc_1).
```
GET /v2.0/vpcs?router:external=true
```

<a id="view-the-list-of-floating-ips"></a>
### View the list of floating IPs { #view-the-list-of-floating-ips }
Returns the list of floating IPs.
```
GET /v2.0/floatingips
X-Auth-Token: {tokenId}
```

<a id="view-the-list-of-floating-ips-request"></a>
#### Request
This API does not require a request body.

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| id | Query | UUID | - | Floating IP ID to view |
| status | Query | Enum | - | Status of the floating IP to view<br>**ACTIVE**: Connected to an instance<br>**DOWN**: Not connected to an instance<br>**ERROR**: Failed to connect or assign to an instance |
| tenant_id | Query | String | - | Tenant ID of the floating IP to view |
| floating_network_id  | Query | UUID | - | External network ID which includes the floating IP to view |
| fixed_ip_address | Query | String | - | Fixed IP address connected with the floating IP to view |
| floating_ip_address | Query | String | - | Floating IP Address to view |
| port_id | Query | UUID | - | Port ID connected with the floating IP to view |
| delete_protection | Query | Boolean | - | Whether to set delete protection | 
| label | Query | String | - | Label |
| sort_dir | Query | Enum | - | Sort direction of the floating IP to view<br>`Sorted by the field specified by sort_key`<br>**asc** or **desc** |
| sort_key | Query | String | - | Sort key of the floating IP to view<br>`Sorted in the direction specified by sort_dir` |
| fields | Query | String | - | Field name of the floating IP to view<br>e.g.) `fields=id&fields=name` |

<a id="view-the-list-of-floating-ips-response"></a>
#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| floatingips | Body | Array | Floating IP information object list |
| floatingips.floating_network_id | Body | UUID | External network ID which includes the floating IP |
| floatingips.router_id | Body | UUID | Router ID connected with the floating IP |
| floatingips.fixed_ip_address | Body | String | Fixed IP address connected with the floating IP |
| floatingips.floating_ip_address | Body | String | Floating IP Address|
| floatingips.tenant_id | Body | String | Tenant ID |
| floatingips.status | Body | Enum | Status of the floating IP<br>**ACTIVE**: Connected to an instance<br>**DOWN**: Not connected to an instance<br>**ERROR**: Failed to connect or assign to an instance |
| floatingips.port_id | Body | UUID | Port ID connected with the floating IP |
| floatingips.id | Body | UUID | Floating IP ID |
| floatingips.delete_protection | Body | Boolean | Whether to set delete protection |
| floatingips.label | Body | String | Label |

<details><summary>Example</summary>
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
      "id": "fed3fcf6-59b1-4f43-93e5-23a47cb5452e",
      "delete_protection": true,
      "label": "LABEL"
    }
  ]
}
```

</p>
</details>

---

<a id="see-the-floating-ip"></a>
### See the floating IP { #see-the-floating-ip }
Returns the information about the specified floating IP.
```
GET /v2.0/floatingips/{floatingIpId}
X-Auth-Token: {tokenId}
```

<a id="see-the-floating-ip-request"></a>
#### Request
This API does not require a request body.

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| floatingIpId | URL | UUID | O | Floating IP ID |
| tokenId | Header | String | O | Token ID |

<a id="see-the-floating-ip-response"></a>
#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| floatingip | Body | Object | Floating IP information object |
| floatingip.floating_network_id | Body | UUID | External network ID which includes the floating IP |
| floatingip.router_id | Body | UUID | Router ID connected with the floating IP |
| floatingip.fixed_ip_address | Body | String | Fixed IP address connected with the floating IP |
| floatingip.floating_ip_address | Body | String | Floating IP Address |
| floatingip.tenant_id | Body | String | Tenant ID |
| floatingip.status | Body | Enum | Status of the floating IP<br>**ACTIVE**: Connected to an instance<br>**DOWN**: Not connected to an instance<br>**ERROR**: Failed to connect or assign to an instance |
| floatingip.port_id | Body | UUID | Port ID connected with the floating IP |
| floatingip.id | Body | UUID | Floating IP ID |
| floatingip.delete_protection | Body | Boolean | Whether to set delete protection |
| floatingip.label | Body | String | Label |

<details><summary>Example</summary>
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
    "id": "fed3fcf6-59b1-4f43-93e5-23a47cb5452e",
    "delete_protection": true,
    "label": "LABEL"
  }
}
```

</p>
</details>

---

<a id="creating-a-floating-ip"></a>
### Creating a floating IP { #creating-a-floating-ip }
Creates a floating IP.
```
POST /v2.0/floatingips
X-Auth-Token: {tokenId}
```

<a id="creating-a-floating-ip-request"></a>
#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| floatingip | Body | Object | O | Object requesting creation of floating IP |
| floatingip.floating_network_id | Body | UUID | O | External network ID which includes the floating IP (same as the VPC ID of 'Public Network') |
| floatingip.port_id | Body | UUID | - | Port ID to connect the floating IP |
| floatingip.delete_protection | Body | Boolean | - | Whether to set delete protection. Default **false** |
| floatingip.label | Body | String | - | Label |

<details><summary>Example</summary>
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

<a id="creating-a-floating-ip-response"></a>
#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| floatingip | Body | Object | Floating IP information object |
| floatingip.floating_network_id | Body | UUID | External network ID which includes the floating IP |
| floatingip.router_id | Body | UUID | Router ID connected with the floating IP |
| floatingip.fixed_ip_address | Body | String | Fixed IP address connected with the floating IP |
| floatingip.floating_ip_address | Body | String | Floating IP Address |
| floatingip.tenant_id | Body | String | Tenant ID |
| floatingip.status | Body | Enum | Status of the floating IP<br>**ACTIVE**: Connected to an instance<br>**DOWN**: Not connected to an instance<br>**ERROR**: Failed to connect or assign to an instance |
| floatingip.port_id | Body | UUID | Port ID connected with the floating IP |
| floatingip.id | Body | UUID | Floating IP ID |
| floatingip.delete_protection | Body | Boolean | Whether to set delete protection |
| floatingip.label | Body | String | Label |


<details><summary>Example</summary>
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
    "id": "fed3fcf6-59b1-4f43-93e5-23a47cb5452e",
    "delete_protection": true,
    "label": "LABEL"
  }
}
```

</p>
</details>

---

<a id="change-floating-ip"></a>
### Change Floating IP { #change-floating-ip }
```
PUT /v2.0/floatingips/{floatingIpId}
X-Auth-Token: {tokenId}
```

<a id="change-floating-ip-request"></a>
#### Request
| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| floatingIpId | URL | UUID | Floating IP ID |
| tokenId | Header | String | O | Token ID |
| floatingip | Body | Object | O | Floating IP modification request object |
| floatingip.delete_protection | Body | Boolean | - | Whether to set delete protection |
| floatingip.label | Body | String | - | Label |

<details><summary>Example</summary>
<p>

```json
{
    "floatingip": {
        "delete_protection": true,
        "label": "LABEL"
    }
}
```

</p>
</details>

<a id="change-floating-ip-response"></a>
#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| floatingip | Body | Object | Floating IP information object |
| floatingip.floating_network_id | Body | UUID | External network ID which includes the floating IP |
| floatingip.router_id | Body | UUID | Router ID connected with the floating IP |
| floatingip.fixed_ip_address | Body | String | Fixed IP address connected with the floating IP |
| floatingip.floating_ip_address | Body | String | Floating IP Address |
| floatingip.tenant_id | Body | String | Tenant ID |
| floatingip.status | Body | Enum | Status of the floating IP |
| floatingip.port_id | Body | UUID | Port ID connected with the floating IP |
| floatingip.id | Body | UUID | Floating IP ID |
| floatingip.delete_protection | Body | Boolean | Whether to set delete protection |
| floatingip.label | Body | String | Label |

<details><summary>Example</summary>
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
    "id": "5338b5b2-9d80-46b5-ba13-2fd13f5c498a",
    "delete_protection": true,
    "label": "LABEL"
  }
}
```

</p>
</details>

---

<a id="connectdisconnect-a-floating-ip"></a>
### Connect/disconnect a floating IP { #connectdisconnect-a-floating-ip }
```
PUT /v2.0/floatingips/{floatingIpId}
X-Auth-Token: {tokenId}
```

<a id="connectdisconnect-a-floating-ip-request"></a>
#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| floatingIpId | URL | UUID | Floating IP ID |
| tokenId | Header | String | O | Token ID|
| floatingip | Body | Object | O | Object requesting modification of floating IP |
| floatingip.port_id | Body | UUID | O | Port ID to connect a floating IP<br>To disconnect, enter `null` |
| floatingip.fixed_ip_address | Body | String | - | Fixed IP address<br>Used to specify an IP if multiple IPs are assigned to the port for connection or disconnection |

<details><summary>Example</summary>
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

<a id="connectdisconnect-a-floating-ip-response"></a>
#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| floatingip | Body | Object | Floating IP information object |
| floatingip.floating_network_id | Body | UUID | External network ID which includes the floating IP |
| floatingip.router_id | Body | UUID | Router ID connected with the floating IP |
| floatingip.fixed_ip_address | Body | String | Fixed IP address connected with the floating IP |
| floatingip.floating_ip_address | Body | String | Floating IP Address |
| floatingip.tenant_id | Body | String | Tenant ID |
| floatingip.status | Body | Enum | Floating IP status |
| floatingip.port_id | Body | UUID | Port ID connected with the floating IP |
| floatingip.id | Body | UUID | Floating IP ID |
| floatingip.delete_protection | Body | Boolean | Whether to set delete protection |
| floatingip.label | Body | String | Label |

<details><summary>Example</summary>
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
    "id": "5338b5b2-9d80-46b5-ba13-2fd13f5c498a",
    "delete_protection": true,
    "label": "LABEL"
  }
}
```

</p>
</details>

---

<a id="deleting-a-floating-ip"></a>
### Deleting a floating IP { #deleting-a-floating-ip }
Deletes the specified floating IP.
```
DELETE /v2.0/floatingips/{floatingIpId}
X-Auth-Token: {tokenId}
```

<a id="deleting-a-floating-ip-request"></a>
#### Request
This API does not require a request body.

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| floatingIpId | URL | UUID | O | Floating IP ID |
| tokenId | Header | String | O | Token ID |

<a id="deleting-a-floating-ip-response"></a>
#### Response
This API does not return a response body.

---

