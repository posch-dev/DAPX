[← Back to DAPX Architecture](./README.md)



## DAPX Architecture / **Mailbox protocol**



This document describes how messages are deposited, stored, retrieved, and deleted on [mailbox nodes](./NodeTypes.md) in the [DAPX](../README.md) protocol. The conditions under which mailbox delivery is used are described in [Routing Rules](./RoutingRules.md).



# Mailbox protocol



Mailbox nodes temporarily store encrypted messages for recipients who are not currently reachable. The recipient configures which mailbox nodes to use, the degree of [segmentation](./NodeTypes.md), and how long messages are stored.



---



## Access control



Only accepted friends may deposit messages on a recipient's mailbox node. When two peers establish a friendship, the recipient shares routing information that includes mailbox access. Without this information, a sender cannot deposit messages.

Friend requests are an exception. A friend request may be delivered to a mailbox node if the recipient has published mailbox hints through an [index node](./DiscoveryFlow.md) and has explicitly permitted friend requests to be delivered this way. If the recipient has not granted this permission, friend requests can only be delivered through a direct connection.



---



## Deposit

When a sender delivers a message to a mailbox node, the message is in the standard [packet format](./PacketFormat.md). The mailbox node stores the packet without decrypting it. It reads only the outer envelope to determine the routing token and the time-to-live.

If [segmentation](./NodeTypes.md) is configured, the sender splits the message across the recipient's mailbox nodes according to the recipient's configuration before depositing.



---



## Retrieval

The recipient polls their mailbox nodes to check for waiting messages. Polling happens on application startup, periodically while online, or manually at the user's request.

When the recipient retrieves a message, it is decrypted locally on the recipient's device.



---



## Deletion

A message is deleted from a mailbox node in one of two cases:

- The recipient retrieves the message and sends an acknowledgement to the mailbox node. The mailbox node deletes the message after receiving the ACK.

- The message's time-to-live expires. The mailbox node deletes the message regardless of whether it was retrieved.

The recipient configures how long messages are stored. This is the maximum time-to-live that the mailbox node will accept. Messages may expire sooner if the sender specified a shorter TTL in the packet.



---



## Spam protection

Mailbox nodes use proof of work to limit abuse. A sender must complete a computational proof before the mailbox node accepts a deposit.

For normal use, this proof takes a very short time to compute. The mailbox node tests the sender's device capability to calibrate the initial difficulty.

If a sender continues to send at a high rate, the proof of work required from that sender gets progressively harder. If high volume comes from many senders at once, the difficulty increases for all incoming deposits.

If a sender keeps sending despite increasing difficulty, the mailbox node blocks that sender for a period of time. The block duration is determined by the mailbox node.



---

