[← Back to DAPX Architecture](./README.md)



## DAPX Architecture / **Security**



This document describes how the [DAPX](../README.md) protocol upholds the core principles of information security across all of its components, including [Node Types](./NodeTypes.md), [Message Flow](./MessageFlow.md), [Packet Format](./PacketFormat.md), [Routing Information](./RoutingInfo.md), [Friend Requests](./FriendRequest.md), [Relay Protocol](./RelayProtocol.md), [Discovery](./DiscoveryFlow.md), [Mailbox Protocol](./MailboxProtocol.md), and [Configuration](./Configuration.md).



# Security Model



DAPX is designed so that no participant in the network needs to be trusted beyond their defined role.

Security is not enforced by a central authority. It is built into the structure of the protocol itself.



---



## Core Principles



### Confidentiality

Message content is encrypted by the sender before it enters the network. No infrastructure node, relay node, mailbox node, or index node can read the content of a message. Only the intended recipient can decrypt the inner payload. Routing information in the outer envelope is kept to the minimum required for delivery and does not reveal message content or sender identity.



### Integrity

Every message is signed by the sender's identity key. The recipient can verify that the message has not been altered in transit. Any message with an invalid or missing signature is rejected. This applies to updates as well: an update to a node's configuration must carry a valid signature from the user who established that relationship with the node.



### Authenticity

Identities in DAPX are derived from public key cryptography. A peer's identity is their public key. There is no central registry that can be manipulated to reassign or impersonate an identity. When a message arrives, the recipient verifies the signature against the sender's known public key. If the signature does not match, the message is rejected.



### Availability

DAPX does not rely on any single node or server for message delivery. If direct communication fails, relay nodes provide an alternative path. If the recipient is offline, mailbox nodes provide temporary storage. Messages can be segmented across multiple mailbox nodes so that delivery succeeds even if some nodes become unavailable. No single point of failure can prevent communication.



### Replay Protection

Each packet carries a nonce, a unique value that identifies this specific packet. Nodes and clients reject packets with a nonce that has already been processed. This prevents an attacker from capturing a valid packet and replaying it later to produce unintended effects.



---



## Decentralization as a Security Property



Centralization creates risk. A single point of control is a single point of failure, compromise, or coercion.

DAPX distributes responsibility across independent node types, each with a limited and clearly defined role. No single node type has enough information or capability to compromise the system on its own.

- Relay nodes see encrypted traffic but cannot read it and do not store it.
- Mailbox nodes store encrypted packets but cannot read them.
- Index nodes hold public metadata only and do not participate in communication.
- Client nodes hold identity keys and message content but are controlled by the user.

This separation means that compromising one node type does not compromise the whole system.



---



## User Sovereignty



Users have full authority over their own configuration. A user decides which nodes they use, how their messages are routed, how long messages are stored, and whether they are discoverable. No external party can change these settings on behalf of a user. Configuration changes require a valid signature from the user's own identity key.

A node can refuse to provide service, but cannot alter a user's configuration without their authorization.

This principle extends to discovery. Discoverability is opt-in. A user who does not publish to an index node cannot be found through the discovery mechanism.

User authority over discovery data does not stop at the main index node. When a user's data is gossiped to other index nodes, those nodes enter a propagation contract. If the user changes or removes their data on the main index node, all other index nodes holding that data are bound to follow. The main index node is the sole authority for that user's entry. Any index node that holds stale or revoked data and refuses to update it is in violation of the protocol.



---



## No Global Authority



The DAPX protocol does not define any authority that can issue instructions to the entire network.

There is no mechanism for forcing a node to delete data, forward a packet, or change its behavior based on instructions from a third party. Nodes act according to their own rules and the signed configurations provided by the users they serve.

Refusing to participate is always permitted. Compelling participation is never possible through the protocol.

This applies at every layer. No message type, no update field, and no discovery mechanism carries the power to force action on nodes or users that have not voluntarily accepted it.



---



## Relay privacy

Relay nodes use onion-style chaining as described in the [Relay Protocol](./RelayProtocol.md). The entry relay knows the sender's network address but not the exit relay or the recipient. The exit relay knows the recipient but not the sender's address. Middle relays know only their immediate neighbors. No single relay in the chain knows the full path.

This limits the information available to any compromised relay node. Even if an attacker controls one relay in the chain, they cannot determine both the sender and the recipient of a given packet.



---



## Friendship tokens and revocation

Each friendship produces a unique [friendship token](./RoutingInfo.md) that authorizes the sender to use the recipient's infrastructure nodes. Because tokens are per-relationship, a user can revoke one friend's access without affecting any other friend.

When a user [unfriends](./FriendRequest.md) someone, the revoked token is removed from all of the user's infrastructure nodes through the normal [configuration update](./Configuration.md) process. The revoked friend can no longer deposit messages or route through relays. This revocation is immediate once the config update propagates.



---



## Configuration update security

Configuration updates are signed by the user's identity key (see [Packet Format](./PacketFormat.md)). When a node receives a config update, whether directly from the user or propagated by another node, it verifies the user's signature before acting on it. The forwarding node is a courier, not an authority. It cannot alter the update or forge one.

To prevent update spam, nodes enforce a protocol-defined minimum interval between different config updates from the same user. Duplicate deliveries of the same update (same timestamp) are accepted and acknowledged, but a new update that arrives before the interval has passed is rejected.

When a user removes a node from their configuration, the removed node is required by the protocol to delete all stored data for that user and stop accepting new deposits or forwarding. The full configuration model is described in [Configuration](./Configuration.md).



---



## Spam protection

Mailbox nodes are exposed to abuse because they accept deposits on behalf of offline recipients. The [Mailbox Protocol](./MailboxProtocol.md) defines the full mechanism. In summary:

- Depositing a message requires completing a proof-of-work challenge.
- Normal use requires only a very short computation time.
- If a sender deposits at a high rate, the difficulty increases progressively for that sender.
- When a sender reaches a difficulty threshold, the node blocks them for a period of time, then allows them again.
- If a sender gets blocked multiple times within a timeframe and shows barely any normal usage between blocks, the node permanently blocks that sender as defined by the protocol.

This approach protects availability without introducing a central authority or requiring identity disclosure from senders.



---



## Checking Security Against New Additions



When any new component or mechanism is added to the DAPX protocol, it should be evaluated against the following questions before being finalized:

- Does it reveal message content or sender identity to infrastructure nodes?
- Does it create a single point of failure or control?
- Does it allow any party to force behavior on another without their consent?
- Does it weaken the separation of responsibilities between node types?
- Does it introduce a mechanism that could be used to coerce or surveil users?

If the answer to any of these is yes, the design must be revised before implementation.



---

