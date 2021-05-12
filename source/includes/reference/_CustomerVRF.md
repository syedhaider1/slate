# Customer VRF

## Get Customer VRF

```python
import NaaS

api = NaaS.authorize('authKey')
api.edge.vrfs.get(2)
```

```shell
curl "http://naas01-dev.idc1.level3.com:8557/api/v1/edge/vrfs?accountId=1234&accountType=ENTERPRISE_ID&vrfType=PRIVATE"
  -H "Authorization: authKey"
```

> The above command returns JSON structured like this:

```json
{
  "operation": "string",
  "requestDate": "string",
  "resourceId": "string",
  "status": "string"
}
```

This endpoint is used to retrieve a customer's existing VRF. This information will be used in subsequent calls to NaaS to connect the customer's VPN to the edge compute environment. This information is retrieved from inventory.

The API requires an input that helps it understand where the VRF details will come from. This parameter can be ENTERPRISE_ID, BIZ_ORG_ID, or BAN

The vrfType defines whether this VRF is connected to a public cloud instance or the customer's private sites.

### HTTP Request

`GET http://naas01-dev.idc1.level3.com:8557/api/v1/edge/vrfs`

### URL Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
accountId | String | True | Customer's account ID
accountType | String | True | Account lookup type. ENTERPRISE_ID, BIZ_ORG_ID, BAN
vrfType | String | True | PUBLIC or PRIVATE

## Post New Customer VRF

```python
import NaaS

api = NaaS.authorize('authKey')
api.edge.vrfs.post(2)
```

```shell
curl "http://naas01-dev.idc1.level3.com:8557/api/v1/edge/vrfs?accountId=<ID>"
  -X POST
  -d '{json}'
  -H "Authorization: authKey"
```

> Request payload JSON is structured like this:

```json
{
  "enterpriseId": "string",
  "vrfType": "PUBLIC",
  "customer": {
    "name": "string"
  }
}
```

> The above command returns JSON structured like this:

```json
{
  "requestStatusUrl": "string",
  "resourceId": "string"
}
```

This endpoint is used to create a customer VRF in inventory systems. This VRF will then be used to connect a VPN endpoint in the edge compute network components.

<aside class="notice">
This endpoint provides an asynchronous response. The synchronous response includes a URL that the caller can poll for status
</aside>

### HTTP Request

`POST http://naas01-dev.idc1.level3.com:8557/api/v1/edge/vrfs`

### URL Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
accountId | String | True | Customer account ID

## Get VRF Request Status

```python
import NaaS

api = NaaS.authorize('authKey')
api.edge.vrfs.get(2)
```

```shell
curl "http://naas01-dev.idc1.level3.com:8557/api/v1/edge/vrfs/{resourceId}?accountId=<accountID>&resourceId=<resourceId>"
  -H "Authorization: authKey"
```

> The above command returns JSON structured like this:

```json
{
  "operation": "string",
  "requestDate": "string",
  "resourceId": "string",
  "status": "string"
}
```

This endpoint is used to retrieve status of the asynchronous Post New Customer VRF request. This will provide up to date status of the on-going request. 

### HTTP Request

`GET http://naas01-dev.idc1.level3.com:8557/api/v1/edge/vrfs/{resourceId}`

### URL Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
accountId | String | True | Customer's account iD
resourceId | String | True | resourceId provided in response to Post New Customer VRF request

