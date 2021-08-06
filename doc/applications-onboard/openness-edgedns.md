```text
SPDX-License-Identifier: Apache-2.0
Copyright (c) 2019 Intel Corporation
```
<!-- omit in toc -->
# Edge DNS

<!-- Use this template to create documentation for a Smart Edge Open component. Guidance for creating documentation is in comments. Remove comments before publishing. -->

## Overview
<!-- Provide a brief description of the component and how it's used in the larger system. -->
The edge platform must provide access to a Domain Name System (DNS) server. The edge platform receives the application DNS rules from the controller. This is specified in the ETSI Multi-access Edge Computing (MEC). From a 5G edge deployment perspective, the Primary DNS (priDns) and Secondary DNS (secDns) need to be configured which is going to be consumed by the SMF.
<!-- fix the last sentence above. Confusing. -->

Smart Edge Open supports DNS microservice to address these DNS requirements and also DNS service for edge applications and network functions.

![DNS support on OpenNESS overview](dns-images/dns1.png)

_Figure - DNS support on OpenNESS overview_

>**NOTE**: Secondary DNS service is out of the scope of OpenNESS and is only used for DNS forwarding.

EdgeDNS is a functionality to provide the Domain Name System (DNS) Server with a possibility to be controlled by its CLI.

## How It Works
<!-- Describe the overall architecture of the system installed by the experience kit. Include a high-level architecture diagram if possible.-->
The EdgeDNS server listens for requests from a client's commandline interface (CLI). After receiving a CLI request, a function handling the request adds or removes the RULE inside of the EdgeDNS database.

EdgeDNS supports only type A records for Set/Delete Fully Qualified Domain Names (FQDN) and the current forwarder is set to 8.8.8.8 (set in docker-compose.yml and openness.yaml).

Network Edge mode provides EdgeDNS as a service, which is an application running in a K8s pod on each node of the OpenNESS K8s cluster. It allows users to add and remove DNS entries of the node directly from K8s control plane using kubectl plugin.

## How To
<!-- This section provides users with concise instructions for tasks commonly performed with this component.

How to's should be granular in scope. They should describe a common, concrete task. Bigger-picture and end-to-end usage questions will be handled elsewhere.

How to's should be based on user needs, describing the functions that are most important to users of this component. FAEs and support forums are two examples of potential sources for understanding what is important to end users.

The label for the how to item should start with a verb, followed by a concrete action.

Examples: Collect features from nodes. List features labeled in Kubernetes.
[Counter examples: Understanding NFD, Using NFD to label features]

Keep the body of the how to item as concise as possible, while still providing the information users need to complete the task. How to's will potentially create a very long page for users to scroll through, so keep each section as targeted as possible.

Include screenshots as needed. Again, balance the overall length of the section with user needs.

Do not use subheaders beneath how to items. This will create cleaner navigation for users. Use bold styling to organize the content  without using subheaders.
-->
### Control the DNS server by client
The EdgeDNS server can be controlled by its CLI. The CLI reads a json file containing HostRecordSet for set operation and RecordSet for del operation. The following is an example of JSON files that defines www.example.com to be 1.1.1.1;1.1.1.2;1.1.1.3;1.1.1.4 as `set.json` and `del.json` files:

set.json
```json  
  {
    "record_type":"A",
    "fqdn":"www.example.com",
    "addresses":["1.1.1.1", "1.1.1.2", "1.1.1.3", "1.1.1.4"]
  }
```

del.json
```json
  {
    "record_type":"A",
    "fqdn":"www.example.com"
  }
```

### Use the EdgeDNS CLI as a Kubernetes* plugin
In Network Edge, the EdgeDNS CLI is used as a Kubernetes\* plugin. The following is an output of help of kubectl edgedns.

```
 `kubectl edgedns --help` to learn about usage
 `kubectl edgedns set <node_hostname> <JSON filename>` to set DNS entry of node
 `kubectl edgedns del <node_hostname> <JSON filename>` to delete DNS entry of node
```

>**NOTE**: `node_hostname` must be a valid node name; it can be found using `kubectl get nodes`

>**NOTE**: `JSON filename` is a path to the file containing record_type, fqdn, and addresses in case of setting operation. JSON file without record_type also is valid, and as default value "A" is set.


### Set or remove the DNS entry on the node
To set the DNS entry on the node from the `set.json` file, use the following command:

`kubectl edgedns set node1 set.json`

To remove a DNS entry on the node from the `set.json` file, use the following command:

`kubectl edgedns del node1 del.json`

<!-- Don't use additional subheaders in this section. If needed, use bold styling to organize the content in this section.-->
### Item #2
<!-- Don't use additional subheaders in this section. If needed, use bold styling to organize the content in this section.-->
### Item #3...
<!-- Don't use additional subheaders in this section. If needed, use bold styling to organize the content in this section.-->
