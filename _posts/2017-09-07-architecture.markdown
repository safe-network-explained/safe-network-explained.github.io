---
layout: post
title:  "SAFE Network Architecture"
date:   2017-09-07 00:00:00 +0000
updated: 2017-09-07 00:00:00 +0000
categories: architecture
---
# SAFE Network Architecture

## A Secure Distributed Data Storage And Communications Network

## Summary

The SAFE network is an autonomous distributed network for data storage and communications [1]. It provides Secure Access For Everyone (SAFE). Data stored on the network has extremely high availability, durability, privacy and security. The network scales efficiently and the security of data stored on the network increases as the network grows.

## Introduction

The existing server-client-based internet gives ownership of data to whoever operates the servers, rather than the people creating the data. The operators can restrict, modify, remove or sell that data with no recourse from the user that created it. Poor acceptance and availability of federated protocols to distribute user data on terms that favour the creators has motivated the creation of the SAFE network.

Clients storing data on the SAFE network are protected by default with strong encryption and can control access through a flexible permissions layer.

Clients retrieving data from the SAFE network are protected by a secure routing and addressing system.

Clients benefit from secure defaults, including built-in end-to-end encryption and secure authentication.

The network is comprised of a graph of independently operated nodes (called vaults) that validate, store and deliver data. Vault operators can contribute to the retention of network data and network performance by supplying disc space and bandwidth for use by the network. Vault operators may join or leave the network at any time without affecting the security of data stored on the network.

Network tokens called Safecoin are distributed to vault operators by the network for providing these resources. The tokens may then be used to purchase network storage space for their own use or to utilise other resources on the network. This motivates benevolent behaviour of vault operators and protects the network against malicious behaviour.

The network utilises SHA3-256 identifiers for vaults and data in combination with XOR distances between these identifiers to anonymise and globally distribute all data and traffic.

Much of the existing internet infrastructure is improved by the SAFE network, including Addressing, Domain Name System, Transport Layer Security, Packet Routing, server software such as http web servers and imap mail servers, authentication layers such as oauth and openid; these are all superseded by secure-by-default modules that combine to make the SAFE network operable.

SAFE operates on existing physical internet infrastructure, but replaces all layers of the network from there up. It primarily targets OSI layers 3 to 7.

## Client Operations

Clients can upload and download data from the SAFE network. This section outlines how these operations are conducted.

### Resource Identifiers

Clients that wish to download data from the SAFE network require software that can translate SAFE resource identifiers to enpoints on the SAFE network, much like how browsers translate http URLs to endpoints on a server. Downloading data requires no special permissions or access, just software that can locate and interpret the data on the network.

Resources are stored on the network as content addressable resources. The identifier for these resources are SHA3-256 hashes of the resource content. This 256 bit identifier is used to retrieve a resource from the network (thus acting similarly to an IP address), allowing the client to specify which part of the network may be able to serve their request.

The 256 bit resource identifier may be represented in a human-friendly form using the built-in SAFE DNS, such as `safe://www.userX/video.mp4`. This can be converted to the 256 bit identifier for the file by a lookup on the SAFE network using software that can interpret SAFE DNS records.

### Self Encryption

Resources on the SAFE network are never more than 1 MB each. Clients working with files larger than 1 MB will have their data automatically split into 1 MB chunks that are then distributed across the network.

This means a typical file on the network consists of several parts: *chunks* which are individual 1 MB portions after the file is split, and a *datamap* which stores the identifier of each portion of the file. The network sees the datamap as just another chunk.

The client keeps a record of the resource identifier for the datamap. Thus, by initially retrieving that single resource (ie the datamap) the entire file can be retrieved despite being spread over many individual resources. This allows any resource to be addressed by a single resource identifier.

The datamap also acts as an encryption key for the chunks it refers to. Chunks are encrypted by that key, so vaults cannot read individual chunks to gain an insight to part of the original. This is known as Self Encryption [2].

Additionally, the file may be encrypted by the client before being uploaded using the encryption option built-in to the client software [3]. This is uses a unique secure key derived from the authentication the user has with the network. This means the key to decrypt the file never leaves the client and is never exposed to the network, allowing extremely secure storage of data which cannot be decrypted by any vault on the network, even with access to the datamap.

Splitting the file via self encryption has many benefits.

- chunks on their own are not useful, so all chunks are fungible and of equal value to the network
- chunks for a file can be downloaded in parallel by clients, increasing performance
- chunks have unique and widely distributed names, so cannot easily be correlated with each other
- wide distribution of chunks greatly reduces the chance of data loss when individual vaults go offline
- blacklisting based on content is not possible

