---
layout: post
title:  "Safe Network implementation explained in plain language"
date:   2016-05-10 00:00:00 +0000
updated: 2016-05-10 00:00:00 +0000
categories: bitcoin
---
This post explains the answers to some common questions about the safe network, with references back to the source code. Even if you can't 'read the source', you should be able to follow the explanations since they're in plain language.

If these explanations are too technical it may be best to ask for clarification for your particular question on the [safe network forum](https://forum.safenetwork.io/).

These questions are particularly about the *operation* of the safe network, as implemented by the code. This may not necessarily match the designed or intended operation. However, as we are about to find out, design and implementation are extremely closely matched (as expected!).

A small disclaimer: this is not official documentation, it's managed by the community.

## Table of contents

### Vaults

<ul>
<li><a id="new-vault-peer-discovery_toc" href="#new-vault-peer-discovery" rel="nofollow">How does a new vault discover their initial peers?</a></li>
<li><a id="how-is-close-group-consensus-reached_toc" href="#how-is-close-group-consensus-reached" rel="nofollow">How is close group consensus reached?</a></li>
<li><a id="how-are-vaults-named-by-the-network_toc" href="#how-are-vaults-named-by-the-network" rel="nofollow">How are vaults named by the network?</a></li>
<li><a id="what-happens-when-a-vault-comes-online_toc" href="#what-happens-when-a-vault-comes-online" rel="nofollow">What happens when a vault comes online?</a></li>
<li><a id="what-happens-when-a-vault-goes-offline_toc" href="#what-happens-when-a-vault-goes-offline" rel="nofollow">What happens when a vault goes offline?</a></li>
</ul>

### Client Data

<ul>
<li><a id="data-transition-to-network_toc" href="#data-transition-to-network" rel="nofollow">How does data transition from a file on my computer to being stored on vaults?</a></li>
<li><a id="data-fetched-from-network_toc" href="#data-fetched-from-network" rel="nofollow">How is data fetched from the network?</a></li>
<li><a id="difference-between-private-and-public-data_toc" href="#difference-between-private-and-public-data" rel="nofollow">What is the difference between private and public data?</a></li>
<li><a id="how-is-data-deleted-from-the-network_toc" href="#how-is-data-deleted-from-the-network" rel="nofollow">How is data deleted from the network?</a></li>
<li><a id="how-many-redundant-copies-are-stored_toc" href="#how-many-redundant-copies-are-stored" rel="nofollow">How many redundant copies are stored?</a></li>
</ul>

### Farming

<ul>
<li><a id="proof-of-resource-algorithm_toc" href="#proof-of-resource-algorithm" rel="nofollow">How does proof of resource algorithm work?</a></li>
<li><a id="vault-ranking-algorithm_toc" href="#vault-ranking-algorithm" rel="nofollow">How does the vault ranking algorithm work?</a></li>
<li><a id="farming-speed-algorithm_toc" href="#farming-speed-algorithm" rel="nofollow">How is farming speed for each vault set by the network?</a></li>
<li><a id="proof-of-resource-conversion-to-safecoin_toc" href="#proof-of-resource-conversion-to-safecoin" rel="nofollow">How is proof of resource converted into safecoin?</a></li>
</ul>

### Safecoin

<ul>
<li><a id="spending-safecoin_toc" href="#spending-safecoin" rel="nofollow">How does spending safecoin (ie changing structured data) work?</a></li>
<li><a id="safecoin-management_toc" href="#safecoin-management" rel="nofollow">How is safecoin managed and how do wallets work?</a></li>
</ul>

### Apps

<ul>
<li><a id="how-is-general-structured-data-created_toc" href="#how-is-general-structured-data-created" rel="nofollow">How is general structured data created?</a></li>
<li><a id="how-is-structured-data-updated_toc" href="#how-is-structured-data-updated" rel="nofollow">How is structured data updated?</a></li>
</ul>

### How does a new vault discover their initial peers? {#new-vault-peer-discovery}

New vaults must discover peers in the network. If the vault initially connects to malicious peers, they would no longer be participating in 'the' safe network. Understanding peer discovery involves quite a deep dive from the vault layer down through the routing layer and ultimately ending up at the crust layer.

When a vault is created one of the first things it does is [create a routing node](https://github.com/maidsafe/safe_vault/blob/85fb34dbca35f64c85bbef01f76bab0fc4dcd797/src/vault.rs#L58). The full implementation of 'routing node' can be found in the routing repository, specifically the file [node.rs](https://github.com/maidsafe/routing/blob/a8ee5ce4ba6356830ce2f38675773fcedc3da13b/src/node.rs).

The functionality of routing nodes that's important for peer discovery is sending and receiving actions. The 'actions' functionality is [contained by the action_sender object](https://github.com/maidsafe/routing/blob/a8ee5ce4ba6356830ce2f38675773fcedc3da13b/src/node.rs#L69), which is an instance of 'routing core'. Routing core is described by [this comment](https://github.com/maidsafe/routing/blob/a8ee5ce4ba6356830ce2f38675773fcedc3da13b/src/core.rs#L146) as an 'interface for clients and nodes that handles routing and connecting to the network'. Let's confirm how this is implemented in routing core and how it initially discovers peers of the network.

Part of the creation of a routing core object involves [creating a new crust service](https://github.com/maidsafe/routing/blob/a8ee5ce4ba6356830ce2f38675773fcedc3da13b/src/core.rs#L269). Crust Service is the point at which the connection to the network is initiated.

Initializing a new crust service creates a variable [bootstrap_contacts](https://github.com/maidsafe/crust/blob/d73e34ca806f13dd15a7bcad19d6feb653536f09/src/service.rs#L180), which is initiated by the return value of the function [get_known_contacts](https://github.com/maidsafe/crust/blob/d73e34ca806f13dd15a7bcad19d6feb653536f09/src/bootstrap.rs#L138). This function reads first [from the bootstrap_cache](https://github.com/maidsafe/crust/blob/d73e34ca806f13dd15a7bcad19d6feb653536f09/src/bootstrap.rs#L150), then [from the hard_coded_contacts](https://github.com/maidsafe/crust/blob/d73e34ca806f13dd15a7bcad19d6feb653536f09/src/bootstrap.rs#L153) in the configuration. Using 'extend' means that priority is given to the bootstrap_cache, and hard_coded_contacts is only used as a fallback. This is reiterated by [the comment not to use HashSet](https://github.com/maidsafe/crust/blob/d73e34ca806f13dd15a7bcad19d6feb653536f09/src/bootstrap.rs#L161) because 'we want to preserve the order in which we collect the contacts'.

Interestingly, the maximum contacts for initializing connection to the network is defined by the constant value [MAX_CONTACTS_EXPECTED](https://github.com/maidsafe/crust/blob/d73e34ca806f13dd15a7bcad19d6feb653536f09/src/bootstrap.rs#L44), which is 1500.

bootstrap_cache is [currently disabled](https://github.com/maidsafe/crust/blob/d73e34ca806f13dd15a7bcad19d6feb653536f09/src/bootstrap_handler.rs#L26), which makes sense while the network is under testing where previous connections are no longer relevant in new test networks. The main point of interest is that the bootstrap cache is [stored in the file .bootstrap.cache](https://github.com/maidsafe/crust/blob/d73e34ca806f13dd15a7bcad19d6feb653536f09/src/bootstrap_handler.rs#L109). Keeping this file secure is important!

bootstrap_cache is automatically updated throughout the normal running of the node. The [bootstrap cache is updated](https://github.com/maidsafe/crust/blob/d73e34ca806f13dd15a7bcad19d6feb653536f09/src/service.rs#L387) after every successful connection to a new peer. This calls the method [insert_contacts](https://github.com/maidsafe/crust/blob/d73e34ca806f13dd15a7bcad19d6feb653536f09/src/bootstrap_handler.rs#L58), which ultimately results in [the newest bootstrap cache being written to the .bootstrap.cache file](https://github.com/maidsafe/crust/blob/d73e34ca806f13dd15a7bcad19d6feb653536f09/src/bootstrap_handler.rs#L103).

The final part to understanding peer discovery is hard_coded_contacts. The config file is [read from the file .crust.config](https://github.com/maidsafe/crust/blob/d73e34ca806f13dd15a7bcad19d6feb653536f09/src/config_handler.rs#L80), which obviously must be initially sourced from a secure verified source, and also kept secure once obtained. The structure of the config file, in particular the values for hard_coded_contacts, can be seen in the [sample.config file of the crust installer](https://github.com/maidsafe/crust/blob/d73e34ca806f13dd15a7bcad19d6feb653536f09/installer/sample.config).

In summary, bootstrapping happens from previously known nodes stored in the bootstrap_cache, falling back to hard_coded_contacts in the supplied configuration file.

[Back to Table of contents](#new-vault-peer-discovery_toc)

### How is close group consensus reached?

Each piece of data has 8 vaults keeping consensus over that data (these vaults are acting with the [DataManager persona](https://github.com/maidsafe/safe_vault/blob/fd117188abf4e9383d5a5ade3a752c184bab5fe0/src/personas/data_manager.rs)). 5 of those vaults must agree for the data to be considered valid. This is different to the intended design of 32 members and 28 for consensus, but will be corrected closer to the network being made live.

Consensus of data is maintained by vaults calling send_refresh whenever a vault has a change-event (ie [node added](https://github.com/maidsafe/safe_vault/blob/373169a7ab0798e2ef5009028670b298cc0d1c13/src/personas/data_manager.rs#L473), [node lost](https://github.com/maidsafe/safe_vault/blob/373169a7ab0798e2ef5009028670b298cc0d1c13/src/personas/data_manager.rs#L522), [data added](https://github.com/maidsafe/safe_vault/blob/373169a7ab0798e2ef5009028670b298cc0d1c13/src/personas/data_manager.rs#L185), [data updated](https://github.com/maidsafe/safe_vault/blob/373169a7ab0798e2ef5009028670b298cc0d1c13/src/personas/data_manager.rs#L207), [data removed](https://github.com/maidsafe/safe_vault/blob/373169a7ab0798e2ef5009028670b298cc0d1c13/src/personas/data_manager.rs#L239)).

This call affects the group of nodes known as 'close nodes'. Close nodes is calculated by the method [other_close_nodes()](https://github.com/maidsafe/safe_vault/blob/373169a7ab0798e2ef5009028670b298cc0d1c13/src/personas/data_manager.rs#L444), which is [implemented in the kademlia repository](https://github.com/maidsafe/kademlia_routing_table/blob/master/src/lib.rs#L426). This returns a list of nodes with length [GROUP_SIZE-1](https://github.com/maidsafe/kademlia_routing_table/blob/1b419c2b92579bd8e0667c936d16bcbbd74d2bca/src/lib.rs#L429) where [GROUP_SIZE is 8](https://github.com/maidsafe/kademlia_routing_table/blob/1b419c2b92579bd8e0667c936d16bcbbd74d2bca/src/lib.rs#L154); so there are 7 other_close_nodes, plus ourself making 8 nodes in the group. The closeness of nodes is calculated using [cmp_distance in the xor_name repository](https://github.com/maidsafe/xor_name/blob/31fec956178b02038c590a6fab859e63b6329a23/src/lib.rs#L172).

Consensus is managed using a data structure called an accumulator (see the [accumulator repository](https://github.com/maidsafe/accumulator) for the full implementation). Let's look at how the accumulator is used by vaults.

Of the 8 nodes in the close group, only a certain subset of nodes (as defined by [the refresh_accumulator for the vault](https://github.com/maidsafe/safe_vault/blob/354ac71d8a67db6fb59fce3ef3fec0d01387bb70/src/personas/data_manager.rs#L78)) must agree with each other for the data to be considered consistent. [ACCUMULATOR_QUORUM](https://github.com/maidsafe/safe_vault/blob/354ac71d8a67db6fb59fce3ef3fec0d01387bb70/src/personas/data_manager.rs#L38) is GROUP_SIZE / 2 + 1 and [GROUP_SIZE is 8](https://github.com/maidsafe/kademlia_routing_table/blob/1b419c2b92579bd8e0667c936d16bcbbd74d2bca/src/lib.rs#L154). So the quorum is 8/2+1 = 5.

Now we know the group size and quorum size, but where does the actual data check happen? The answer is on [data_manager.rs:L336](https://github.com/maidsafe/safe_vault/blob/373169a7ab0798e2ef5009028670b298cc0d1c13/src/personas/data_manager.rs#L336). The vault will take action on this data only if the quorum has been reached ([here's where the quorum test is implemented, in the accumulator repository](https://github.com/maidsafe/accumulator/blob/d8162eafc02897027b308a468331f87418f53a8c/src/lib.rs#L113)). If quorum is reached, then on [L342](https://github.com/maidsafe/safe_vault/blob/373169a7ab0798e2ef5009028670b298cc0d1c13/src/personas/data_manager.rs#L342), the vault *checks whether it needs to fetch this data or not*. This is how the accumulator is filled and consensus can be formed. If the vault doesn't have the data yet, it must fetch it using [send_gets_for_needed_data](https://github.com/maidsafe/safe_vault/blob/354ac71d8a67db6fb59fce3ef3fec0d01387bb70/src/personas/data_manager.rs#L366), specifically on [L409 where send_get_request is called](https://github.com/maidsafe/safe_vault/blob/354ac71d8a67db6fb59fce3ef3fec0d01387bb70/src/personas/data_manager.rs#L409) to fetch the data from other close group vaults.

The data will be fetched from data_holders, which [only contains nodes from the close group](https://github.com/maidsafe/safe_vault/blob/354ac71d8a67db6fb59fce3ef3fec0d01387bb70/src/personas/data_manager.rs#L335). As the network changes, the nodes forming the close group change. Data is shared to newcomers of the close group and dropped if the vault is pushed out of the close group.

Of interest here is that vaults which are too slow to respond are [removed from consensus formation](https://github.com/maidsafe/safe_vault/blob/354ac71d8a67db6fb59fce3ef3fec0d01387bb70/src/personas/data_manager.rs#L383), known as expired_gets.

[Back to Table of contents](#how-is-close-group-consensus-reached_toc)

### How are vaults named by the network?

An attacker that wants to modify or control data will try to minimize the distance between their vaults to gain control of consensus, so it's important to understand how they can (or can't) do that.

The vault name is the only variable in calculating distance to other vaults to form a close group, so this is where the attacker will focus their efforts. The name for a vault is generated by the following steps (outlined in fairly plain language in [this long comment](https://github.com/maidsafe/routing/blob/3582f8d0994e31707a4b46c006129b6b36a63649/src/core.rs#L148)):

* The vault chooses a name for itself. [See L124](https://github.com/maidsafe/routing/blob/3582f8d0994e31707a4b46c006129b6b36a63649/src/id.rs#L124)

* The node requests to be upgraded to a full node by the network. [Source comment](https://github.com/maidsafe/routing/blob/3582f8d0994e31707a4b46c006129b6b36a63649/src/core.rs#L171) and [Source code](https://github.com/maidsafe/routing/blob/3582f8d0994e31707a4b46c006129b6b36a63649/src/core.rs#L1442).

* The network combines the chosen vault name with the names of the two closest vaults on the network to generate a new name. [Source code](https://github.com/maidsafe/routing/blob/3582f8d0994e31707a4b46c006129b6b36a63649/src/utils.rs#L80) - close nodes are [sorted by closest first](https://github.com/maidsafe/kademlia_routing_table/blob/1b419c2b92579bd8e0667c936d16bcbbd74d2bca/src/lib.rs#L531) and [truncated to 2](https://github.com/maidsafe/routing/blob/3582f8d0994e31707a4b46c006129b6b36a63649/src/utils.rs#L71)

* The network sends this new name back to the vault. [Source code](https://github.com/maidsafe/routing/blob/0363ec5686ca10e960c87c4f9d9f3454df36b362/src/core.rs#L1798)

* The vault _must_ use this new name. [Source code](https://github.com/maidsafe/routing/blob/3582f8d0994e31707a4b46c006129b6b36a63649/src/core.rs#L1885). Note the new node will not be added to the network unless it uses the new name. [Source code](https://github.com/maidsafe/routing/blob/0363ec5686ca10e960c87c4f9d9f3454df36b362/src/core.rs#L1536)

The third point is what makes it so difficult for an attacker to gain a foothold. It's extremely difficult to predict the final name the network will issue, which ultimately means it's extremely difficult to form a group of close nodes on the network.

There's an interesting edge case to consider. How does the very first vault join the network? It can't get a name from the network since there's not two other vaults to complete step three above.

This edge case is handled in routing core. In this case, [the original name the vault gave itself is used](https://github.com/maidsafe/routing/blob/a8ee5ce4ba6356830ce2f38675773fcedc3da13b/src/core.rs#L551).

[Back to Table of contents](#how-are-vaults-named-by-the-network_toc)

### What happens when a vault comes online?

[Back to Table of contents](#what-happens-when-a-vault-comes-online_toc)

### What happens when a vault goes offline?

[Back to Table of contents](#what-happens-when-a-vault-goes-offline_toc)

### How does data transition from a file on my computer to being stored on vaults? {#data-transition-to-network}

This explains the process of self-encryption and outlines the layers of the network that facilitate data storage.

It should be pointed out that this all happens behind the scenes, and the api exposed for use by applications is extremely simple.

Before this explanation kicks off, it's worth reading the plain language description of [routing](http://maidsafe.net/routing/master/routing/index.html) on the maidsafe site, which covers a lot of the concepts used in this explanation. There's also an [explanation of the put flow](https://github.com/maidsafe/safe_vault/blob/87c64d6c74a403629b48d34bee9508706ca083b9/docs/put_flow.md) in the safe vault docs.

Data (aka file content) is managed by the client using [SelfEncryptor](https://github.com/maidsafe/self_encryption/blob/1d0b1e268bce5b1300b23509a222a0b0dce26852/src/lib.rs#L361) objects. It looks after the chunks of data, the datamap, the filesize.

The SelfEncryptor methods of [read](https://github.com/maidsafe/self_encryption/blob/1d0b1e268bce5b1300b23509a222a0b0dce26852/src/lib.rs#L439), [write](https://github.com/maidsafe/self_encryption/blob/1d0b1e268bce5b1300b23509a222a0b0dce26852/src/lib.rs#L427) and [close](https://github.com/maidsafe/self_encryption/blob/1d0b1e268bce5b1300b23509a222a0b0dce26852/src/lib.rs#L452) are designed to "loosely mimic a filesystem interface for easy connection to FUSE like programs as well as fine grained access to system level libraries for developers." - ie regular files and SelfEncryptor objects can be used the same way by developers; 'save a regular file to a hard drive' works the same as 'save a SelfEncryptor to the safe network'. App developers don't need to learn new interfaces, they just continue using normal file operations.

When a SelfEncryptor is created, it [uses a sequencer](https://github.com/maidsafe/self_encryption/blob/1d0b1e268bce5b1300b23509a222a0b0dce26852/src/lib.rs#L391) to split the content into [1MB](https://github.com/maidsafe/self_encryption/blob/1d0b1e268bce5b1300b23509a222a0b0dce26852/src/lib.rs#L149) chunks. These chunks are encrypted before being sent to the network.

The close method of SelfEncryptor is where the heavy lifting happens. "Content temporarily held in self_encryptor will only get flushed into storage when this function gets called." - ie network activity for writing the file only happens when the close method is called.

Storage is specified as a simple key value store with only [get](https://github.com/maidsafe/self_encryption/blob/1d0b1e268bce5b1300b23509a222a0b0dce26852/src/lib.rs#L353) and [put](https://github.com/maidsafe/self_encryption/blob/1d0b1e268bce5b1300b23509a222a0b0dce26852/src/lib.rs#L355) methods required to be implemented. The exact implementation of this storage object will be discussed soon, and may for now be conceptualized as simply 'the safe network' like you may conceptualize 'the internet'. This is a very powerful yet simple abstraction indeed!

Chunks are [encrypted](https://github.com/maidsafe/self_encryption/blob/1d0b1e268bce5b1300b23509a222a0b0dce26852/src/lib.rs#L522) when data is written to storage (ie calling the close method of the SelfEncryptor). Chunks are [decrypted](https://github.com/maidsafe/self_encryption/blob/1d0b1e268bce5b1300b23509a222a0b0dce26852/src/lib.rs#L643) when the read method is called.

The actual [encryption](https://github.com/maidsafe/self_encryption/blob/1d0b1e268bce5b1300b23509a222a0b0dce26852/src/encryption.rs#L28) and [decryption](https://github.com/maidsafe/self_encryption/blob/1d0b1e268bce5b1300b23509a222a0b0dce26852/src/encryption.rs#L32) of chunks is performed by secretbox, which ultimately depends on the libsodium crypto library. The secretbox binding to libsodium is in [the sodiumoxide fork](https://github.com/maidsafe/sodiumoxide/blob/a9646ce47ae424f8085358e9f85631ec9eba05d7/src/crypto/secretbox/xsalsa20poly1305.rs#L59) maintained by MaidSafe. The encryption method is [crypto_secretbox_xsalsa20poly1305](https://github.com/maidsafe/sodiumoxide/blob/a9646ce47ae424f8085358e9f85631ec9eba05d7/src/crypto/secretbox/xsalsa20poly1305.rs#L1), "a particular combination of Salsa20 and Poly1305 specified in Cryptography in NaCl".

So far we've covered how data is prepared on the client-side before putting it on the safe network. What happens to this data after it's prepared?

The mechanism for storing data is up to the client, but we can use the example from the [nfs implementation in safe_core](https://github.com/maidsafe/safe_core/tree/69f646db0b7cd905d54579df4125f1b9ae924661/src/nfs), which implements a [reader](https://github.com/maidsafe/safe_core/blob/69f646db0b7cd905d54579df4125f1b9ae924661/src/nfs/helper/reader.rs) and [writer](https://github.com/maidsafe/safe_core/blob/69f646db0b7cd905d54579df4125f1b9ae924661/src/nfs/helper/writer.rs) (both of which use the SelfEncryptor). These methods tap into the first layer of the safe network, specified in [core/self_encryption_storage.rs](https://github.com/maidsafe/safe_core/blob/bc9994b5fb95fff610ce0de3afac6b63bdcace02/src/core/self_encryption_storage.rs). Both the [get](https://github.com/maidsafe/safe_core/blob/bc9994b5fb95fff610ce0de3afac6b63bdcace02/src/core/self_encryption_storage.rs#L46) and the [put](https://github.com/maidsafe/safe_core/blob/bc9994b5fb95fff610ce0de3afac6b63bdcace02/src/core/self_encryption_storage.rs#L59) methods use the 'client' object to execute these actions.

Client is specified in [core/client](https://github.com/maidsafe/safe_core/tree/bc9994b5fb95fff610ce0de3afac6b63bdcace02/src/core/client). Client makes heavy use of routing. Routing is managed by the client using a [routing object](https://github.com/maidsafe/safe_core/blob/bc9994b5fb95fff610ce0de3afac6b63bdcace02/src/core/client/mod.rs#L78). This object manages connections to the safe network. The get action from the client [passes to get](https://github.com/maidsafe/safe_core/blob/bc9994b5fb95fff610ce0de3afac6b63bdcace02/src/core/client/mod.rs#L328) in routing, and likewise the put action from the client [passes to put](https://github.com/maidsafe/safe_core/blob/bc9994b5fb95fff610ce0de3afac6b63bdcace02/src/core/client/mod.rs#L344) in routing. The client also [passes on the method for post](https://github.com/maidsafe/safe_core/blob/bc9994b5fb95fff610ce0de3afac6b63bdcace02/src/core/client/mod.rs#L360) and [the method for delete](https://github.com/maidsafe/safe_core/blob/bc9994b5fb95fff610ce0de3afac6b63bdcace02/src/core/client/mod.rs#L376), even though we (obviously) don't see these as part of SelfEncryptor.

Once the data passes from the client to routing, it must be verified by the network and stored.

The client finds an 'authority' from the network which acts as the entry point for the storage process. This authority is where the client must send the data to store it on the safe network. The authority is [known as a ClientManager](https://github.com/maidsafe/safe_core/blob/bc9994b5fb95fff610ce0de3afac6b63bdcace02/src/core/client/mod.rs#L337). The way [the ClientManager is chosen](https://github.com/maidsafe/safe_core/blob/bc9994b5fb95fff610ce0de3afac6b63bdcace02/src/core/client/mod.rs#L197) depends on the session for the client.

The data finally reaches the network via the method [send_put_request](https://github.com/maidsafe/routing/blob/72bc19b318f2f60bb07a1a20db8bf50932a29a37/src/client.rs#L124) of the routing object. This calls the generic [send_action](https://github.com/maidsafe/routing/blob/72bc19b318f2f60bb07a1a20db8bf50932a29a37/src/client.rs#L129) with the message to put data on the network.

The send_action functionality is executed by an [action_sender](https://github.com/maidsafe/routing/blob/72bc19b318f2f60bb07a1a20db8bf50932a29a37/src/client.rs#L157) object, in this case a [routing core object](https://github.com/maidsafe/routing/blob/72bc19b318f2f60bb07a1a20db8bf50932a29a37/src/client.rs#L73). When a routing core object [receives a put message](https://github.com/maidsafe/routing/blob/72bc19b318f2f60bb07a1a20db8bf50932a29a37/src/core.rs#L1121), it simply [relays that event](https://github.com/maidsafe/routing/blob/72bc19b318f2f60bb07a1a20db8bf50932a29a37/src/core.rs#L1126) to the next destination using the [send](https://github.com/maidsafe/routing/blob/72bc19b318f2f60bb07a1a20db8bf50932a29a37/src/core.rs#L2393) method. The destination for the next hop on the network is determined by calling [to_destination](https://github.com/maidsafe/routing/blob/72bc19b318f2f60bb07a1a20db8bf50932a29a37/src/core.rs#L2421) for the message. Since this is a ClientManager node, the result of calling to_destination is the [group](https://github.com/maidsafe/routing/blob/5747c084533a40e8dbbb9264237a02a10d132e4a/src/authority.rs#L83) for this node.

The last link in the chain of network activity is implemented by [the handling of a put request](https://github.com/maidsafe/safe_vault/blob/9c68532360e7ac26644499d165c4ffb9ed4ea9f5/src/vault.rs#L171) by the vault. In this case, the data source is the ClientManager, the data destination is the NaeManager (the Network Addressable Element Manager), and the event is a Put.

When a vault receives data to be stored, it [calls put on the chunk_store](https://github.com/maidsafe/safe_vault/blob/5cf9f1837f2b435f61044676eafa324353ff9968/src/personas/data_manager.rs#L357). The chunk_store is implemented in the [chunk_store repository](https://github.com/maidsafe/chunk_store) and handles storage of data in the temporary directory of the system running the vault.

There is still an uncovered detail regarding redundancy of data on the network. Nowhere in this chain has the data been duplicated for storing multiple copies. There's methods implemented for [creating redundant copies of immutable data](https://github.com/maidsafe/routing/blob/5747c084533a40e8dbbb9264237a02a10d132e4a/src/immutable_data.rs#L173), which indicates there are three copies of the data created (normal, backup and sacrificial). However the place in the code where these copies are generated is not clear. If you can help clarify this, please make a comment on [the discussion](https://forum.safenetwork.io/t/explanation-sourced-direct-from-the-code/8992) on the safe network forum.

Another detail not covered is how consensus is implemented. Without getting into details here, it's [implemented in the routing/core object in handle_routing_message](https://github.com/maidsafe/routing/blob/322788b69127decfe3d0bce2bf2994ba4e079da7/src/core.rs#L923). The consensus logic is contained in an accumulator object. Consensus is applied to [almost all routing messages](https://github.com/maidsafe/routing/blob/322788b69127decfe3d0bce2bf2994ba4e079da7/src/core.rs#L940), including the Put message which comes from the client to store data on the safe network. This consensus mechanism is the key to why data storage on the safe network is so robust.

There are some interesting failure conditions such as [when the vault is full](https://github.com/maidsafe/safe_vault/blob/5cf9f1837f2b435f61044676eafa324353ff9968/src/personas/data_manager.rs#L345), however they are not covered in detail here.

Now the full picture of data flow has been covered. Client &rarr; ClientManager &rarr; NaeManager. Self encryption happens at the client before reaching the network. Consensus of the data happens from the ClientManager onward. Storage on a harddrive happens on the NaeManager.

[Back to Table of contents](#data-transition-to-network_toc)

### How is data fetched from the network? {#data-fetched-from-network}

[Back to Table of contents](#data-fetched-from-network_toc)

### What is the difference between private and public data? {#difference-between-private-and-public-data}

[Back to Table of contents](#difference-between-private-and-public-data_toc)

### How is data deleted from the network?

[Back to Table of contents](#how-is-data-deleted-from-the-network_toc)

### How many redundant copies are stored?

[Back to Table of contents](#how-many-redundant-copies-are-stored_toc)

### How does proof of resource algorithm work? {#proof-of-resource-algorithm}

Proof of resource is the algorithm that determines whether a vault should be rewarded with safecoin or not.

Proof of resource has not yet been implemented in code. Read more about the progress of this feature:

* [Farm Attempt RFC](https://github.com/maidsafe/rfcs/blob/e5568a4464353255ea6f5baebd55d275a7c8b210/text/0004-farm-attempt/0004-farm-attempt.md) and [discussion](https://github.com/maidsafe/rfcs/issues/25)
* [Balance Network Resources RFC](https://github.com/maidsafe/rfcs/blob/e5568a4464353255ea6f5baebd55d275a7c8b210/text/0005-balance-network-resources/0005-balance-network-resources.md) and [discussion](https://github.com/maidsafe/rfcs/issues/26)
* [Safecoin Implementation RFC](https://github.com/maidsafe/rfcs/blob/e5568a4464353255ea6f5baebd55d275a7c8b210/text/0012-safecoin-implementation/0012-safecoin-implementation.md) and [discussion](https://github.com/maidsafe/rfcs/issues/61)
* [Safe Vault Documentation](https://github.com/maidsafe/safe_vault/tree/87c64d6c74a403629b48d34bee9508706ca083b9/docs)
* [Wiki page for Proof of Resource](https://safenetwork.wiki/en/Proof_of_resource)
* [Safecoin Roadmap](http://maidsafe.net/roadmap#safecoin)

[Back to Table of contents](#proof-of-resource-algorithm_toc)

### How does the vault ranking algorithm work? {#vault-ranking-algorithm}

The vault ranking algorithm is used to manage network authority and thus has implications for the security of data on the network.

Vault ranking has not yet been implemented in code. Read more about the progress of this feature:

* [Farm Attempt RFC](https://github.com/maidsafe/rfcs/blob/e5568a4464353255ea6f5baebd55d275a7c8b210/text/0004-farm-attempt/0004-farm-attempt.md) and [discussion](https://github.com/maidsafe/rfcs/issues/25)
* [Balance Network Resources RFC](https://github.com/maidsafe/rfcs/blob/e5568a4464353255ea6f5baebd55d275a7c8b210/text/0005-balance-network-resources/0005-balance-network-resources.md) and [discussion](https://github.com/maidsafe/rfcs/issues/26)
* [Safecoin Implementation RFC](https://github.com/maidsafe/rfcs/blob/e5568a4464353255ea6f5baebd55d275a7c8b210/text/0012-safecoin-implementation/0012-safecoin-implementation.md) and [discussion](https://github.com/maidsafe/rfcs/issues/61)
* [Safe Vault Documentation](https://github.com/maidsafe/safe_vault/tree/87c64d6c74a403629b48d34bee9508706ca083b9/docs)
* [Wiki page for Measurement And Reward](https://safenetwork.wiki/en/Measurement_and_reward)
* [Safecoin Roadmap](http://maidsafe.net/roadmap#safecoin)

[Back to Table of contents](#vault-ranking-algorithm_toc)

### How is farming speed for each vault set by the network? {#farming-speed-algorithm}

Farming speed is the algorithm which determines how much reward a vault earns through proof of work, and incentivizes a stable network structure.

Farming speed has not yet been implemented in code. Read more about the progress of this feature:

* [Farm Attempt RFC](https://github.com/maidsafe/rfcs/blob/e5568a4464353255ea6f5baebd55d275a7c8b210/text/0004-farm-attempt/0004-farm-attempt.md) and [discussion](https://github.com/maidsafe/rfcs/issues/25)
* [Balance Network Resources RFC](https://github.com/maidsafe/rfcs/blob/e5568a4464353255ea6f5baebd55d275a7c8b210/text/0005-balance-network-resources/0005-balance-network-resources.md) and [discussion](https://github.com/maidsafe/rfcs/issues/26)
* [Safecoin Implementation RFC](https://github.com/maidsafe/rfcs/blob/e5568a4464353255ea6f5baebd55d275a7c8b210/text/0012-safecoin-implementation/0012-safecoin-implementation.md) and [discussion](https://github.com/maidsafe/rfcs/issues/61)
* [Safe Vault Documentation](https://github.com/maidsafe/safe_vault/tree/87c64d6c74a403629b48d34bee9508706ca083b9/docs)
* [Wiki page for Farming Rates](https://safenetwork.wiki/en/Safecoins_(How_it_works)#Farming_rates)
* [Safecoin Roadmap](http://maidsafe.net/roadmap#safecoin)

[Back to Table of contents](#farming-speed-algorithm_toc)

### How is proof of resource converted into safecoin? {#proof-of-resource-conversion-to-safecoin}

Proof of resource is the first step toward earning safecoin from the network. The network must automatically decide when and how to allocate safecoin to vaults in exchange for proof of resource.

Proof of resource and safecoin has not yet been implemented in code. Read more about the progress of this feature:

* [Farm Attempt RFC](https://github.com/maidsafe/rfcs/blob/e5568a4464353255ea6f5baebd55d275a7c8b210/text/0004-farm-attempt/0004-farm-attempt.md) and [discussion](https://github.com/maidsafe/rfcs/issues/25)
* [Balance Network Resources RFC](https://github.com/maidsafe/rfcs/blob/e5568a4464353255ea6f5baebd55d275a7c8b210/text/0005-balance-network-resources/0005-balance-network-resources.md) and [discussion](https://github.com/maidsafe/rfcs/issues/26)
* [Safecoin Implementation RFC](https://github.com/maidsafe/rfcs/blob/e5568a4464353255ea6f5baebd55d275a7c8b210/text/0012-safecoin-implementation/0012-safecoin-implementation.md) and [discussion](https://github.com/maidsafe/rfcs/issues/61)
* [Safe Vault Documentation](https://github.com/maidsafe/safe_vault/tree/87c64d6c74a403629b48d34bee9508706ca083b9/docs)
* [Wiki page for How Are Safecoins Distributed](https://safenetwork.wiki/en/FAQ#How_are_safecoins_distributed.3F)
* [Safecoin Roadmap](http://maidsafe.net/roadmap#safecoin)

[Back to Table of contents](#proof-of-resource-conversion-to-safecoin_toc)

### How does spending safecoin (ie changing structured data) work? {#spending-safecoin}

[Back to Table of contents](#spending-safecoin_toc)

### How is safecoin managed and how do wallets work? {#safecoin-management}

[Back to Table of contents](#safecoin-management_toc)

### How is general structured data created?

[Back to Table of contents](#how-is-general-structured-data-created_toc)

### How is structured data updated?

[Back to Table of contents](#how-is-structured-data-updated_toc)

