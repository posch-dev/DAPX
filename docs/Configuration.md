[← Back to DAPX Architecture](./README.md)



## DAPX Architecture / **Configuration**



This document describes how user configuration is stored, distributed, and propagated across nodes in the [DAPX](../README.md) protocol. Configuration determines which nodes a user relies on, how messages are handled, and how changes are communicated to the network.



# Configuration model



All configuration lives on the user's client node. When the user interacts with infrastructure nodes, the relevant configuration is passed to those nodes. Nodes do not generate or modify configuration on their own. They receive it from the user, verify the signature, and act accordingly.



---



## What is configurable



### Mailbox configuration

- Which mailbox nodes to use (see [node selection](#node-selection) below)
- How long messages are stored (maximum TTL the mailbox node will accept)
- How long friend requests are stored (separate TTL from regular messages, see [Friend Requests](./FriendRequest.md))
- The degree of segmentation (see [segmentation](#segmentation) below)

### Relay configuration

- Which relay nodes to use (see [node selection](#node-selection) below)
- Minimum and maximum chain length for [relay chaining](./RelayProtocol.md)

### Index node configuration

- Whether to be discoverable at all
- Which index node is the user's main index node
- What public metadata to publish
- Whether friend requests may be delivered through index node hints (see [Mailbox Protocol](./MailboxProtocol.md))

### Client-side configuration

- Retry intervals and backoff progression (see [Routing Rules](./RoutingRules.md))
- Timeout before prompting the user on failed delivery

Client-side configuration stays on the client and is not sent to any node.



---



## Config updates



When a user changes their configuration, the protocol identifies which nodes are affected and sends a signed update to them. Updates are delivered using the [update message type](./PacketFormat.md) with the configuration data in the update field of the outer envelope.

Every config update carries a timestamp. Nodes use this timestamp to determine ordering. If two different updates arrive, the node applies the one with the later timestamp and discards the older one.

To prevent update spam, nodes enforce a minimum interval between config updates from the same user. If a new update (with a different timestamp than the last accepted one) arrives before the interval has passed, the node rejects it. This interval is defined by the protocol.



---



## Config propagation



Config propagation works differently depending on the node type.


### Mailbox and relay nodes

There is no main mailbox or relay node. Any of the user's nodes of that type can receive an update from the user. When a mailbox or relay node receives a config update from the user, it propagates that same update to all other nodes of its type that are listed in the **old** configuration. This way, every node that was previously part of the config learns about the change, whether that means adjusting settings, accepting a new node, or stopping service.

The propagating node signals that it is relaying a user update, not issuing its own. The receiving node verifies the user's signature on the update directly. The forwarding node is a courier, not an authority.

If a node receives the same update twice (once propagated from another node, once directly from the user), it accepts both, crosschecks, and acknowledges. The minimum interval restriction applies only to different updates, not to duplicate deliveries of the same update.

Propagation is type-isolated. Mailbox config updates are only sent through the user and mailbox nodes. Relay config updates are only sent through the user and relay nodes. There is no cross-type propagation.


### Index nodes

Index nodes do not use the propagation mechanism described above. Instead, every index node that holds a user's data periodically checks the user's main index node and syncs accordingly. This is described in [Discovery Flow](./DiscoveryFlow.md).



---



## Node selection



Users can select their mailbox and relay nodes in two ways.


### Manual selection

The user explicitly chooses which nodes to use. This is the recommended approach for mailbox nodes, where the user has a direct interest in which infrastructure stores their messages.


### Automatic assignment

The user can let the protocol assign nodes automatically. The client probes available nodes (discovered through [index nodes](./DiscoveryFlow.md)) and filters by reliability. A node qualifies for automatic assignment if it meets a reliability threshold, for example 99% uptime over the last 30 days.

Reliability is tracked by the client itself. The client periodically probes its known nodes and builds its own reliability picture over time. There is no central reliability tracker. New users with no history go through an initial scan phase before automatic assignment can make an informed selection.

If an automatically assigned node drops below the reliability threshold, the client flags the issue to the user. If the user does not act, the protocol eventually drops that node from the automatic assignment. Manual assignments are not affected by reliability drops. The user must remove a manually assigned node themselves.

Node operators choose which index nodes to register with. A node that does not want broad discovery can register only with index nodes in a trusted cluster. If the user does not use index nodes at all, automatic assignment coverage cannot be guaranteed, because the client has no way to discover available nodes.



---



## Segmentation



Segmentation determines how a message is split across a user's mailbox nodes. The concept is described in [Node Types](./NodeTypes.md).

The user configures the segmentation threshold. At the lowest setting, one mailbox node holds the complete message. At the highest setting, 70% of the user's mailbox nodes are needed to reconstruct the message. The user picks a threshold anywhere in this range.

This follows a secret-sharing scheme: the message is split into shares distributed across mailbox nodes, and only the configured fraction of shares is needed to reconstruct the original. This means delivery can succeed even if some mailbox nodes are unavailable, and no single mailbox node holds enough data to reconstruct the message at higher segmentation levels.



---



## Node removal



When a user removes a node from their configuration, the update is propagated as described above. The removed node receives the update (because it was in the old configuration), verifies the user's signature, deletes all stored messages for that user, and stops accepting new deposits or forwarding for that user. This is enforced by the protocol.



---

