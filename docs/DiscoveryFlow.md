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

The main index node distributes this discovery information to other known index nodes using a gossip-style propagation mechanism. However, the main index node remains the authority for the entry.
If the user withdraws discovery from their main index node, the other index nodes are expected to remove the data as well.
This can be enforced through periodic update or deletion queries between index nodes.



---



## Peer Discovery



When a user wants to find another peer, an index node returns the public metadata associated with the queried identity. Using the obtained information, A can attempt to directly contact B. B may then accept or reject the connection or friendship request.



**B ⇄ Main Index Node ⇄ other Index Nodes**

**A ⇄ Index Node** then **A → B**



