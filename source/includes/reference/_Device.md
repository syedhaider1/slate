# Get Connected Devices

```python
import NaaS

api = NaaS.authorize('authKey')
api.edge.devices.get()
```

```shell
curl "http://naas01-dev.idc1.level3.com:8557/api/v1/edge/devices?machineHostname={machineHostname}"
  -H "Authorization: authKey"
```

```javascript
const naas = require('NaaS');

let api = naas.authorize('authKey');
let devices = api.devices.get();
```

> The above command returns JSON structured like this:

```json

  {
  "leafDevices": [
    {
      "deviceName": "string",
      "interfaceName": "string"
    }
  ]
}

```

This endpoint is used to retrieve which network switches a compute server is connected to. This information is 
used in future requests to NaaS to configure the network components. The endpoint requires the machineHostName
parameter, which is used to lookup connections in inventory.

### HTTP Request

`GET http://naas01-dev.idc1.level3.com:8557/api/v1/edge/devices`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
machineHostName | true | The name of the server being provisioned.