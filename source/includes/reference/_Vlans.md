# VLANs

The Vlan endpoint is used to reserve a vlan on the relevant leaf switch ports. This will create
a disabled subInterface on the leaf switches with the appropriate vlan configured on it to reserve
the vlan for future use by the port and path calls. This ensures that future requests do not
also attempt to consume the same vlan. This reserved vlan can then be used by an orchestrator
to build connected device network configurations.

<aside class="notice">
This endpoint provides an asynchronous response for POST and DELETE requests. The synchronous response includes a URL that the caller can poll for status
</aside>

## POST HTTP Request

```shell
 curl "http://naas-prod1.idc1.level3.com:8557/api/v1/edge/vlans?accountId={accountId}"
  -X POST
  -d '{json}'
```
```json
  {
    "requestId": "41c998be-0de3-4cb2-8931-9fc3aecb1e6c",
    "customer": {
      "name": "string"
    },
    "serverHostnames": [
      "MINERLABBM010201"
    ],
    "vlan": "100",
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

`POST http://naas-prod1.idc1.level3.com:8557/api/v1/edge/vlans?accountId={accountId}`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
accountId | true | The customer accountId

### Body Parameters

Parameter | Required | Description
--------- | -------- | -----------
requestId | True | This is the upstream system's correlation ID for tracking the complete network function build
customer{name} | True | This is the name of the customer. This value is placed in the subInterface description on the switch
serverHostnames | True | This is a list of connected devices where you would like to reserve the vlan
vlan | False | If provided, NaaS will attempted to reserve this specific vlan. If it is not provided, then NaaS will retrieve the next available vlan across all relevant ports
timeoutInSeconds | False | This is the period of time in which your system will wait for this task to be completed. If this timer expires, any configurations applied will be backed out from the network

## GET HTTP Status Request

```shell
curl "http://naas-prod1.idc1.level3.com:8557/api/v1/edge/vlans?accountId={accountId}&resourceId={resourceId}"
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
      "serverHostnames": [
        "MINERLABBM010201"
      ],
      "vlan": "100",
      "timeoutInSeconds": 600
    },
    "requestDate": "string",
    "resourceId": "string",
    "status": "string",
    "vlan": "string"
  }
```

`GET http://naas-prod1.idc1.level3.com:8557/api/v1/edge/vlans/{resourceId}?accountId={accountId}`

Because all NaaS POST calls are asynchronous, the api/v1/edge/vlans endpoint provides a mechanism to retrieve the status of the request. By providing the accountId used to make the request and the resourceId, you can receive realtime status. The status values are:

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
curl "http://naas-prod1.idc1.level3.com:8557/api/v1/edge/vlans/{resourceId}?accountId={accountId}"
-x DELETE"
```

> The above command returns JSON structured like this:

```json
  {
    "requestStatusUrl": "string",
    "resourceId": "string"
  }
```

`DELETE http://naas-prod1.idc1.level3.com:8557/api/v1/edge/vlans/{resourceId}?accountId={accountId}`

### Query Parameters

Parameter | Required | Description
--------- | -------- | -----------
accountId | True | The customer accountId
resourceId | True | The resourceId from the original POST response