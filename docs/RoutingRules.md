[← Back to DAPX Architecture](./README.md)



## DAPX Architecture / **Routing rules**



This document describes how a client determines the delivery path for a message in the [DAPX](../README.md) protocol. The available delivery paths are described in [Message Flow](./MessageFlow.md). The packet structure used across all paths is described in [Packet Format](./PacketFormat.md).



# Routing rules



When a client wants to send a message, it follows a fixed sequence of delivery attempts. The client moves to the next step only when the current one fails.



---



## Delivery sequence



### 1. Direct P2P

The client attempts a direct connection to the recipient. This includes NAT traversal techniques such as STUN and hole-punching. If a direct path can be established through any of these methods, the message is delivered and the sequence stops.



### 2. Relay

If no direct path can be established, the client attempts to forward the message through the recipient's relay nodes. The recipient defines a relay cluster and a chain length range as part of their [configuration](./Configuration.md). The sender constructs a random relay chain from this cluster following the [relay protocol](./RelayProtocol.md).



### 3. Mailbox

If all relays are unreachable or the recipient is offline, the client sends the message to the recipient's mailbox nodes. The recipient defines which mailbox nodes to use.

Mailbox delivery may itself be routed through a relay if the mailbox node is not directly reachable by the sender, or if the recipient's configuration requires it.



---



## Routing information

The client needs the recipient's routing configuration to execute this sequence. Where this information comes from depends on the relationship between sender and recipient.



**Between friends:** The recipient's [routing information](./RoutingInfo.md) is exchanged during [friendship establishment](./FriendRequest.md). The recipient can update this information at any time by sending an [update](./PacketFormat.md) message to the sender.

**Initial contact (friend request to a stranger):** If the recipient has published a [public routing hint](./RoutingInfo.md) through an [index node](./DiscoveryFlow.md), the sender can retrieve that hint and use it to deliver the [friend request](./FriendRequest.md). If the recipient has not published any hint, the sender can only reach them through a direct connection, which requires both to be online at the same time.



---



## Retry behavior

If all delivery paths fail (direct, relay, and mailbox), the client queues the message locally and retries using an increasing backoff interval. The intervals start short and grow over time. The client also retries on application restart.

If retries continue for an extended period without success, the client asks the user whether to keep retrying or to abort.

The retry intervals, the backoff progression, and the timeout before prompting the user are all client-side configuration. The user can adjust them.



---



## Mailbox routing through relays

A sender may not be able to reach a mailbox node directly. In this case, the message to the mailbox node can be routed through a relay. This means the delivery sequence is not strictly linear. The mailbox step may use the relay infrastructure as a transport layer to reach the mailbox node itself.

The decision to route mailbox delivery through a relay is based on the sender's own connectivity and the recipient's routing configuration.



---

