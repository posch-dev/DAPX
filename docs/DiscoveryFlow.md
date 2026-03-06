[← Back to DAPX Architecture](../README.md)

## DAPX Architecture / **Discovery**



This document describes the conceptual architecture of decentralized Discoverability trough the use of the [DAPX Protocol](../README.md). [Index Nodes](./NodeTypes.md) enable the discovery of peers, communities, and services. They only provide publicly shared discovery information and do not participate in message transmission or store private communication, which is handled separately through the [Messaging](./MessageFlow.md) mechanisms.



# Discovery Flow



Discovery is optional and fully controlled by the user.
A user may choose whether they want to be discoverable or not.

If discoverability is enabled, the user may publish:



- a username or identifier

- a public key

- optional public metadata



This information is published to an index node chosen by the user as their **main index node**.

The main index node distributes this discovery information to other known index nodes using a gossip-style propagation mechanism. The main index node is the sole authority for that user's entry.

When a user changes or removes their information on the main index node, all other index nodes that hold a copy of that data must follow. They are bound by the propagation contract established when the data was first gossiped. Periodic synchronization queries between index nodes enforce this. An index node that holds outdated or revoked data and refuses to update it is in violation of the protocol.



---



## Peer Discovery



When a user wants to find another peer, an index node returns the public metadata associated with the queried identity. Using the obtained information, A can attempt to directly contact B. B may then accept or reject the connection or friendship request.



**B ⇄ Main Index Node ⇄ other Index Nodes**

**A ⇄ Index Node** then **A → B**



