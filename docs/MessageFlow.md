[← Back to DAPX Architecture](../README.md)



## DAPX Architecture / **Message Flow**



This document describes the conceptual flow of communication in the [DAPX](../README.md) Architecture. The Communication takes place via dedicated [Nodes](./Nodetypes.md) that ensure the following alternative Messaging Flows, as well as [Discoverability](./DiscoveryFlow.md).



# Message Flow



DAPX message flow is atomised and abstracted.  
The system always prefers direct peer-to-peer communication.  

If direct communication is not possible, the system algorithmically selects alternative delivery mechanisms until a viable path is found.



---



## Direct P2P

**A → B**



Preferred communication mode.

Both peers are reachable and establish a direct encrypted connection.



---



## Relay Communication



**A → Relay Node → B**



If direct P2P communication is not possible (due to NAT or other connectivity restrictions), communication may be forwarded through relay nodes.



Properties of relay nodes:



- relay nodes **do not store messages**

- they only forward encrypted traffic

- they purely assist the transportation of a message



Relay configuration is predefined by the recipient.
He defines which relay nodes he uses.
The sender simply follows the relay configuration provided by the recipient.



---



## Mailbox Delivery

**A → Mailbox Node ⇄ B**



If the recipient is offline or relay delivery is not possible, messages may be delivered through mailbox nodes.
Mailbox nodes temporarily store encrypted message data until the recipient retrieves it. Mailbox configuration is also defined by the recipient.

Properties

- messages remain encrypted

- storage is temporary

- mailbox nodes may be multiple and distributed

The recipient decides:

- how many mailbox nodes are used

- how long messages remain stored

- the degree of segmentation


Segmentation allows a message to be split across multiple mailbox nodes.
Only a subset of the segments may be required to reconstruct the original message, similar to redundancy used in QR codes or erasure coding systems. This ensures message delivery even if some nodes become unavailable.



---