### Immutable Data

Resource identifiers are determined by the content of that resource. This means two identical copies of a resource will have the same identifier (eg the same photo kept on my phone and my laptop). This comes with several benefits:

* resource identifiers are universal, unique and permanent (unlike urls on the current internet)
* resources cannot be duplicated on the network, increasing storage efficiency
* caching rules for resources are extremely simple and efficient
* encrypted resources are unique to the key that encrypts them, so cannot be decrypted by the vaults that store them

### Network Traversal

When a client connects to the network they are allocated a session identifier. The session identifier determines which vault acts as their entry point to the network.

When the client requests a resource, the request is sent to their entry point vault, which is then routed via several other vaults on the network before finally reaching the vault storing the data being requested. The data is then passed back along the route to the client.

This routing mechanism traverses a 256 bit namespace progressively via closest XOR distance [4].

The resource being requested has a unique 256 bit identifier determined by the self encryption process. The vault acting as the entry point for the client also has a unique 256 bit identifier (as do all vaults).

The chunk identifier is XORed with the entry point vault identifier. The resulting value is the XOR distance between the chunk and the vault.

If a neighbouring vault has an identifier with a smaller XOR distance to the chunk, the request is passed to that vault.

This continues until there is no vault with a closer XOR distance to the chunk.

The chunk will be stored in the vault with the identifier closest to the chunk identifier (measured using XOR distance).

That vault checks if it has a copy of the requested chunk and returns the response back along the route the request came from.

This process forms a request chain where each vault only knows the details of the node one step away in the chain. The original requester and the vault storing the chunk are separated by this chain of vaults, making the request anonymous.

The same network traversal happens when storing chunks. Chunks enter the network via their entry point vault, and are then passed on until they reach the closest vault, which then stores the chunk.

Nodes along the route can cache the chunk so if another request for it is made later, perhaps by a different route, it can be responded to sooner without having to extend the request all the way to the final storage vault.

### Messaging

Email and instant messaging are ubiquitous experiences of the current internet. The SAFE network facilitates messaging which can replace imap / smtp / xmpp servers.

A message may be stored on the network the same way any resource may be stored. To ensure the message remains private, the message may be encrypted prior to sending using the recipients key (that key can also be stored as a resource on the safe network). By the same method, connections between contacts (like the gpg web of trust) may also be represented on the network by resources. This collection of resources form the foundation for a secure messaging platform.

The only remaining step is to notify the recipient when they have received a message.

On the current internet, new messages are typically presented to the user by adding a message to their inbox. Since an inbox is simply a list of messages, this list may be represented as a resource on the network. However, in the case of the SAFE network, the inbox is created not as immutable data, but as mutable data. This second data type has a fixed identifier which does not depend on the content of the resource. This, combined with a permissions system, allows data at that identifier to be updated.

This data type allows the recipient to be notified of their new message by updating the data located at the identifier for the recipients inbox. The inbox resource can be created with permission for anyone to append new data. The sender of the message appends the identifier of their message to the inbox of the recipient, and the recipient is able to locate the new message.

This messaging system facilitates features such as email and instant messaging, but also introduces scope for other systems based on messaging such as payments, smart contracts, social networking, inter-process signalling, dynamic web content, stun servers for webrtc...

### Mutable Data

Mutable data [5] is the second and final data type present on the network. It allows data at a fixed location on the network to be modified.

A strong permissions layer allows the owner of the mutable data to specify who may change it and how they may change it.

The validation of ownership and modification of mutable data is performed by the network using digital signatures. The owner of mutable data can specify which keys are permitted or denied modifications to the data.

Each key is permitted or denied specific actions to the existing content. These actions are either 'update' or 'append'.

By combining clearly defined permissions with cryptographically secure signatures, the modifications to mutable data may be strictly controlled by the owner.

The content of mutable data may point to other mutable data, allowing the creation of chains of mutable data [6] that can be used for many purposes such as version control and branching, verifiable history and data recovery.

## Network Operations

There are several operations performed by the network that allow clients to store and retrieve data securely and reliably. These operations give rise to an autonomous self-healing network which is resistant to attack.

### Close Group Consensus

Nodes on the network (known as vaults) are primarily responsible for storing chunks of data. The availability of chunks is critical to the success of the network.

