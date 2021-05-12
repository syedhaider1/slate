# IP Addresses

NaaS provides a mechanism to assign, and release IP address blocks in Incognito. The requested block must be smaller than a /24 per the current network and IP Addressing design

## Create New IP Block

```python
import NaaS

api = NaaS.authorize('authKey')
api.ips.post(2)
```

```shell
curl "http://naas01-dev.idc1.level3.com:8557/api/v1/ips?accountId=<ID>"
  -X POST
  -d '{json}'
  -H "Authorization: authKey"
```

> Request payload JSON is structured like this:

```json
{
  "productType": "either EDGE_COMPUTE_IPVPN, or EDGE_COMPUTE_INTERNET",
  "clliCode": "string",
  "cidr": "must be a number between 8 and 32",
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

This endpoint is used to resere a block of IP addresses in incognito per site. The caller must provide the type of service, the location clli, and the size of the ip block. NaaS will then retrieve the appropriate sized block from Incognito, and assign that block to the customer entity. If the customer entity does not exist, then NaaS will direct Incognito to create that entity, and then associate to the reserved IP block.

### HTTP Request

`POST http://naas01-dev.idc1.level3.com:8557/api/v1/ips`

### URL Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
accountId | String | True | Customer's account iD

## Delete Existing IP Block

```python
import NaaS

api = NaaS.authorize('authKey')
api.ips.delete(2)
```

```shell
curl "http://naas01-dev.idc1.level3.com:8557/api/v1/ips/{resourceId}?accountId=<ID>"
  -X DELETE
  -H "Authorization: authKey"
```

> The above command returns JSON structured like this:

```json
{
  "requestStatusUrl": "string",
  "resourceId": "string"
}
```

This endpoint is used to delete an IP address block reservation in Incognito. The resourceId provides the ID of the original request that was used to reserve the IP block. This is used by NaaS to identify what to clean up in Incognito.

### HTTP Request

`DELETE http://naas01-dev.idc1.level3.com:8557/api/v1/ips/{resourceId}`

### URL Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
accountId | String | True | Customer's account iD
resourceId | String | True | resourceId provided in response to Post New IP Address request