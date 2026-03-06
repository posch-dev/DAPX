[← Back to DAPX Architecture](./README.md)



## DAPX Architecture / **Friend requests and friendships**



This document describes how friendships are established, maintained, and ended in the [DAPX](../README.md) protocol. Friendships are the basis for all ongoing communication. Only friends can exchange messages through the full set of [delivery paths](./MessageFlow.md).



# Friend requests



---



## Sending a friend request

When A wants to contact B, A sends a friend request. The request contains:

- A's public key (identity)
- A's display name
- A's [routing information](./RoutingInfo.md), including a friendship token unique to the A-B relationship

By including routing information, A gives B the ability to respond even if a direct connection is not available. This also means A should not send friend requests carelessly, since the request reveals A's infrastructure to the recipient.

The friend request is delivered as a [packet](./PacketFormat.md) with the message type set to **friend request**.



---



## Delivery

A friend request can be delivered in two ways.


### Direct delivery

If both A and B are online and can establish a direct connection, A delivers the friend request directly.


### Mailbox delivery

If B is not reachable directly, A can deliver the friend request to one of B's [mailbox nodes](./MailboxProtocol.md), but only if both conditions are met:

- B has published a [public routing hint](./RoutingInfo.md) through an [index node](./DiscoveryFlow.md)
- B has explicitly permitted friend requests to be delivered this way (see [Mailbox Protocol](./MailboxProtocol.md))

If neither delivery method is available, A cannot send the request.



---



## Acceptance

When B accepts A's friend request, B sends an acceptance packet back to A. This packet contains:

- B's [routing information](./RoutingInfo.md), including a friendship token unique to the B-A relationship

A already provided their routing information in the original request. After acceptance, both sides have each other's full routing information and can communicate through all available [delivery paths](./MessageFlow.md).

The acceptance is delivered as a [packet](./PacketFormat.md) with the message type set to **friend accept**.



---



## Expiry

If B does not respond to a friend request, it expires silently based on its time-to-live. After expiry, the request is deleted on both ends (from B's mailbox if it was stored there, and from A's pending list).

A may send another friend request after the previous one has expired.

Friend requests and regular messages have separate TTL settings on mailbox nodes. A user can configure friend requests to persist longer or shorter than regular messages. This is part of the user's [mailbox configuration](./Configuration.md).



---



## Display names

Every user chooses a display name that is included in their friend request and shared with all contacts. This name is visible network-wide to anyone the user communicates with.

When a user changes their display name, the change is sent to all friends as an [update](./PacketFormat.md) packet, or applied on the next interaction with each friend.

Friends can assign a local nickname to any contact. This nickname is stored only on the friend's client and is never sent through the network. If B assigns the nickname "Al" to A, B's client displays "Al" but A's actual display name (for example "Alex") remains visible in contact details alongside A's public key.



---



## Unfriending

When B decides to end a friendship with A:

1. B sends an **unfriend** packet to A using A's routing information (which B still has).
2. B updates their infrastructure nodes to revoke A's friendship token (see [Configuration](./Configuration.md)). The token is removed from the valid list on B's mailbox and relay nodes.
3. A's client receives the notification and displays it (for example, a visual indicator next to B's name).
4. A can no longer deposit messages on B's mailbox or route through B's relays, since A's token is now invalid.

To reconnect after unfriending, either side must send a new friend request. A user can also block friend requests from a specific public key, preventing that person from sending new requests.



---