Since anyone may add or remove their vaults from the network at any time, the network must be able to detect and respond to malicious behaviour before it results in the loss of data. This requires enforcement of a set of rules governing acceptable behaviour of vaults. Vaults which don't comply with the rules are rejected from the network and are not responsible for client data.

The enforcement of the rules occurs through a process called Close Group Consensus [7].

Vaults form groups which coordinate with each other to reach consensus about the state of the data on the network. Any vault which does not comply with the consensus of the group is rejected and replaced by a different vault.

The rules that groups enforce are that data:

- can be stored
- can be retrieved
- has not been modified

This depends on vaults in the group having:

- space available to store new data
- bandwidth available to relay that data when it's requested
- sufficient participation in achieving consensus with the rest of the group

### Vault Naming

All vaults are allocated a random unique 256 bit identifier by the network upon joining or rejoining. Vaults that are close together (measured by the XOR distance between their identifiers) form groups. The vaults in a group work together to form consensus about data on the network so it may be stored and retrieved. Groups are formed in sets of between 8 and 16 close vaults. The more vaults on the network, the greater the number groups on the network.

If a majority of vaults in a group are dishonest, the data in that group is vulnerable to corruption.

This means the group size is a trade off between being large enough that it's difficult to gain control of a majority of vaults in the group and being small enough that consensus can be reached quickly.

The security of the network primarily arises because the attacker *may not choose the identifier for their vault*. They must join and leave repeatedly until the network allocates their vault an identifier in the group they are attempting to control. They must do this for a majority of vaults in a group in order to control consensus for that group.

The difficulty of controlling a group is thus determined by the size of the network, becoming proportionally harder as the network grows in size. The larger the network the more groups there are; the more groups there are the harder it is to join any one specific group.

Overcoming the network-allocated-identifier for new vaults is the primary source of difficulty in trying to abuse the consensus mechanism.

### Disjoint Sections

A practical consideration of the formation of groups is the efficiency of inter-group messaging (ie consensus of group membership rather than consensus of individual vault behaviour).

Groups are formed based on the similarity of the leading bits of their identifier (this portion of the identifier is called the Prefix for the Section).

This makes the coordination of vaults leaving or joining the group much simpler.

If groups were to retain a constant membership of 8 vaults, there's a need to reorganizing individual vaults between groups as new vaults join and leave. This may have a cascading effect on nearby groups.

Rather than do this cascading reorganization, groups may vary in size between 8 and 16 vaults. If a group becomes larger than 16 vaults it's split into two new groups, and if it becomes less than 8 vaults it's merged back into the nearest group.

This method of organizing groups is called Disjoint Sections [8].

### Churn

Vaults are established in random locations on the network to prevent attacks on the group consensus mechanism. This security is further strengthened by intermittent relocation of vaults. An attack on the group must happen before relocation happens.

A vault is relocated by the network by allocating it a new random identifier. This causes it to leave it's existing group and become part of a new group. Any chunks it was previously storing become the responsibility of a different vault and is automatically handled by the group consensus mechanism. The relocated vault must now store chunks that are closest to it's new identifier and continue building consensus with a new group of vaults.

This mechanism is known as churn and is an extension of the same process that happens when new vaults join the network or existing vaults leave.

### Farming

The incentive for vault operators to join the network and cooperate toward the goal of secure data storage is motivated by a network token. This token (called safecoin [9]) can be redeemed for resources on the network or used to engage with other resources offered on the network. The motivation for the token is similar to that of blockchains - to ensure cooperation participation is a more rational course of action than uncooperative participation.

Tokens are represented as mutable data on the network. The network defines a total of 2^32 mutable data resources as safecoin objects, which initially have no owner so do not exist as far as the token economy is concerned.

The network intermittently allocates unowned safecoins to network participants via a Proof Of Resource mechanism, which causes the total number of tokens to grow over time, acting as a bootstrapping mechanism for the token and network storage economy.

The total number of safecoins allocated may also shrink when safecoins are exchanged for network resources. Users must exchange safecoin to store data on the network. This involves submitting safecoin to the network which will then clear the safecoin owner and allocate network storage space for that user to consume. This mechanism is known as coin recycling and provides a counterbalance to the growth of safecoin allocated through Proof Of Resource.

Safecoin is transferred between users by digitally signing a transfer of ownership for the safecoin mutable data resource to the recipients key. The digital signature is easily verified using the key of the existing owner stored in the mutable data resource. Once the owner is updated the transfer is complete, making safecoin transfers extremely fast, efficient and secure.

