## Network > Floating IP > API v2 Guide

To enable APIs, API endpoint and token are required. Prepare information required to enable an API, in reference of  [Preparing for APIs](/Compute/Compute/ko/identity-api/)

Use  `network`- type endpoint for Floating IPs API. For more details, see `serviceCatalog` from response of token issuance. 

| Type | Region | Endpoint |
|---|---|---|
| Network | Korea (Pangyo)<br>Japan | https://kr1-api-network.infrastructure.cloud.toast.com<br>https://jp1-api-network.infrastructure.cloud.toast.com |

In API response, you may find fields that are not specified in the guide. Refrain from using them because such fields are only for the TOAST internal usage and might be changed without previous notice. 

## Floating IP
### List Floating IPs 
Return the list of floating IPs. 
```
GET /v2.0/floatingips
X-Auth-Token: {tokenId}
```

#### Request
This API does not require a request body. 

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| id | Query | UUID | - | ID of floating IP to query |
| status | Query | Enum | - | Status of floating IP to query <br>**ACTIVE**: Attached to instance <br>**DOWN**: Not attached to instance <br>**ERROR**: Attached to instance or failed in assignment |
| tenant_id | Query | String | - | Tenant ID of floating IP to query |
| floating_network_id  | Query | UUID | - | ID of external network including floating IP to query |
| fixed_ip_address | Query | String | - | Fixed IP address associated with floating IP to query |
| floating_ip_address | Query | String | - | Address of floating IP to query |
| port_id | Query | UUID | - | ID of port attached with floating IP to query |
| sort_dir | Query | Enum | - | Sorting direction of floating IP to query <br>Sort by the field as specified by `sort_key`<br>Either **asc**, or **desc** |
| sort_key | Query | String | - | Sorting key of floating IP to query<br>Sort in the direction as specified by `sort_dir` |
| fields | Query | String | - | Field name of floating IP to query <br>e.g.) `fields=id&fields=name` |

#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| floatingips | Body | Array | List of information objects of floating IP |
| floatingips.floating_network_id | Body | UUID | ID of external network including floating IP |
| floatingips.router_id | Body | UUID | ID of router attached with floating IP |
| floatingips.fixed_ip_address | Body | String | Fixed IP address associated with floating IP |
| floatingips.floating_ip_address | Body | String | Floating IP address |
| floatingips.tenant_id | Body | String | Tenant ID |
| floatingips.status | Body | Enum | Status of floating IP <br>**ACTIVE**: Attached to instance <br>**DOWN**: Not attached to instance <br>**ERROR**: Attached to instance or failed in assignment |
| floatingips.port_id | Body | UUID | ID of port attached with floating IP |
| floatingips.id | Body | UUID | ID of floating IP |

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

### Get Floating IP
Return information for specified floating IP. 
```
GET /v2.0/floatingips/{floatingIpId}
X-Auth-Token: {tokenId}
```

#### Request
This API does not require a request body.

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| floatingIpId | URL | UUID | O | ID of floating IP |
| tokenId | Header | String | O | Token ID |

#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| floatingip | Body | Object | Information object of floating IP |
| floatingip.floating_network_id | Body | UUID | ID of external network including floating IP |
| floatingip.router_id | Body | UUID | ID of router attached with floating IP |
| floatingip.fixed_ip_address | Body | String | Fixed IP address associated with floating IP |
| floatingip.floating_ip_address | Body | String | Floating iP address |
| floatingip.tenant_id | Body | String | Tenant ID |
| floatingip.status | Body | Enum | Status of floating IP<br>**ACTIVE**: Attached to instance <br>**DOWN**: Not attached to instance<br>**ERROR**: Attached to instance or failed in assignment |
| floatingip.port_id | Body | UUID | ID of port attached with floating IP |
| floatingip.id | Body | UUID | ID of floating IP |

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

### Create Floating IP
Create a floating IP. 
```
POST /v2.0/floatingips
X-Auth-Token: {tokenId}
```

