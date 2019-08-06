---
title: "Network Design"
fullTitle: "Network Design"
name: "Mikhail Ignatovich"
date: 2018-08-06
external_link: ""
weight: 2
draft: false
---

The KolmoLD Network is decentralized network storage of [manifests](/documentation/manifest) and data chunks based on classic [p2p](https://en.wikipedia.org/wiki/Peer-to-peer) networks design. There are two types of nodes in this network are can be present: boot nodes and data nodes (peers). Boot nodes are for peers discovering. Peers are storing pinned data chunks and manifests and gives access for all other peers to this data. Peers are forming a distributed hash table (DHT) for all data they stored.

Each peer in the Network has a web interface with methods for storing and getting data. Implementation of storing and getting methods contains separate methods for storing manifests and data chunks. All data received for storing by the peer are stored locally in the database. These data are pinned data. Getting data algorithm is first search data in the local database, in case of absence data - search data in other peers by [DHT mechanism](https://en.wikipedia.org/wiki/Kademlia).

The database of the peer is based on classic [key-value database](https://en.wikipedia.org/wiki/Key-value_database). The data chunks have keys are the result of a hash algorithm of itself. KolmoLD now supports only [SHA-256](https://en.wikipedia.org/wiki/SHA-2) hash algorithm. The manifest key is the value of [doi::SHA256](/documentation/manifest/#doi) property from the manifest with a prefix. Storing manifests without this property is impossible.

To retrieve data from The KolmoLD Network use this [Java Script](/documentation/scripts).