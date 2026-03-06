[← Back to DAPX Architecture](../README.md)



## DAPX Architecture / **Node Types**



This document describes the conceptual architecture of the Types of Nodes used in the DAPX Architecture. These Nodes ensure alternative [Messaging](./MessageFlow.md) and [Discovery](./DiscoveryFlow.md) through the [DAPX Protocol](../README.md).



# Nodes



To minimize trust requirements, DAPX follows an atomized network design.  

Each node shall serve a single purpose only. Therefore each node type has a unique set of responsibilities and performs no other actions besides their own in the network.



---



## Client Nodes



Client nodes represent the actual user in the network.



**Responsibilities**



- send and receive data  

- establish direct P2P connections when possible  

- fetch public discovery information from index nodes  

- store their chats locally

- store and manage their own [configuration](./Configuration.md), and propagate config updates to their infrastructure nodes



**Properties**



- fully independent  

- no reliance on a central service  

- full authority over themself



---



## Mailbox Nodes



Mailbox nodes enable asynchronous delivery when the recipient is offline.



**Responsibilities**



- temporarily store encrypted messages  

- allow recipient to retrieve messages later

- propagate [config updates](./Configuration.md) to other mailbox nodes of the same user



**Restrictions**



- messages remain encrypted

- storage is temporary

- multiple mailbox nodes may exist



**Segmentation**

A message can be split across multiple mailbox nodes. Only a subset of the segments is required to reconstruct the original message, similar to erasure coding. This allows delivery to succeed even if some mailbox nodes become unavailable.

The recipient decides how many mailbox nodes are used, how long messages remain stored, and the degree of segmentation. These settings are part of the user's [configuration](./Configuration.md).



---



## Relay Nodes



Relay nodes enable communication when direct connections cannot be established.



**Responsibilities**



- temporarily forward encrypted traffic between peers

- help bypass NAT or connectivity restrictions

- support [relay chaining](./RelayProtocol.md) (onion-style multi-hop forwarding)

- propagate [config updates](./Configuration.md) to other relay nodes of the same user





**Restrictions**



- do not decrypt messages  

- do not store messages  

- purely assist the transport of messages  



---



## Index Nodes



Index nodes provide discovery information.



**Responsibilities**



- display public metadata  

- enable means of discovery for peers and services  

- allow clients to locate other nodes  



**Restrictions**



- do **not store messages**  

- do **not relay communications**  

- contain **no private data**  



---

