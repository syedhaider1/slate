# Create a Layer 2 Network

Layer-2 Networking  allows the customer to build Layer-2 Ethernet only networks by adding one or more VLANs to their Lumen Edge Bare Metal instances, within a specific Lumen Edge Compute location, to facilitate Layer-2 Ethernet switching.  Customers may use this networking connectivity option to create clusters of bare metal machines, virtual machines or other instances that may be useful in a number of their use cases.  When Layer-2 Networking is used, no IP addresses are Lumen assigned to the virtual network interface allowing the customer to define their own subnet size and IP addresses.  The VLAN assignment can be specified by the customer or Lumen can auto-select and set a non-conflicting VLAN ID. 

![layer 2 networking image](/images/layer2Network.png)

## NaaS Requests

There are three steps required to utilize NaaS to build a layer 2 network between bare metal hosts. You can configure the vlan across multiple bare metal hosts at the same time, however you will need to configure each vlan individually. To build a layer 2 network, you must first reserve the vlan on all relevant host revenue ports, then configure the host revenue ports, then build the path between the ports.

For this scenario, we will be building a layer 2 network with the following requirements:

Customer Name | Account # | Hosts | VLAN | Request ID
------------- | --------- | ----- | ---- | ----------
Acme | 123456 | MINERLABBM010201<br />MINERLABBM010301 | 100 | layer-2-network-tutorial

## Step 1: Reserve Vlan

