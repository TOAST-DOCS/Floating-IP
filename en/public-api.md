## Network > Floating IP > API v2 Guide

Using the API requires API endpoint, token, etc. Please refer to [Getting ready to use API](/Compute/Compute/en/identity-api/) to prepare information required for using the API.

Floating IP API uses the `network`-type endpoint. To see the exact endpoint, refer to `serviceCatalog` of the token issuance response.

| Type | Region | Endpoint |
|---|---|---|
| network | Korea(Pangyo) Region<br>Korea(Pyeongchon) Region<br>Japan region | https://kr1-api-network.infrastructure.nhncloudservice.com<br>https://kr2-api-network.infrastructure.nhncloudservice.com<br>https://jp1-api-network.infrastructure.nhncloudservice.com |

API response may show the fields not specified by the guide. These fields are internally used by NHN Cloud, and not used because they are subject to change without prior notice.

## Floating IP
### View the list of floating IPs
Returns the list of floating IPs.
```
GET /v2.0/floatingips
X-Auth-Token: {tokenId}
```

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
| sort_dir | Query | Enum | - | Sort direction of the floating IP to view<br>`Sorted by the field specified by sort_key`<br>**asc** or **desc** |
| sort_key | Query | String | - | Sort key of the floating IP to view<br>`Sorted in the direction specified by sort_dir` |
| fields | Query | String | - | Field name of the floating IP to view<br>e.g.) `fields=id&fields=name` |

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
      "id": "fed3fcf6-59b1-4f43-93e5-23a47cb5452e"
    }
  ]
}
```

</p>
</details>

---

### See the floating IP
Returns the information about the specified floating IP.
```
GET /v2.0/floatingips/{floatingIpId}
X-Auth-Token: {tokenId}
```

#### Request
This API does not require a request body.

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| floatingIpId | URL | UUID | O | Floating IP ID |
| tokenId | Header | String | O | Token ID |

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
    "id": "fed3fcf6-59b1-4f43-93e5-23a47cb5452e"
  }
}
```

</p>
</details>

---

### Creating a floating IP
Creates a floating IP.
```
POST /v2.0/floatingips
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| floatingip | Body | Object | O | Object requesting creation of floating IP |
| floatingip.floating_network_id | Body | UUID | O | External network ID which includes the floating IP |
| floatingip.port_id | Body | UUID | - | Port ID to connect the floating IP |

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
    "id": "fed3fcf6-59b1-4f43-93e5-23a47cb5452e"
  }
}
```

</p>
</details>

---

### Connect/disconnect a floating IP
```
PUT /v2.0/floatingips/{floatingIpId}
X-Auth-Token: {tokenId}
```

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
    "id": "5338b5b2-9d80-46b5-ba13-2fd13f5c498a"
  }
}
```

</p>
</details>

---

### Deleting a floating IP
Deletes the specified floating IP.
```
DELETE /v2.0/floatingips/{floatingIpId}
X-Auth-Token: {tokenId}
```

#### Request
This API does not require a request body.

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| floatingIpId | URL | UUID | O | Floating IP ID |
| tokenId | Header | String | O | Token ID |

#### Response
This API does not return a response body.

---

