> **Project Status:**  
> DAPX is an exploratory project currently in the design phase. This repository focuses on documenting ideas, architecture, and design concepts. A full implementation or production-ready code should not be expected at this stage.

# Decentralized Asynchronous Peer Exchange (DAPX)

A decentralized communication model designed to enable “offline” peer-to-peer message exchange and discovery without relying on centralized infrastructure. It allows independent nodes to communicate directly when possible and asynchronously when participants are not online at the same time.

## Applications
DAPX can be used as a communication layer for systems that require decentralized messaging and discovery.

## Goals
DAPX aims to improve the usability of peer-to-peer networks by integrating:

- Support for both synchronous and asynchronous communication
- Reliable delivery of messages when recipients are offline
- Decentralized mechanisms for discovery

All of these objectives must be achieved without compromising the core principles of strong encryption, decentralization, and the fundamental pillars of information security.

## Core & Principles

### Peer-to-Peer Communication
DAPX steps in where direct peer-to-peer communication fails. **Peer-to-peer connectivity shall always be prioritized.** When direct communication is not possible, DAPX provides supporting mechanisms such as offline message delivery and decentralized discovery to maintain network usability.

### Asynchronous Delivery
If a recipient is offline, DAPX enables asynchronous communication through mailbox and relay nodes.  
Mailbox nodes temporarily store encrypted messages for offline recipients until they reconnect and retrieve them. Relay nodes assist in forwarding messages through the network.

### Decentralized Discovery
DAPX uses index nodes to enable discovery of peers, communities, and services within the network. They do not host messages, store private data, or participate in communication between clients. Clients simply read public information from index nodes.

Multiple independent index nodes may exist, and none control or govern the network. **Governance of discoverability remains with the users.**

---

*poschdev*