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

**[Mailbox Protocol](./MailboxProtocol.md)**

How messages are deposited, stored, retrieved, and deleted on mailbox nodes. Covers access control, polling-based retrieval, deletion after acknowledgement or TTL expiry, and spam protection through adaptive proof of work.

**[Routing Rules](./RoutingRules.md)**

The client follows a fixed delivery sequence: direct P2P (including STUN/hole-punching), then relay, then mailbox. If all paths fail, the message is queued locally and retried with backoff.

**[Packet Format](./PacketFormat.md)**

DAPX uses a universal two-layer packet structure for all communication.
The outer envelope handles routing without exposing identities. The inner payload carries encrypted content only the recipient can read.

**[Routing Information](./RoutingInfo.md)**

What routing information contains, how it is structured (private routing info for friends vs public routing hints for discovery), friendship tokens, and how updates are distributed.

**[Friend Requests and Friendships](./FriendRequest.md)**

How friendships are established, maintained, and ended. Covers sending and delivering friend requests, acceptance, expiry, display names, and unfriending with token revocation.

**[Relay Protocol](./RelayProtocol.md)**

How relay nodes forward traffic. Covers authorization via friendship tokens, per-packet stateless delivery, onion-style relay chaining, and what each relay in a chain can see.

**[Configuration](./Configuration.md)**

All configuration lives on the user's client node and is propagated to infrastructure nodes through signed updates. Covers node selection (manual and automatic), config propagation across mailbox and relay nodes, segmentation thresholds, update ordering, and node removal.

**[Security](./Security.md)**

DAPX builds security into the structure of the protocol rather than relying on trusted parties.
This document describes how the protocol upholds confidentiality, integrity, authenticity, availability, and decentralization across all components.

**[Discovery Flow](./DiscoveryFlow.md)**

Discovery is optional and fully controlled by the user. A user may choose whether they want to be discoverable or not.

**B ⇄ Main Index Node ⇄ other Index Nodes**

**A ⇄ Index Node** then **A → B**

---