### Proof Of Resource

When cooperative behaviour from vaults is observed by the network, the vault earns the right to claim ownership of a safecoin. The specific safecoin identifier for each claim is randomly generated by the network.

If the safecoin for that identifier is not currently owned, the network assigns it to the owner of the vault, thus minting a new safecoin into the economy.

If the safecoin for that identifier is already owned no further action is taken.

In this sense, much like blockchain proof of work, safecoin farming has an element of chance that should lead to an even distribution proportional to the resources supplied.

The rate at which safecoin is allocated is adjusted in a similar way to the blockchain difficulty mechanism. The adjustment aims to balance the availability of resources on the network. It encourages the provision of more resources when needed and discourages wasteful excess by reducing reward during times of oversupply.

The safecoin disbursement algorithm has not yet been formally specified or implemented.

The network defines cooperative behaviour as the reliable supply of bandwidth and storage space, and continued involvement in reaching consensus. Unlike blockchain Proof Of Work, where end users receive diminishing returns from further growth in mining capacity, the Proof Of Resource mechanism continues to provide utility to end users proportional to the growth of network resources.

## Conclusion

The SAFE network is an autonomous network for reliable data storage and communications. The combination of a robust data storage and messaging system forms a secure and private alternative to much of the existing infrastructure of the internet.

Data is stored efficiently and reliably on the network using content addressable resource identifiers and self encryption.

Data is retained without corruption and may be retrieved at any time through the use of close group consensus and disjoint sections.

A network allocated token to incentivise the supply of resources within the agreed rules of the system discourages malicious behaviour and ensures the sustainable supply of resources for future clients. The distribution is based on a Proof Of Resource mechanism which is difficult to cheat and exhibits positive externality.

The network improves in speed, security and reliability as more vaults are added, making rapid increases in scale a benefit rather than a concern.

End users benefit from secure-by-default modules and a flexible permissions layer to control access in the manner best suit their needs. The SAFE network combines many individual modules to create a network with Secure Access For Everyone.

## References

[1] David Irvine, Fraser Hutchison, Steve Mücklisch, "Autonomous Network", 2010, [http://docs.maidsafe.net/Whitepapers/pdf/AutonomousNetwork.pdf](http://docs.maidsafe.net/Whitepapers/pdf/AutonomousNetwork.pdf)

[2] David Irvine, "Self Encrypting Data", 2010, [http://docs.maidsafe.net/Whitepapers/pdf/SelfEncryptingData.pdf](http://docs.maidsafe.net/Whitepapers/pdf/SelfEncryptingData.pdf)

[3] David Irvine, "Self-Authentication", 2010, [http://docs.maidsafe.net/Whitepapers/pdf/SelfAuthentication.pdf](http://docs.maidsafe.net/Whitepapers/pdf/SelfAuthentication.pdf)

[4] "Structuring Networks With XOR", 2016, [https://blog.maidsafe.net/2016/05/27/structuring-networks-with-xor/](https://blog.maidsafe.net/2016/05/27/structuring-networks-with-xor/)

[5] Nikita Baksalyar, "Mutable Data", 2016, [https://github.com/maidsafe/rfcs/blob/master/text/0047-mutable-data/0047-mutable-data.md](https://github.com/maidsafe/rfcs/blob/master/text/0047-mutable-data/0047-mutable-data.md)

[6] David Irvine, Data Chains, 2016, [https://github.com/maidsafe/rfcs/blob/master/text/0029-data-chains.md/0029-data-chains.md](https://github.com/maidsafe/rfcs/blob/master/text/0029-data-chains.md/0029-data-chains.md)

[7] "Introduction & Technical Overview Of Safe Consensus", 2016, [https://blog.maidsafe.net/2016/06/23/introduction-technical-overview-of-safe-consensus/](https://blog.maidsafe.net/2016/06/23/introduction-technical-overview-of-safe-consensus/)

[8] Andreas Fackler, "Disjoint Sections", 2016, [https://github.com/maidsafe/rfcs/blob/master/text/0037-disjoint-groups/0037-disjoint-groups.md](https://github.com/maidsafe/rfcs/blob/master/text/0037-disjoint-groups/0037-disjoint-groups.md)

[9] Nick Lambert, Qi Ma, David Irvine, "Safecoin: The Decentralised Network Token", 2015, [http://docs.maidsafe.net/Whitepapers/pdf/Safecoin.pdf](http://docs.maidsafe.net/Whitepapers/pdf/Safecoin.pdf)
