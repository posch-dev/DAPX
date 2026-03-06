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



If direct P2P communication is not possible (due to NAT or other connectivity restrictions), communication may be forwarded through [relay nodes](./NodeTypes.md) using the [relay protocol](./RelayProtocol.md). Relay [configuration](./Configuration.md) is predefined by the recipient.



---



## Mailbox Delivery

**A → Mailbox Node ⇄ B**



If the recipient is offline or relay delivery is not possible, messages may be delivered through [mailbox nodes](./NodeTypes.md). Mailbox nodes temporarily store encrypted message data until the recipient retrieves it. Mailbox [configuration](./Configuration.md) is defined by the recipient.



---

