---
layout: post
title: "IPFS P2P File Sharing System Whitepaper Overview"
author: "Anne Ouyang"
# categories: blog
# tags: [distributed-systems, paper-reading]
image: 
---

The [InterPlanetary File System (IPFS)](https://ipfs.io/) is a peer-to-peer network of a distributed file system (whitepaper [here](https://github.com/ipfs/ipfs/blob/master/papers/ipfs-cap2pfs/ipfs-p2p-file-system.pdf)). The nodes in this network store a generated public-private key pair and are identified by the cryptographic hashes of their public keys. IPFS can be used on overlay networks to provide features such as integrity, reliability, and authenticity even if the underlying network doesn't support them. Similar to BitTorrent, IPFS uses a distributed hashing table for addressing peers and finding peers who have particular objects. 

Data distribution happens by exchanging blocks via the [BitSwap Protocol](https://github.com/ipfs/go-bitswap/blob/master/docs/how-bitswap-works.md) inspired by BitTorrent; however, the BitSwap protocol enables nodes to acquire any blocks they need rather than being limited to the ones in a single torrent. BitSwap operates as a barter system where nodes have a "have list" and a "want list" which they need to "work" for. To prevent leeches, the probability of a node sending to a debtor node decreases and the debt increases. In practice, a sigmoid function can be used: 
$$P(\text{send} | r) = 1 - \frac{1}{1+e^{(6-3r)}}$$, where $$r$$ is the debt ratio and defined as $$\frac{\text{bytes_sent}}{\text{bytes_received+1}}$$.

The protocol happens in several stages for a successful exchange,  including sharing ledgers, sharing want lists, sending blocks, and closing the connection.

Data is stored in a Merkle DAG structure that enables content addressing, tamper resistance, and deduplication. The object model of IPFS is similar to that of Git's, so version control tools can be available to IPFS users. IPFS objects can be traversed with a string path API and the lookup performance can be improved by caching or flattening trees. 

The Merkle DAG contains permanent objects and IPNS enables mutable pointers to the Merkle DAG. Mutable, self-certified names can be constructed in a cryptographically assigned global namespace. IPNS is not inherently human-friendly, and this issue can be mitigated through solutions such as peer links, name shortening, pronounceable identifiers, and DNS TXT records. 

IPFS offers benefits such as being DDoS-resistant, being censorship-resistant, saving bandwidth, preventing hotspots from becoming bottlenecks, and making content permanent.

The paper does not seem to address security issues related to IPFS. Based on the design of IPFS, anyone with the hash of a file can acquire the contents of the file. There's currently no mechanism for managing file permissions, and it is up to the user to encrypt confidential files themselves. Deduplication and encryption can be conflicting goals. On the other hand, an opposite problem can be content discovery and indexing content without knowing hashes, which can make it difficult to build a search engine. 

Another challenge of IPFS is serving dynamic websites. It seems that IPFS is better suited for transferring static files rather than supporting real time interactive applications. 

In addition, IPFS is only useful when users actually participate in the network. Currently, to incentivize adoption, [filecoin](https://filecoin.io/) can be earned by users hosting files, and it seems to be a more environment-friendly mining mechanism than the proof of work mechanism used by Bitcoin. 