First we use the [Vlans](#vlans) endpoint to reserve the vlan. Because we are building this network across multiple hosts, we must include all of the bare metal host names in the serverHostnames list in our POST request payload. We will be telling NaaS to use vlan 100, though we could allow NaaS to select the next available vlan for us, but leaving that parameter out of our Reserve Vlan request.

We make a post call to the /api/v1/edge/vlans endpoint with the payload referenced below.

`POST http://naas-prod1.idc1.level3.com:8557/api/v1/edge/vlans?accountId=123456`

<pre class="center-column">
{
  "requestId": "layer-2-network-tutorial",
  "customer": {
    "name": "Acme"
  },
  "serverHostnames": [
    "MINERLABBM010201",
    "MINERLABBM010301"
  ],
  "vlan": "100",
  "timeoutInSeconds": 600
}
</pre>

We get a response of with a resourceId of "987654":

<pre class="center-column">
{
"requestStatusUrl": "http://naas-prod1.idc1.level3.com:8557/api/v1/edge/vlans/987654?accountId=123456",
"resourceId": "987654"
}
</pre>

Because the NaaS API is asynchronous, we must then use the Vlans GET endpoint to retrieve the status of our request using the resourceId that we received in the response.

`GET http://naas-prod1.idc1.level3.com:8557/api/v1/edge/vlans/987654?accountId=123456`

We continue to poll for status, until we receive a status of SUCCESS!

<pre class="center-column">
{
  "operation": "Build",
  "requestData": {
    "requestId": "layer-2-network-tutorial",
    "customer": {
      "name": "Acme"
    },
    "serverHostnames": [
      "MINERLABBM010201",
      "MINERLABBM010301"
    ],
    "vlan": "100",
    "timeoutInSeconds": 600
  },
  "requestDate": "3/3/2021",
  "resourceId": "987654",
  "status": "SUCCESS",
  "vlan": "100"
}
</pre>
<aside class="notice">
For more details on the VLANs endpoints, please reference: <a href="#vlans">NaaS Endpoints: VLANs</a>
</aside>

## Step 2: Configure Ports

Next we use the [Ports](#ports) endpoint to configure the host revenue ports with the new vlan that was reserved above. This will apply the vlan to the host revenue ports on the leaf switches for each of the named hosts that we provide. We will provide the same customer details, request ID, and hosts as above, along with the vlan that we reserved in the Reserve Vlan call. 

In addition, we also need to now include a serviceId, and a productType. The productType we will use for this build is "EDGE_COMPUTE_LEAF_TO_LEAF".

We make a post call to the /api/v1/edge/leaf/ports endpoint with the payload referenced below.

`POST http://naas-prod1.idc1.level3.com:8557/api/v1/edge/leaf/ports?accountId=123456`

<pre class="center-column">
{
  "requestId": "layer-2-network-tutorial",
  "customer": {
    "name": "string"
  },
  "serviceId": "CO/IRXX/12345/LVLC",
  "productType": "EDGE_COMPUTE_LEAF_TO_LEAF",
  "serverHostnames": [
    "MINERLABBM010201",
    "MINERLABBM010301"
  ],
  "vlan": 100,
  "timeoutInSeconds": 600
}
</pre>

We get a response of with a resourceId of "234567":

<pre class="center-column">
{
"requestStatusUrl": "http://naas-prod1.idc1.level3.com:8557/api/v1/edge/vlans/987654?accountId=123456",
"resourceId": "234567"
}
</pre>

Because the NaaS API is asynchronous, we must then use the Ports GET endpoint to retrieve the status of our request using the resourceId that we received in the response.

`GET http://naas-prod1.idc1.level3.com:8557/api/v1/edge/leaf/ports/234567?accountId=123456`

We continue to poll for status, until we receive a status of SUCCESS!

<pre class="center-column">
{
  "operation": "string",
  "requestData": {
    "requestId": "layer-2-network-tutorial",
    "customer": {
      "name": "Acme"
    },
    "serviceId": "CO/IRXX/12345/LVLC",
    "productType": "EDGE_COMPUTE_LEAF_TO_LEAF",
    "serverHostnames": [
      "MINERLABBM010201",
      "MINERLABBM010301"
    ],
    "vlan": 100,
    "timeoutInSeconds": 600
  },
  "requestDate": "3/3/2021",
  "resourceId": "234567",
  "status": "SUCCESS"
}
</pre>

<aside class="notice">
For more details on the Ports endpoints, please reference: <a href="#ports">NaaS Endpoints: Ports</a>
</aside>

## Step 3: Configure Path

Finally, we use the [Path](#path) endpoint to retrieve the next available VNI, build it in the VXLAN domain, and add all of the host revenue ports to it. 

We make a POST call to the /api/v1/edge/paths endpoint with the payload referenced below.

`POST http://naas-prod1.idc1.level3.com:8557/api/v1/edge/paths?accountId=123456`

<pre class="center-column">
{
  "requestId": "layer-2-network-tutorial",
  "customer": {
    "name": "string"
  },
  "serviceId": "CO/IRXX/12345/LVLC",
  "productType": "EDGE_COMPUTE_LEAF_TO_LEAF",
  "serverHostnames": [
    "MINERLABBM010201",
    "MINERLABBM010301"
  ],
  "vlan": 100,
  "timeoutInSeconds": 600
}
</pre>

We get a response of with a resourceId of "789012":

<pre class="center-column">
{
"requestStatusUrl": "http://naas-prod1.idc1.level3.com:8557/api/v1/edge/paths/789012?accountId=123456",
"resourceId": "789012"
}
</pre>

Because the NaaS API is asynchronous, we must then use the Paths GET endpoint to retrieve the status of our request using the resourceId that we received in the response.

`GET http://naas-prod1.idc1.level3.com:8557/api/v1/edge/paths/234567?accountId=123456`

We continue to poll for status, until we receive a status of SUCCESS!

<pre class="center-column">
{
  "operation": "string",
  "requestData": {
    "requestId": "layer-2-network-tutorial",
    "customer": {
      "name": "Acme"
    },
    "serviceId": "CO/IRXX/12345/LVLC",
    "productType": "EDGE_COMPUTE_LEAF_TO_LEAF",
    "serverHostnames": [
      "MINERLABBM010201",
      "MINERLABBM010301"
    ],
    "vlan": 100,
    "timeoutInSeconds": 600
  },
  "requestDate": "3/3/2021",
  "resourceId": "234567",
  "status": "SUCCESS"
}
</pre>

<aside class="notice">
For more details on the Path endpoints, please reference: <a href="#path">NaaS Endpoints: Path</a>
</aside>