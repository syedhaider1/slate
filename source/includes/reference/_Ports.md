# Ports

The Ports endpoint is used to apply customer details to the relevant leaf switch ports, and to remove the build network from the port.

<aside class="notice">
This endpoint provides an asynchronous response for POST and DELETE requests. The synchronous response includes a URL that the caller can poll for status
</aside>

## POST HTTP Request

```shell
 curl "http://naas-prod1.idc1.level3.com:8557/api/v1/edge/leaf/ports?accountId={accountId}"
  -X POST
  -d '{json}'
```
```json
  {
    "requestId": "41c998be-0de3-4cb2-8931-9fc3aecb1e6c",
    "customer": {
      "name": "string"
    },
    "serviceId": "CO/IRXX/12345/LVLC",
    "productType": "string",
    "serverHostnames": [
      "MINERLABBM010201"
    ],
    "vlan": 100,
    "timeoutInSeconds": 600
  }
```

> The above command returns JSON structured like this:

```json
  {
    "requestStatusUrl": "string",
    "resourceId": "string"
  }
```

`POST http://naas-prod1.idc1.level3.com:8557/api/v1/edge/leaf/ports?accountId={accountId}`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
accountId | true | The customer accountId

### Body Parameters

Parameter | Required | Description
--------- | -------- | -----------
requestId | True | This is the upstream system's correlation ID for tracking the complete network function build
customer{name} | True | This is the name of the customer. This value is placed in the subInterface description on the switch
serviceId | True | This is the circuitId that was assigned to the customer in previous calls
productType | True | This must be either EDGE_COMPUTE_INTERNET, or EDGE_COMPUTE_IPVPN depending on the type of connection that you are building
vlan | True | This is the vlan that was previously reserved with the vlans endpoint
timeoutInSeconds | False | This is the period of time in which your system will wait for this task to be completed. If this timer expires, any configurations applied will be backed out from the network

## GET HTTP Status Request

```shell
curl "http://naas-prod1.idc1.level3.com:8557/api/v1/edge/leaf/ports/{resourceId}?accountId={accountId}"
```

> The above command returns JSON structured like this:

```json
  {
    "operation": "string",
    "requestData": {
      "requestId": "41c998be-0de3-4cb2-8931-9fc3aecb1e6c",
      "customer": {
        "name": "string"
      },
      "serviceId": "string",
      "productType": "string",
      "serverHostnames": [
        "MINERLABBM010201"
      ],
      "vlan": 100,
      "timeoutInSeconds": 600
    },
    "requestDate": "string",
    "resourceId": "string",
    "status": "string"
  }
```

`GET http://naas-prod1.idc1.level3.com:8557/api/v1/edge/leaf/ports/{resourceId}?accountId={accountId}`

Because all NaaS POST calls are asynchronous, the api/v1/edge/leaf/ports endpoint provides a mechanism to retrieve the status of the request. By providing the accountId used to make the request and the resourceId, you can receive realtime status. The status values are:

Status | Description
------ | -----------
WIP | Work In Progress
SUCCESS | Requested task was successfully applied to the network
FAILURE | The requested task could not be successfully applied to the network


### Query Parameters

Parameter | Required | Description
--------- | -------- | -----------
accountId | True | The customer accountId
resourceId | True | The resourceId from the POST response

## DELETE HTTP Status Request

```shell
curl "http://naas-prod1.idc1.level3.com:8557/api/v1/edge/leaf/ports/{resourceId}?accountId={accountId}"
-x DELETE"
```

> The above command returns JSON structured like this:

```json
  {
    "requestStatusUrl": "string",
    "resourceId": "string"
  }
```

`DELETE http://naas-prod1.idc1.level3.com:8557/api/v1/edge/leaf/ports/{resourceId}?accountId={accountId}`

### Query Parameters

Parameter | Required | Description
--------- | -------- | -----------
accountId | True | The customer accountId
resourceId | True | The resourceId from the original POST response