[← Back to DAPX Architecture](./README.md)



## DAPX Architecture / **Relay protocol**



This document describes how [relay nodes](./NodeTypes.md) operate in the [DAPX](../README.md) protocol. Relay communication is used when a direct peer-to-peer connection cannot be established, as described in [Routing Rules](./RoutingRules.md).



# Relay protocol



Relay nodes forward encrypted packets between peers without reading or storing the content. The recipient defines which relay nodes to use as part of their [configuration](./Configuration.md). The sender learns the recipient's relay nodes from their [routing information](./RoutingInfo.md).



---



## Authorization

A relay node only forwards traffic for users who have configured it. When B adds a relay node to their configuration, that node receives B's list of valid [friendship tokens](./RoutingInfo.md).

When A sends a packet through one of B's relay nodes, the relay checks the friendship token in the packet against B's valid token list. If the token is recognized, the relay processes the packet. If not, the relay rejects it.

The relay does not learn A's identity from this process. It only confirms that the token belongs to one of B's authorized contacts.

[Friend requests](./FriendRequest.md) from strangers are an exception. A stranger does not have a friendship token. The relay accepts a friend request without a token as long as the packet is addressed to a user the relay serves (B has an established trust relationship with this node) and B has permitted friend request delivery in their [configuration](./Configuration.md). The relay checks the message type in the outer envelope to identify friend requests.



---



## Per-packet delivery

Relay communication is stateless. There is no persistent session or connection between the sender and a relay node. Each packet is independently routed: A sends a packet to a relay node, the relay verifies authorization, and the relay forwards the packet toward its destination. The next packet may take a different path through a different chain of relays.



---



## Relay chaining

Relay nodes can be chained so that a packet passes through multiple relays before reaching the recipient. This follows an onion routing model.


### How it works

B's [configuration](./Configuration.md) includes a relay cluster (the set of relay nodes B uses) and a chain length range (minimum and maximum number of hops). When A wants to send a packet to B through relays, A constructs a chain:

1. A knows B's full relay cluster from B's [routing information](./RoutingInfo.md).
2. A randomly selects a chain of relay nodes from B's cluster. The number of relays in the chain falls within B's configured min/max range.
3. A wraps the packet in layers of encryption, one layer per relay in the chain. The innermost layer is the actual [DAPX packet](./PacketFormat.md) destined for B. Each outer layer contains the address of the next hop and the friendship token for authorization at that relay.
4. A sends the wrapped packet to the first relay in the chain.
5. Each relay peels one encryption layer, which reveals the next hop. The relay forwards the remaining packet to the next address.
6. The last relay in the chain peels its layer and finds the DAPX packet for B. It delivers the packet to B through B's existing connection to that relay.

The chain is randomly selected per delivery. Different packets may take different paths through B's relay cluster.


### What each relay knows

- The **entry relay** (first in the chain) knows A's network address (since A connects to it) but does not know which relay delivers to B or that B is the final recipient.
- **Middle relays** know only the previous relay and the next relay. They do not know A's address or B's address.
- The **exit relay** (last in the chain) delivers to B but does not know A's address or which relay A originally connected to.

No single relay in the chain knows the full path. Each relay only knows its immediate neighbors.


### Recipient connectivity

B maintains connections to the relay nodes in its cluster. This is necessary because relay communication is used when B cannot be reached directly. The exit relay delivers to B through B's existing connection.



---



## Configuration propagation

Relay nodes follow the same [configuration propagation](./Configuration.md) model as mailbox nodes. When a relay node receives a config update from the user, it propagates the update to all other relay nodes of that user listed in the old configuration. The forwarding relay is a courier; the receiving relay verifies the user's signature directly.

Relay config propagation is type-isolated. Relay config updates are only sent through the user and relay nodes, never through mailbox or index nodes.



---