#### Request	

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| floatingip | Body | Object | O | Object requesting of creating floating IP |
| floatingip.floating_network_id | Body | UUID | O | ID of external network including floating IP |
| floatingip.port_id | Body | UUID | - | ID of port to be attached with floating IP |

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
| floatingip | Body | Object | Information object of floating IP |
| floatingip.floating_network_id | Body | UUID | ID of external network including floating IP |
| floatingip.router_id | Body | UUID | ID of router attached to floating IP |
| floatingip.fixed_ip_address | Body | String | Fixed IP address associated with floating IP |
| floatingip.floating_ip_address | Body | String | Floating IP address |
| floatingip.tenant_id | Body | String | Tenant ID |
| floatingip.status | Body | Enum | Status of floating IP <br>**ACTIVE**: Attached to instance <br>**DOWN**: Not attached to instance <br>**ERROR**: Attached to instance or failed in assignment |
| floatingip.port_id | Body | UUID | Port ID associated with floating IP |
| floatingip.id | Body | UUID | ID of floating IP |

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

### Attach/Detach Floating IP
```
PUT /v2.0/floatingips/{floatingIpId}
X-Auth-Token: {tokenId}
```

#### Request

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| floatingIpId | URL | UUID | ID of floating IP ||
| tokenId | Header | String | O | Token ID |
| floatingip | Body | Object | O | Object requesting of modifying floating IP |
| floatingip.port_id | Body | UUID | O | ID of port to be attached to floating IP<br>Enter `null` to detach |
| floatingip.fixed_ip_address | Body | String | - | Fixed IP address<br>To specify a particular IP, if many IPs are assigned to a port to be attached or detached |

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
| floatingip | Body | Object | Information object of floating IP |
| floatingip.floating_network_id | Body | UUID | ID of external network including floating IP |
| floatingip.router_id | Body | UUID | ID of router attached to floating IP |
| floatingip.fixed_ip_address | Body | String | Fixed IP address associated with floaitng IP |
| floatingip.floating_ip_address | Body | String | Floating IP address |
| floatingip.tenant_id | Body | String | Tenant ID |
| floatingip.status | Body | Enum | Status of floating IP |
| floatingip.port_id | Body | UUID | ID of port attached to floating IP |
| floatingip.id | Body | UUID | ID of floating IP |

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

### Delete Floating IP
Delete a specified floating IP. 
```
DELETE /v2.0/floatingips/{floatingIpId}
X-Auth-Token: {tokenId}
```

#### Request
This API does not require a request body.

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| floatingIpId | URL | UUID | O | ID of floating IP |
| tokenId | Header | String | O | Token ID |

#### Response
This API does not return a response body. 

---

## Security Group
### List Security Groups 
```
GET /v2.0/security-groups
X-Auth-Token: {tokenId}
```

#### Request
This API does not require a request body. 

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| tokenId | Header | String | O | Token ID |
| id | Query | UUID | - | Security group ID to query |
| tenant_id | Query | String | - | Tenant ID of security group to query |
| name | Query | String | - | Name of security group to query |
| sort_dir | Query | Enum | - | Sorting direction of security group to query <br>Sort by the field specified by`sort_key`<br>Either **asc** or **desc** |
| sort_key | Query | String | - | Sorting key of security group to query <br>Sort in the direction as specified by`sort_dir` |
| fields | Query | String | - | Field name of security group to query <br>e.g.) `fields=id&fields=name` |

#### Response

| Name | Type | Format | Description |
|---|---|---|---|
| security_groups | Body | Array | Object of security group list |
| security_groups.tenant_id | Body | String | Tenant ID |
| security_groups.description | Body | String | Description of security group |
| security_groups.id | Body | UUID | Security group ID |
| security_groups.security_group_rules | Body | Array | List of security group rules |
| security_groups.name | Body | String | Security group name |

<details><summary>Example</summary>
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
