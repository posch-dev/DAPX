[← Back to DAPX Architecture](./README.md)



## DAPX Architecture / **Packet Format**



This document describes the conceptual structure of packets in the [DAPX](../README.md) protocol. All communication between [Nodes](./NodeTypes.md) across all [Message Flow](./MessageFlow.md) paths uses the same universal packet format.



# Packet Format



DAPX uses a single, universal packet structure for all communication.

The same format is used regardless of delivery path, whether a message is sent directly, forwarded through a relay node, or stored on a mailbox node.



---



## Two-Layer Design



A DAPX packet consists of two layers: an **outer envelope** and an **inner payload**.



The outer envelope contains only the information needed to route, deliver, and handle the packet. It is readable by any node that handles the packet.

The inner payload contains the actual message content. It is encrypted and can only be read by the intended recipient.



This separation ensures that infrastructure nodes such as relay nodes and mailbox nodes can perform their function without accessing message content or identifying the sender.



---



## Outer Envelope



The outer envelope is the routing and control layer of the packet.
It is visible to every node that handles the packet and contains the minimum information required for delivery and handling.



Fields include:

- a **protocol version** identifier to ensure compatibility between nodes

- a **message type** indicating the purpose of the packet

- a **routing token** that identifies the delivery destination without exposing the recipient's identity

- a **time-to-live** value that defines how long the packet remains valid

- a **nonce** to prevent replay of previously delivered packets

- an **update field** that carries configuration instructions for infrastructure nodes when applicable



The outer envelope does **not** contain the sender's identity.

Infrastructure nodes can route the packet using the routing token alone, without knowing who sent it or who the actual recipient is.



---



## Update Field



The update field is part of the outer envelope. It carries configuration data that infrastructure nodes need to read in order to fulfill their role correctly.

When the message type is **update**, the update field contains instructions meant for the receiving node. For all other message types, the update field is absent or empty.

The update field is not encrypted, because it must be readable by the infrastructure node that receives it. It is however signed by the sender's identity key. When a user first establishes a relationship with a node, that node records the user's public key. Any future update sent to that node must carry a valid signature from the same key. The node verifies the signature before acting on the update. An update that cannot be verified is rejected. This means the update field is readable but not fakeable.



Examples of information that may be carried in the update field:

- a user opting in or out of a service provided by the node

- changes to how long messages should be stored on a mailbox node

- changes to how messages should be segmented across mailbox nodes

- changes to relay configuration

- changes to discovery preferences on an index node



The update field allows a single message type to cover all configuration changes across all node types. A mailbox node reads the update field to learn about storage policy. A relay node reads it to learn about forwarding preferences. An index node reads it to learn about discovery settings.

The update field does not contain message content and does not reveal information about other participants.



---



## Inner Payload



The inner payload is the content layer of the packet.

It is encrypted by the sender and can only be decrypted by the intended recipient.



Fields include:

- the **sender's identity** so the recipient knows who the message is from

- the **recipient's identity** for verification

- a **timestamp** indicating when the message was created

- the **message content** itself

- a **signature** from the sender to prove authenticity and integrity



No infrastructure node can access any of these fields.



---



## Routing Tokens



DAPX uses opaque routing tokens instead of exposing peer identities in the outer envelope.

A routing token is a value that allows a node to determine where to deliver a packet without revealing the identity of the sender or the recipient.

Routing tokens are generated and distributed by the recipient as part of their routing configuration.

The sender includes the appropriate routing token in the outer envelope when sending a packet.

Infrastructure nodes use the token to perform their role, forwarding or storing, without learning anything about the participants.



---



## Message Types



The message type field in the outer envelope indicates the purpose of the packet.

The following types of messages are anticipated:

- **message**: a standard communication between peers

- **friend request**: an initial contact request from one peer to another

- **friend accept**: acceptance of a friend request

- **friend reject**: rejection of a friend request

- **update**: a configuration change sent to an infrastructure node, carried in the update field

- **acknowledgement**: confirmation that a message was received



All message types use the same packet structure. The message type field allows nodes and clients to handle each packet appropriately.



---



## How Packets Are Used Across Delivery Paths



### Direct P2P

**A → B**

A constructs a packet with B's routing information in the outer envelope and the encrypted message in the inner payload. B receives the packet, reads the outer envelope, decrypts the inner payload, and processes the message.



### Relay Communication

**A → Relay Node → B**

A constructs the same packet. The relay node reads only the outer envelope to determine where to forward the packet. It does not access the inner payload. B receives the packet and decrypts it as in the direct case.



### Mailbox Delivery

**A → Mailbox Node ⇄ B**

A constructs the same packet. The mailbox node reads the outer envelope to determine the routing token and the time-to-live. It stores the packet without accessing the inner payload. When B reconnects, B retrieves the packet using the routing token and decrypts it locally.



In all three cases, the packet structure is identical. Only the handling by infrastructure nodes differs.



---

