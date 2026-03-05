[← Back to DAPX](../README.md)

# DAPX Architecture

This document describes the conceptual architecture of the DAPX network,
including node types, message flow, and discovery mechanisms.

---

## Network Overview

DAPX prioritizes direct peer-to-peer communication.

If peers cannot connect directly, asynchronous exchange mechanisms allow
messages to be delivered once connectivity becomes available.

DAPX consists of several independent node types that together enable decentralized communication. Each of these nodes provide services to ensure communication in case a direct P2P-Connection fails:

**[Node Types](./NodeTypes.md)**

- Client nodes
- Index nodes
- Mailbox nodes
- Relay nodes

**[Message Flow](./MessageFlow.md)**

DAPX message flow is atomised and abstracted.
The system always prefers direct peer-to-peer communication. 
If direct communication is not possible, the system algorithmically selects alternative delivery mechanisms until a viable path is found.

1. Direct P2P (**A → B**)
1. Relay Communication (**A → Relay Node → B**)
1. Mailbox Delivery (**A → Mailbox Node ⇄ B**)

**[Discovery Flow](./DiscoveryFlow.md)**

Discovery is optional and fully controlled by the user. A user may choose whether they want to be discoverable or not.

**B ⇄ Main Index Node ⇄ other Index Nodes**

**A ⇄ Index Node** then **A → B**

---