---
layout: post
title:  "Safe Network explained using bitcoin terminology"
date:   2016-05-05 00:00:00 +0000
updated: 2016-05-10 00:00:00 +0000
categories: bitcoin
---
If you already know how bitcoin works, it makes sense to build on that knowledge when trying to understand SAFE rather than start from scratch.

Despite the safe and bitcoin networks having very similar goals and outcomes, the differences in the way they work are quite substantial. If any of the explanations below seem labored, it's because bitcoin concepts only extend so far into describing the safe network. The similarities will take us a fair way, but eventually there are limitations.

The following content is not meant for obtaining an overall understanding of the safe network, but should provide a very solid basis for further reading.

The terms used as the headings for each section are like individual windows in a house, allowing various bitcoin-centric perspectives into the safe network.

## Table of contents

<ul>
<li><a id="blockchain_toc" href="#blockchain" rel="nofollow">Blockchain</a></li>
<li><a id="proof-of-work-for-verification_toc" href="#proof-of-work-for-verification" rel="nofollow">Proof of work for verification</a></li>
<li><a id="double-spend-prevention_toc" href="#double-spend-prevention" rel="nofollow">Double spend prevention</a></li>
<li><a id="transaction-propagation_toc" href="#transaction-propagation" rel="nofollow">Transaction propagation</a></li>
<li><a id="zero-confirmations_toc" href="#zero-confirmations" rel="nofollow">Zero confirmations</a></li>
<li><a id="mining-block-reward_toc" href="#mining-block-reward-coinbase" rel="nofollow">Mining, block reward, coinbase</a></li>
<li><a id="deflation-scarcity-and-monetary-policy_toc" href="#deflation-scarcity-and-monetary-policy" rel="nofollow">Deflation, scarcity, and monetary policy</a></li>
<li><a id="transaction-fees-and-long-term-sustainability_toc" href="#transaction-fees-and-long-term-sustainability" rel="nofollow">Transaction fees and long term sustainability</a></li>
<li><a id="difficulty--target_toc" href="#difficulty--target" rel="nofollow">Difficulty / target</a></li>
<li><a id="pooled-mining_toc" href="#pooled-mining" rel="nofollow">Pooled mining</a></li>
<li><a id="mining-centralization_toc" href="#mining-centralization" rel="nofollow">Mining centralization</a></li>
<li><a id="public-blockchain-and-pseudo-anonymity_toc" href="#public-blockchain-and-pseudo-anonymity" rel="nofollow">Public blockchain and pseudo-anonymity</a></li>
<li><a id="transaction-inputs-and-outputs_toc" href="#transaction-inputs-and-outputs" rel="nofollow">Transaction inputs and outputs</a></li>
<li><a id="bitcoin-core_toc" href="#bitcoin-core" rel="nofollow">Bitcoin core</a></li>
<li><a id="altcoin_toc" href="#altcoin" rel="nofollow">Altcoin</a></li>
<li><a id="digital-cash_toc" href="#digital-cash" rel="nofollow">Digital cash</a></li>
<li><a id="premine_toc" href="#premine" rel="nofollow">Premine</a></li>
<li><a id="forking_toc" href="#forking" rel="nofollow">Forking</a></li>
<li><a id="blocksize_toc" href="#blocksize" rel="nofollow">Blocksize</a></li>
<li><a id="scaling_toc" href="#scaling" rel="nofollow">Scaling</a></li>
<li><a id="incentives---goodness-is-more-rational-than-badness_toc" href="#incentives---goodness-is-more-rational-than-badness" rel="nofollow">Incentives - goodness is more rational than badness</a></li>
<li><a id="and-sybil-attacks_toc" href="#and-sybil-attacks" rel="nofollow">51% and sybil attacks</a></li>
<li><a id="proof-of-burn_toc" href="#proof-of-burn" rel="nofollow">Proof of burn</a></li>
<li><a id="divisibility_toc" href="#divisibility" rel="nofollow">Divisibility</a></li>
<li><a id="addresses_toc" href="#addresses" rel="nofollow">Addresses</a></li>
<li><a id="irreversible_toc" href="#irreversible" rel="nofollow">Irreversible</a></li>
<li><a id="genesis-block_toc" href="#genesis-block" rel="nofollow">Genesis block</a></li>
<li><a id="antifragile-honeybadger_toc" href="#antifragile-honeybadger" rel="nofollow">Antifragile honeybadger</a></li>
<li><a id="testnet_toc" href="#testnet" rel="nofollow">Testnet</a></li>
<li><a id="satoshi-nakamoto_toc" href="#satoshi-nakamoto" rel="nofollow">Satoshi Nakamoto</a></li>
<li><a id="byzantine-generals-problem_toc" href="#byzantine-generals-problem" rel="nofollow">Byzantine generals problem</a></li>
<li><a id="wallets_toc" href="#wallets" rel="nofollow">Wallets</a></li>
<li><a id="multisig_toc" href="#multisig" rel="nofollow">Multisig</a></li>
<li><a id="smart-contracts_toc" href="#smart-contracts" rel="nofollow">Smart contracts</a></li>
<li><a id="signed-messages_toc" href="#signed-messages" rel="nofollow">Signed messages</a></li>
<li><a id="trustlessness_toc" href="#trustlessness" rel="nofollow">Trustlessness</a></li>
<li><a id="cold-storage_toc" href="#cold-storage" rel="nofollow">Cold storage</a></li>
<li><a id="the-bitcoin-community_toc" href="#the-bitcoin-community" rel="nofollow">The bitcoin community</a></li>
<li><a id="bips_toc" href="#bips" rel="nofollow">BIPs</a></li>
<li><a id="revolutionary-concepts_toc" href="#revolutionary-concepts" rel="nofollow">Revolutionary concepts</a></li>
<li><a id="whitepape_toc" href="#whitepaper" rel="nofollow">Whitepaper</a></li>
<li><a id="first-mover-advantage-and-network-effect_toc" href="#first-mover-advantage-and-network-effect" rel="nofollow">First mover advantage and network effect</a></li>
<li><a id="hashing-the-blockchain_toc" href="#hashing-the-blockchain" rel="nofollow">Hashing the blockchain</a></li>
</ul>

### Blockchain

The blockchain is the most important innovation of bitcoin. The purpose of the blockchain is to achieve consensus for the state of data in the bitcoin network.

The safe network addresses the same problem as the blockchain, namely that of consensus, but does not use a blockchain. The major innovation introduced by the safe network to solve the consensus problem is called 'close group consensus'.

With bitcoin and blockchains, every node of the network has a full copy of all data on the network. As new data is added to the network, every node keeps a copy of that data.

Safe keeps only a few copies of each piece of data on the network. Each piece of data is allocated a group of nodes (about 30) by the network to keep watch over the integrity of that piece of data. Changes to that data can only be made if there's consensus by a supermajority of the group (about 80%) as to the true state of the data. Because only that relatively small group of nodes must agree on the true state of the data, safe is much more efficient than bitcoin.

There is some doubt about how large groups should be, is this secure, why not more, why not less? Rather than jump to deriving this number by proof, take a moment to consider what is being achieved by the consensus rules of both bitcoin and safe: data should not be vulnerable to being lost, corrupted, or incorrectly modified. If that group of nodes can be manipulated (hacked or bribed etc), the data they control would not be considered secure. This concern is addressed by the first part of the phrase, 'close' group consensus.

Closeness is the algorithm which determines how the group governing each piece of data is formed. Members of this group are chosen by the network in a way that is very difficult to manipulate (the difficulty of doing this, like bitcoin, changes depending on the size of the network, becoming harder as the network grows). Rather than go into the details of the close group consesus algorithm here, I would instead recommend reading about xor distance as used by the safe network and follow the information trail from there.

To directly compare the language for the security of the bitcoin vs safe network, bitcoin is secured by 'proof of work', safe is secured by 'close group consensus'.

For more reading about consensus using close groups rather than a blockchain, it's worth reading [Consensus Systems](https://blog.maidsafe.net/tag/consensus-systems/) on the maidsafe blog. It's also worth reading about [Disjoint Groups](https://github.com/maidsafe/rfcs/blob/master/text/0037-disjoint-groups/0037-disjoint-groups.md).

In the same way the blockchain was the key innovation that allowed the realization of bitcoin, close group consensus is the key innovation that allows safe to provide the same functionality but with vastly improved efficiency.

[Back to Table Of Contents](#blockchain_toc)

### Proof of work for verification

One breakthrough of the blockchain is that it's easy to verify the data you have is the true blockchain and not a malicious person sending you invalid data. This is possible because of proof of work, which is easy to verify but difficult to create.

How does safe ensure the data you receive is from the 'true' safe network and not from a malicious actor impersonating the network?

There are two main mechanisms that work together to replace proof of work and protect data integrity.

The first mechanism is called self-encryption. Any data stored on the network is automatically encrypted by the client before being sent for storage. This means anyone running a vault is unable to determine what specific data they are storing, since only the owner of the data can decrypt it. This makes it extremely difficult to create a second network containing malicious data and fool users into participating on the fake network.

The second mechanism is 'chained close group consensus'. When data is added to the network or requested from the network, it must pass through a series of nodes and checks before reaching the final location where the data is stored. This means the node which delivered the data for storage is merely part of a chain of nodes in the network, and is not the original owner of that data. This chaining mechanism, similar to TOR, makes the data storage completely anonymous. All data is fungible, no data is more or less important than others from the perspective of the vaults or other nodes in the chain. This makes it extremely difficult to isolate a meaningful subset of the data on the network to use in creating a fake alternative network.

By encrypting data at the client and then chaining the consensus of integrity for that data through the request path, it becomes extremely difficult to create an alternative network which still has meaning to the client. This is what makes it trivial to detect a fake version of the network, much like proof of work makes detecting a fake blockchain very simple.

These properties of both the data and the network make it extremely difficult to corrupt part of the network, the same way proof of work makes it extremely difficult to modify part of the blockchain.

Self-encryption and chained close group consensus is how the safe network retains integrity without using proof of work.

Read more about [self-encryption on the wiki](https://safenetwork.wiki/en/Security_-_Self_encryption).

Read more about chained close group consensus in [the vault repository](https://github.com/maidsafe/safe_vault/blob/87c64d6c74a403629b48d34bee9508706ca083b9/README.md#flows) and on the wiki [How Vaults Work](https://safenetwork.wiki/en/Vaults_(How_it_works)#Group_Consensus)

[Back to Table Of Contents](#proof-of-work-for-verification_toc)

### Double spend prevention

Bitcoin is often said to have solved the 'double spend problem', which is akin to saying bitcoin retains data atomicity, consistency, isolation and durability ([ACID](https://en.wikipedia.org/wiki/ACID)) even though it's a distributed, decentralized and trustless network. This is a huge deal both in computer science and in real world applications. The result of this is money that's spent _stays_ spent. Or in data-terms, when data is changed, it stays changed.

Safe has a token similar to that of bitcoin, called safecoin. Where bitcoin _transaction_ data is _appended_ to the blockchain, safecoin _ownership_ data is _updated_ on the safe network. How does safe ensure that coins spent on the safe network are not susceptible to double spending, and how does this compare to bitcoin's implementation?

Let's establish a common understanding of bitcoin and double-spends. When bitcoin is received, the confidence it will not be double-spent depends on how many confirmations the transaction has. Double spend in bitcoin _is possible_, it's just very difficult. Replace-by-fee made the window for double spending much larger and simpler to execute, and double-spending in bitcoin should be thought of as very high or low _probabilities of confidence_, not by absolute certainty.

This is one of the biggest advantages safe has over bitcoin - transactions are extremely fast and once made, confidence is complete. How does this work? To understand, we must first look at how bitcoin vs safecoin is represented by the data of the network.

On the bitcoin blockchain, bitcoins are grouped into outputs, and do not exist as discrete entities. For example, the reward for mining a block is currently 25 BTC, but this 25 BTC is represented in a single transaction. 25 coins, 1 piece of data.

Quite differently, every safecoin is a discrete piece of data with its own location on the network (just like regular data on the safe network). 25 safecoins would be 25 different bits of data on the safe network.

This fundamentally different way of representing tokens is how safecoins can be transferred so quickly and with such high confidence. The transfer mechanism of safecoin is quite different to that of bitcoin.

A safecoin is a special package of data (called structured data) which contains details of the current owner and the previous owner. To transfer ownership of that coin, the current owner must verify their ownership (similar to a signature in a bitcoin transaction). If they own the coin, they are allowed to change the data for the coin's current owner to be the new owner. The 'current owner' of a safecoin is like the utxo of a bitcoin transaction. The 'previous owner' is like the input that generated the utxo. Safecoins ownership can be traced back to only the current and previous owners, whereas bitcoins ownership can be traced back to the initial generation, which may include hundreds or thousands of owners.

Transferring safecoin is a single atomic data event on the safe network. Once completed, it stays that way until the new owner decides to change it. There is no way it can be reversed, unlike bitcoin which can have changes to the mempool or even orphaned blocks.

Compare the cost of completing a transaction. In bitcoin, the cost of a transaction is the size of the transaction relative to the entire block, multiplied by the cost of the proof of work to generate the block. In safecoin, the cost is the resources required to obtain consensus for the piece of data (a close group of nodes must reach consensus then store the change) multiplied by the number of coins being spent. Safecoin transactions are extremely efficient compared to bitcoin transactions because close group consensus is much cheaper and faster than generating a proof of work.

[Back to Table Of Contents](#double-spend-prevention_toc)

### Transaction propagation

One of the interesting considerations with bitcoin is when two different transactions spending the same outputs are announced simultaneously. When this happens, a 'race' is started to see which transaction can propagate the fastest to all the other nodes of the network. A node that has received one transaction will eventually also receive the second one, in which case it simply ignores the second one. This is the 'first seen' principle of relay policy and transaction propagation.

However, the actual transaction included in the blockchain depends on what the miners choose to include in their blocks, and may even be a transaction never propagated to the network before (so long as it's still valid).

This highlights an interesting property of bitcoin - the 'true' state of the network can only be determined from the blockchain, which may be quite old, in some cases over an hour. This poses a challenging problem for merchants wanting to accept immediate payment, such as from in-store customers.

How does the safe network compare in terms of truth and handling 'race' situations? If a client announces a change to part of the network and a different change simultaneously to another part of the network, how does this resolve?

When data is to be changed on the safe network, 28 of 32 close group nodes must agree on the change. If the client simultaneously announces different data so 22 nodes in the group receive one version but 10 receive a different version, consensus will not be reached and the change will not be made.

It's actually a bit more subtle than that, depending on whether the data is mutable or immutable, a put or a post; but those details begins to blur the concept so it's best to research that yourself.

There are some relevant articles on the blog -
[multiple store/delete](https://blog.maidsafe.net/2014/02/23/multiple-storedelete/)
[pmidnode leave/join](https://blog.maidsafe.net/2014/03/07/pmidnode-leavejoin/)


[Back to Table Of Contents](#transaction-propagation_toc)

### Zero confirmations

As indicated in the section on Transaction propagation, zero confirmation transactions in bitcoin cannot be fully trusted, and with replace-by-fee, cannot be trusted at all.

In safe, the resolution of a transaction should be extremely fast, typically less than a second (the network isn't live so this has not yet been confirmed publicly). This difference is due to the way transactions are represented and stored on the safe network.

A bitcoin transaction is represented as an _addition_ of data to the blockchain. Since adding data requires a new block, this is both time consuming and expensive. During the time between a transaction announcement on the network and its acceptance into the blockchain, the receiver cannot be sure if the transaction will be confirmed by inclusion in a block, hence the term 'zero confirmation' transaction and the associated wariness.

A safecoin transaction is represented as a _modification_ to an existing piece of data, namely by changing the details for the current owner of the coin. The change to the data is performed by the network in an atomic operation, subject to approval by close group consensus. Once the close group consensus is agreed, the change is made and is final.

The safe network does not have a concept of zero confirmations (or confirmations at all) and transactions are final once committed to the network, which happens as soon as 32 close group nodes have reached consensus on the change (typically less than a second).

To better understand how transactions are made on the safe network, a good place to start is the entire [unified structured data](https://github.com/maidsafe/rfcs/blob/1cd4ed22709fed673f5ce51c1d861d879abd7aec/text/0000-Unified-structured-data/0000-Unified-structured-data.md) rfc.

[Back to Table Of Contents](#zero-confirmations_toc)

### Mining, block reward, coinbase

Bitcoin incentivizes participation in proof of work by rewarding it with new coins.

How are new safecoins generated and how are the incentives for network participation structured?

New safecoins are generated by 'proof of resource'.

Firstly, consider bitcoin. If a user can provide a valid proof of work, the network will allocate the user with new coins. The number of coins is determined by the rules of the network; if the rules are not followed the proof of work is invalid. Safecoin is issued when a user provides a valid proof of resource. Clearly we need to understand what proof of resource means and how it compares to proof of work.

Proof of work proves the user expended some certain amount of computation (it's a statistical thing but still creates valid proofs). Proof of resource proves the user is able to provide a certain amount of resource (storage, bandwidth, computation).

Both proofs are very easy to verify but difficult to create. How does proof of resource achieve this?

The idea is to prove a node can supply resource without actually having to supply it. This is like proof of work where a node can prove it did work without having every other node actually do the work.

The proof of resource algorithm is surprisingly simple and clever.

* A checking group of nodes creates a random string
* This random string is sent encrypted to all nodes that hold the data
* Each data holder node takes this string, appends it to the original data, and hashes the result
* The result from each data holder node is collected and decrypted by the checking group and results of all holders are compared
* If any holder node returns a different result, it is considered to be compromised and is deranked

This should begin to illustrate how important the concept of close group consensus is in the safe network. It forms the basis for basically every single operation on the network.

The clever bit is the nodes doing the checking don't need to have the data in order to check it. They rely on the data having been initially stored in a specific way (ie consensus across a close group) which allows it to check on it again in the future without needing to have a copy of the data itself.

Read more about [proof of resource](https://safenetwork.wiki/en/Proof_of_resource) on the wiki.

The information in the [safecoin](https://github.com/maidsafe/rfcs/blob/cd47937ebf053e90bde20f18eced2866854e8234/text/0012-safecoin-implementation/0012-safecoin-implementation.md) rfc is also valuable.

[Back to Table Of Contents](#mining-block-reward_toc)

### Deflation, scarcity, and monetary policy

Bitcoin is desirable because it's scarce. This scarcity is an essential aspect of the incentive structure of the bitcoin network which ultimately helps keep it secure. The monetary policy ensures that only 21 million bitcoins will ever exist, and this cannot be changed without complete consensus from the entire network.

What is the monetary policy of safecoin and how is it enforced?

Safecoin will never have more than 4.3 billion coins (2^32 coins).

In the section on double spend prevention, it's mentioned that safecoins are a particular type of data structure. Part of that data structure is the 'name' of the coin, which is limited to 32 bits.

This name is allocated by the network and cannot be manipulated by the user.

Since the network is coded to only recognize 2^32 possible safecoin names, there can never be more than that many coins.

[Back to Table Of Contents](#deflation-scarcity-and-monetary-policy_toc)

### Transaction fees and long term sustainability

Bitcoin rewards miners with new coins for keeping the network secure. As time goes on, the reward decreases (an essential part of the economics and incentives of bitcoin). This raises questions about what happens to bitcoin mining and security once the mining reward stops? Is bitcoin viable long term? The answer lies in transaction fees, which provide additional subsidy to miners and continues their incentive to mine.

How does this compare with the long term sustainability and security of the safe network?

One of the interesting features of safe is called 'recycling' coins. Performing certain actions on the network requires the expenditure of coin to the network itself, like how a bitcoin transaction requires leaving some 'unspent' coins which go to the miners in the form of a fee.

Recycling safecoin means as long as people are actively using the network there will always be some supply of coins for vaults to claim via proof of resource. Recycling is actually a convenience term for a cumbersome technical phrase: creating a delete request on a safecoin structured data. If you want to know more about how recycling works, these are good terms to research.

Recycling is sometimes said to affect the economics of the safe network, because it allows the same coin to be 'minted' multiple times. Some people say this makes safecoin inflationary, but since there can never be more than 4.3B coins this isn't really true. It's like saying bitcoin is inflationary because the miners continue to receive coin from fees - in both bitcoin and the safe network the coins all come from the same limited resource pool, which makes both coins deflationary.

[Back to Table Of Contents](#transaction-fees-and-long-term-sustainability_toc)

### Difficulty / target

An amazing property of the bitcoin network is how it automatically adjusts the difficulty of mining to ensure blocks are consistently ten minutes apart, even if the network grows or shrinks considerably. This ensures the rate of newly generated coins is kept fairly consistent with the planned schedule.

What happens as the safe network grows and more resources join? How does this affect the generation of new coins and the incentives for new users joining the network?

When a user completes a proof of resource they're entitled to create a new coin. The network generates a 32 bit 'name' for the newly generated coin. The network then checks if the coin for that name already has an owner. If it does, the mining attempt (or to use the safe term, farm attempt) is unsuccessful. If there's no user for the coin, it's created and assigned to the user who earned it.

There's a similarity between undertaking proof of resource on the safe network and undertaking a proof of work when mining bitcoin. Most proof of work yields a result above the target, and no coins will issued for that particular proof of work.

The same idea applies to proof of resource. A proof of resource is simply a chance to earn a coin, not a guarantee. The proof must be converted to a coin by a mechanism called the farm rate.

A node is given an ongoing score by the network for how reliable it is. This score is based on proof of resource, and is called a 'ranking'. This score determines how rapidly _the node_ can generate new safecoin. Additionally, there is a _network rate_ which determines how much coin overall can be generated by all nodes on the network. The network rate is similar to the difficulty of bitcoin; it's the mechanism by which the supply and demand of resources is kept balanced.

At the current time there's no implementation or particularly robust design docs for these mechanisms, which is one of the indicators of how much work remains to be completed on safe, and how challenging the prior work has been to consume so much time in development.

Read more about farm rate on
[the wiki](https://safenetwork.wiki/en/Safecoins_(How_it_works)#Farming_rates)
and [the safe vault docs](https://github.com/maidsafe/safe_vault/blob/master/docs/safecoin_farming_rate.md).

[Back to Table Of Contents](#difficulty--target_toc)

### Pooled mining

Bitcoin was easy to generate when the network started, because there weren't many participants. As the network grew, the thousands of miners competing for one block reward every ten minutes realized their chance to get reward was very low, and they may never be paid for their work. This led to the formation of pools to combine their work and split any rewards evenly according to the work done by each miner of the pool. Eventually this caused concern for centralization of the network, which would undermine the goals bitcoin was initially designed to achieve.

Are there similar situations that will arise in safe which may undermine the original goals?

There may be. Since there are only 4.3B coins available, it could become very difficult to claim a coin as an individual. This may lead to pooled farming, but it's hard to know for sure.

I think speculation about the pitfalls of the safe network is important, but am unable to provide much insight on this myself. If you have ideas about it, please add a comment on the discussion at forum by clicking the link at the very bottom of all this. It's certainly an interesting topic, but one that's hard to provide concrete knowledge about.

Vitalik Buterin from the ethereum project provided an interesting discussion about the [potential pitfalls](https://forum.safenetwork.io/t/vitalik-buterin-on-maidsafes-consensus-mechanism/3542) that close group consensus may face.

[Back to Table Of Contents](#pooled-mining_toc)

### Mining centralization

Bitcoin began with a 'one computer one vote' democratic ideal, however is now mired in controversy over miner centralization.

Is the safe network likely to face similar issues of farmer centralization and industry-grade growth problems, and what might be the impact of such a situation?

Firstly, this is a speculative section. Nobody can predict the future, although we can make fair guesses.

It's extremely likely that if the safe network is successful it will have enterprise-level involvement. This will probably make the small-time farmers using their home pc and internet connection uncompetitive. Whether being 'uncompetitive' will prevent small users participating in farming is yet to be seen; after all, there are still plenty of 'irrational' bitcoiners running full nodes with little or no economic incentive.

If the safe network becomes primarily centralized to a small number of giant data centers, it would have some pros and some cons.

Imagine a large-scale blackout across a nation. This could have extreme impact on the safe network, and possibly induce churn events that cannot be resolved. This is true of the network even without centralized farming, but would be especially prominent as farming becomes more centralized. This situation is a lot like the type of runaway bitcoin might face if verification of blocks takes longer than ten minutes, leading to a situation where it's impossible to catch up to the head of the blockchain.

Imagine collusion between data centers. They may be able to destroy individual bits of data, but they wouldn't know what they were destroying. They wouldn't be able to modify it in a meaningful way because they wouldn't be able to decrypt and re-encrypt the data without the keys, which only the owner has.

Imagine hacks to data centers. This could unleash a vast number of malicious nodes which could cause mass deranking and huge computational load managing the resulting churn.

The network has a technique (based on the sigmoid curve) to minimize centralization. This technique is unseen in blockchains. The algorithm results in diminishing returns to farmers that supply significantly above or below the average of other vaults, which creates strong incentives to maintain a decentralization of resources on the network (especially in the xor space). Minimizing centralization of the xor space is essential to maintaining the security of the network. This is discussed on the MaidSafe blog post about [keeping safecoin decentralised](https://blog.maidsafe.net/2015/02/06/keeping-safecoin-decentralised/).

There are different forms of centralization that may happen in decentralized networks, each with their own possible motivators, severity and impact.

* Economic centralization of safecoin rewards, similar to the primary cause of bitcoin mining centralization
* XOR space centralization, which poses a risk to data integrity
* Governance centralization, ie controlling resources that determine the network rules
* Organizational centralization, similar to bitcoin core vs competing implementations

The ability to form these different kinds of centralization depend on several factors

* the _intended_ behavior of participants due to incentives put in place by the rules of the network
* the _actual_ (possibly irrational) behavior of participants compared to the rational behavior determined by the incentive structure
* divergence between specialized and consumer 'resources', ie bandwidth, storage, computation etc and how this affects behavior
* organizational and governance decisions, which depend on feedback of many types

The safe network has balanced these considerations differently to blockchains (both proof-of-work and proof-of-stake), mainly due to the underlying structure of the network.

It's worth reading the discussion on the safe network forum titled '[How is Farming Centralization Disincentivized](https://forum.safenetwork.io/t/how-is-farming-centralization-disincentivized/6639)' to further understand decentralization on the safe network.

Maybe your imagination can shed some additional light. If you can think of an interesting mode of centralization, please share it in the discussion using the link at the bottom of all this.

[Back to Table Of Contents](#mining-centralization_toc)

### Public blockchain and pseudo-anonymity

Bitcoin shot to fame when it was reported as 'used for buying drugs online' - a use case only possible because of the relatively high anonymity provided by bitcoin. Since then bitcoins pseudo-anonymity has been contentious, featuring prominently in tracing hacked and stolen coins, and poses ongoing questions around privacy.

At the same time, the transparency offered by the blockchain is one of its greatest assets.

How private is the safe network?

This is best explained using the idea of a privacy continuum.

Bitcoin is by default a public system, with opt-in privacy. The user picks the degree of privacy most suitable for their needs.

Safe is by default a private system, with opt-in transparency.

By default, data on the safe network is encrypted, fragmented, and anonymized. This is because of the built in properties of self-encryption and routing.

The privacy model of the safe network is nicely explained in the maidsafe blog post
[opting into transparency](https://blog.maidsafe.net/2014/08/23/opting-into-transparency/).

However, this model of privacy raises some questions.

How do we know how many safecoins have currently been farmed?

How do we progressively make our data more transparent and what degrees of control do we have?

What is the permissions model for data on the safe network?

How is permission allocated and revoked?

How does a team of people work collaboratively on data?

These are questions that require further research and are not explained here. Much of this will be supplied as an overlay on the safe network, rather than natively in the data layer itself.

This seems like a robust approach to developing the safe network, keeping the base level as simple as possible with sensible defaults, allowing additional uses to be built on a solid foundation.

We see with bitcoin that having a strong foundation is essential to adding functionality via overlays - think of the lightning network, sidechains, multisig, HD wallets, segregated witness, the app ecosystem such as mixers and open bazaar; all made possible by initially creating bitcoin with a strong simple foundation.

[Back to Table Of Contents](#public-blockchain-and-pseudo-anonymity_toc)

### Transaction inputs and outputs

Bitcoins "don't exist", ie there's no discrete coin, just the sum of all transactions until you get to the latest one.

This goes against the intuition of money as we know it (discrete tokens like coins and notes, or abstractions of such). The language used in bitcoin has subtle yet important meanings, especially when it comes to addresses and transactions. An address doesn't "have" bitcoins, it simply controls the right to change certain unspent transaction outputs. Aaack I can already feel the pedant in me crawling at the inexactitude!

Another classic terminology confusion is the use of Bitcoin vs bitcoin, or BTC vs XBT; the linguistic details are seemingly endless.

What's important is that terminology is specific for a reason. To effectively understand bitcoin requires a solid understanding of the lingo as well as the technical details.

What similar strange quirks of nomenclature can be found in the safe network?

* maidsafe vs safe network vs safecoin vs maidsafecoin

    * Maidsafe: seemingly a catch-all phrase. The proper use is to refer to the organization overseeing the development of the network. But it's often used as a name for the network itself, the coin for the network; this has become a very confusing term which has been used in many different ways. The most correct use of this term is to refer to the for-profit organization that oversees the development of the network, including design, source code, marketing etc. The closely-related Maidsafe Foundation is the not-for-profit organization that [holds patents](http://cointelegraph.com/news/clearing-the-air-over-maidsafes-patent-request-an-interview-with-coo-nick-lambert) and fosters education and innovation. Maidsafe is an acronym for massive array of internet disks, secure access for everyone.

    * safe network: the network that manages and stores data. Analogous to Bitcoin with a capital B. The safe network is not currently live but is undergoing testing and continued development before launch.

    * safecoin: the token of exchange on the safe network. Analogous to bitcoin with a lowercase b. Not currently available since this feature has not yet been coded.

    * maidsafecoin: the token issued as a presale to raise funds for the company to continue their work. Issued on the mastercoin protocol, it currently trades on exchanges and will be redeemable for safecoin once the network goes live and safecoin is available.

* proof of resource vs farming

Proof of resource is the algorithm to determine nodes are honest. Farming is the process of obtaining new safecoins, and may also be used to refer to the algorithm that determines when new safecoins are issued.

* farm rate vs network rate

Farm rate is the rate at which a node may attempt to generate new coins. Nodes that are positive contributors to the network may attempt to mine more often than nodes that are negative contributors.

Network rate is the rate at which the network releases new coins.

* naming of personas

PmidManager vs MpidManager is a bit of esoteric naming, among many others. Go on, get yourself confused reading the [safe vault conventions](https://github.com/maidsafe/safe_vault/blob/bae659e579a56e145159805ce87770174f2d678b/docs/conventions.md)

There are surely others but these are some of the main ones. My main tip for novices is to make sure the use of 'maidsafe' and 'safe network' are clear and consistent.

Check out [the glossary](https://safenetwork.wiki/en/Glossary) on the wiki for more clarity.

[Back to Table Of Contents](#transaction-inputs-and-outputs_toc)

### Bitcoin core

Bitcoin has a unique governance model, initially dominated by individuals such as Satoshi Nakamoto and Gavin Andresen, gradually giving way to a group of developers known as 'bitcoin core developers'. Other groups have come and gone with varying degrees of controversy, such as 'Bitcoin XT', 'Blockstream', and 'Bitcoin Classic'.

How does this compare with the governance of safe network?

Safe network has a single implementation, unlike bitcoin which has several implementations. The implementation of safe network is developed by the MaidSafe organization, which is a not-for-profit organization based in Troon, Scotland.

The project is open source and accepts contributions from the community if they fit or improve the design.

No alternative or competing implementation exist, however there is a desire in the future to have this happen.

There are libraries to access the safe network written in various languages

[Go client library](https://github.com/cretz/go-safeclient/) and [discussion](https://forum.safenetwork.io/t/ann-safe-cli-go-library-and-integration-tests-alpha-release/7957)

[Ruby client library](https://github.com/loureirorg/ruby-safenet) and [discussion](https://forum.safenetwork.io/t/ruby-safenet-create-safe-apps-in-ruby/8182)

[Javascript client library](https://github.com/cretz/safeclient.js) and [discussion](https://forum.safenetwork.io/t/ann-safeclient-js-client-library/8206)

[Python example](https://forum.safenetwork.io/t/registering-with-safe-launcher-from-python/8175)

Safe 'core' is written in rust.

[Back to Table Of Contents](#bitcoin-core_toc)

### Altcoin

Bitcoin was the first cryptocurrency, but it spawned hundreds of alternatives, such as litecoin and dogecoin, and hundreds of competitors, such as ripple and ethereum.

Is safe an altcoin, a competitor, a complement? What is the competitive relation of the safe network to the bitcoin network?

Safe is not an altcoin in the sense that altcoins are usually based on a blockchain and have a very similar fundamental mode of operation to bitcoin, with innovative tweaks and features added.

Safe is perhaps a competitor, depending how the relation is framed. Bitcoin is seen primarily as 'money' with other benefits such as smart contracts being on-the-side to the main show of 'being money'. Safe is seen primarily as 'data storage', with other benefits such as 'money' being on-the-side. When we look at the goals of both projects in terms of what they achieve technically, they're quite similar. But looking at the means by which they achieve that goal, they are the most dissimilar of all the cryptocurrency projects.

The relationship and classification of safe relative to bitcoin depends on the perspective of each individual. Personally, in my heart I consider it a competitor first, with the possibility that it may replace bitcoin. But in my head I accept safe will more realistically be a 'complement' to bitcoin, operating side by side and filling a different-yet-similar purpose.

Another consideration here is that safecoin is simply a 'specific type of structured data on the safe network'. Other generic types of structured data can (and will) be created on the safe network, which could be competing with or complementing safecoin. It may be that safecoin is not the dominant currency on the safe network, it all depends on adoption from users. One thing in safecoins favor is it will be the only currency that can be used to put data on the network, giving it a strong advantage over other structured-data-as-currency.

[Back to Table Of Contents](#altcoin_toc)

### Digital cash

Digital cash didn't exist until bitcoin arrived on the scene. Hashcash set the groundwork, but bitcoin was the first usable digital cash. It has the required properties for spending cash on the internet that credit cards can never deliver - irreversible, anonymous, simple, peer to peer.

Is safecoin cash? How does it compare to bitcoin for transferring wealth on the internet?

Safecoin is arguably better digital cash than bitcoin.

Safecoin is more anonymous, since once you hand it to another peer on the network, the record of prior ownership disappears, unlike bitcoin where the transaction remains recorded forever. Zerocash has shown that bitcoin can be made anonymous, but as it stands today, the anonymity of safecoin is far stronger than bitcoin (at least as designed, since there is no implementation). The keen observers may say that safecoins store the current _and previous_ owner, so you aren't anonymous until two spends happen, but the receiver could spend the coins to them self, making them self both the current and previous owner; this would remove the original sender from the previous sender and ensure their anonymity.

Safecoin is irreversible, since the only way to transfer ownership is to provide the network with a valid signature as the current owner of the coin. If you change the owner of the coin to a key you don't control, you can't reverse that action. Best of all, it's instantly irreversible, whereas bitcoin is on irreversible once included in a block (usually several blocks deep in the chain), which may be hours after the transaction was initially made.

Safecoin is simple to use, equally simple as bitcoin since it uses the same underlying signing and crypto techniques for ownership. This means transfer requires knowing the destination to send to which can be scanned from a QR code, emailed, scanned via NFC, faxed, whatever. 'Transfer of information' is equal to 'transfer of wealth' in both safecoin and bitcoin, no matter the medium used for transfer. Safecoin is still under development so it remains to be seen how transfer works in anger, and whether there's similar protocols to the Bitcoin Payment Protocol specified in BIP70-73, but there's no reason why safecoin cannot be as simple and robust as bitcoin for making payments. Considering changes to bitcoin such as replace-by-fee, safecoin may even be considered slightly simpler than bitcoin, at least in terms of intuitive expectation for how it behaves.

Safecoin is peer to peer, there's no middle-man in charge of the transaction. Nobody can prevent safecoin being transferred from one person to another. Safecoin you own is yours and nobody can take it or prevent you from spending it.

To better understand the properties of safecoin as a form of digital cash, it's best to read about how [structured data](https://github.com/maidsafe/rfcs/blob/1cd4ed22709fed673f5ce51c1d861d879abd7aec/text/0000-Unified-structured-data/0000-Unified-structured-data.md) works, since safecoin is a particular type of structured data.

[Back to Table Of Contents](#digital-cash_toc)

### Premine

Bitcoin was not premined - if you knew about it early enough, you got to mine the early coins. Other coins have been criticized for premining their coin to provide seed funds for the developers of the network.

Is safecoin premined?

Safecoin is premined. 10% of safecoin has been issued.

You may wonder how this can possibly be, considering safecoin and the safe network do not yet exist!

The way it was arranged was by issuing an intermediate token on the mastercoin network called maidsafecoin. This coin is traded on exchanges and has a value that fluctuates, just like other coins.

When the safe network and safecoin is launched, maidsafecoin can be exchanged 1:1 for safecoin. The exact mechanism for this is not clear, but will probably be through a burn mechanism. The wiki has some [more information](https://safenetwork.wiki/en/FAQ#How_will_MaidSafeCoin_be_converted_to_safecoin_.3F) about how this may happen.

There was a lot of controversy over the initial sale of maidsafecoin. If the controversy of the presale is important to you it's best to research yourself, but I'm leaving it alone here. Perhaps the judgment of the people who organized it should be questioned, which may flow on to their technical judgment in creating the safe network; only you can be the judge.

[Back to Table Of Contents](#premine_toc)

### Forking

Bitcoin is the third major use of the word fork, after cutlery-based usage and usage in git. Forking is considered extremely undesirable and difficult to manage. Forks have happened on the bitcoin network, but they're rare and usually well-managed.

Can the safe network fork? How does the safe network remain a single homogeneous whole?

This terminology is one of the harder ones to compare across the bitcoin and safe networks.

Let's start by frame 'forking' as 'breaking the network into two or more discreetly different parts'.

In this case, we must consider, how would safe become two different networks?

It doesn't happen like bitcoin, which has a common root but a divergent head, since there is no 'root' or 'head' in the safe network.

A 'fork' in safe would look like a corrupt portion of the network. Can this corrupt portion be detected and fixed? Will it remain stable and static as a corrupt portion forever? Will the corruption grow to affect the rest of the network?

Rather than labor the comparison, I think it's better to focus on the techniques that safe uses to remain healthy and keep data secure.

Safe uses redundancy when storing data. Read about how it works in the [immutable data naming](https://github.com/maidsafe/rfcs/blob/1cd4ed22709fed673f5ce51c1d861d879abd7aec/text/0013-immutable-data-naming/0013-immutable-data-naming.md) rfc

Safe uses a quorum of 28 of 32 nodes to ensure data remains consistent and changes are valid. There's [an informative post](https://blog.maidsafe.net/2015/01/29/consensus-without-a-blockchain/) on the maidsafe blog about the consensus mechanism. The idea of close group consensus is so important to the operation of the safe network that it's worth reading about in detail.

[Back to Table Of Contents](#forking_toc)

### Blocksize

Bitcoin implemented a change early on to restrict spam on the network. This is the now-infamous blocksize limit of 1MB.

This introduces two important considerations. How does the safe network manage spam? How does the safe network implement changes when a bottleneck arises?

Spam is managed by charging safecoin to add data to the network. In the same way that 'no transaction is spam' on the bitcoin network, 'no data is spam' on the safe network. If it's valuable enough for the user to spend money to make it happen, it's not spam.

In bitcoin, floating transaction fees has been a controversial mechanism, with some claiming it makes bitcoin unusable because it leads to higher transaction fees... I don't need to go into the details of this complex and often vitriolic topic here.

There is every chance the same thing will happen with the safe network. Until we see how the balance between resource availability and resource consumption works, we can't know for certain - just like we didn't know for certain what would happen when the bitcoin network became congested and the blocksize issue became significant. It's not a purely technical or economic problem. People respond in different ways, even though the behavior of the network itself is predetermined.

From a technical perspective, the safe network will adjust itself in a similar way the bitcoin network does, adjusting incentives in the form of money supply rate and the cost to perform operations on the network.

The second issue is how are changes implemented? Bitcoin has faced challenges implementing changes because if the rules of the network are to be changed, there must be consensus. The safe network will face similar challenges of governence and consensus among developers if the network grows as large as the bitcoin network (large in terms of developers and community members, not in terms of node count or data throughput).

One thing going for the safe network at this point is the clear governance model by having the code developed by a not-for-profit organization. This is a double edged sword - on one hand it makes it clear where the decision comes from and the reasoning behind it, but on the other hand it promotes appeal-to-authority and may leave smaller voices with sound technical reasoning ignored. This is one of those topics that has no 'best' answer, and the main point is to at least be aware of how it is and how it isn't.

[Back to Table Of Contents](#blocksize_toc)

### Scaling

Related to blocksize, the issue of scaling is a hot topic in the world of bitcoin.

How does the safe network scale, and what are the likely bottlenecks of the network?

The most obvious one, which was a complaint of similar systems such as freenet, will be bandwidth and latency. If the network cannot achieve efficiency in delivering and storing data, it will not be popular. There are strict physical limits to the speed at which a global network can operate, light simply won't travel through fibre optic any faster. Safe will need to overcome that constraint, especially given the multi-hop non-geographic chained-request method in use (like TOR) which can drastically increase the latency of the network. There's [a good analysis and discussion of this bottleneck](https://forum.safenetwork.io/t/hardware-network-communications-speeds-lags-bottlenecks/6021) on the safe network forum.

One of the ways this is resolved is called opportunistic caching. The idea is that popular data is cached close to the source requesting it, so any future requests can be served much more rapidly. Caching is well known to be an extremely prickly subject, one that I'm not going to cover here; all I want is to bring awareness to this feature so if it's something you're interested in there's a stepping-off point to your search.

Another issue around scaling for both networks is participation. In bitcoin this is the problem of having enough 'full nodes'. In the safe network this will probably be problem of having enough 'vaults' (although slightly different since a vault is like both bitcoins full nodes and a miners). How do you ensure that people participate and how do you keep them involved once they begin?

There is no simple answer to this. A few points can be posited, but ultimately participation behavior can only be observed once the network is running. One advantage of safe is that to become a vault requires no overhead; simply download and start the software. Becoming a full node in bitcoin requires downloading the entire blockchain before the node can be a meaningful member of the network. True, there is some overhead to becoming a vault in that it's required to gain some level of authority from the network, but this is extremely lightweight and essentially zero cost, especially compared to downloading the blockchain.

Blocks on the blockchain are 'getting full', which is obviously a scaling problem, perhaps the result of poor capacity planning (I'm inclined to say bitcoin has good capacity planning but the plan is badly communicated!). Does the safe network suffer from 'getting full'? It may. If there aren't enough vaults, or there's too much demand to store data, it may become very expensive to add more data. If that's the case, the additional reward should create the incentive to add more capacity, and the supply and demand should balance. The big difference with bitcoin is that bitcoin blocks can fill up due to a _rule_ of the network, whereas safe vaults can fill up due to _participation_ in the network. Safe can expand capacity by adding participants rather than changing the rules - adding participants is much easier than changing the rules. Safe should be much more elegant at handling capacity issues; we'll have to wait and see once the network launches.

[Back to Table Of Contents](#scaling_toc)

### Incentives - goodness is more rational than badness

The true beauty and art of bitcoin is in successfully encoding incentives into a technological set of rules. Being a good participant of the bitcoin network always yields more reward than being a bad participant. Behaving 'selfishly' in bitcoin results in benefiting others at the same time. It's honestly amazing.

What are the incentives of the safe network and will it succeed in fending off uncontrollable growth of bad actors the way bitcoin has?

The mechanism of coin issuance is crucial to both networks.

Bitcoin issues coin in exchange for security of the blockchain.

Safe issues coin in exchange for the capacity to store and deliver data.

If there were a criticism of bitcoin's incentives, it's that running a full node is only incentivised by 'confidence' in the accuracy of the transaction data, not by an economic reward. Full nodes will become a dying breed, especially as the blockchain grows.

If there were a criticism of safe, it would have to be that the confidence of data integrity is not as high as bitcoin. It may be that malicious users are able to undermine that confidence enough that people lose interest in participating, because the goals of the network will not be delivered. The incentive to participate in safe depends on being able to deliver on permanency of stored data.

The incentives of both networks is extremely interesting. However, they're also fairly similar. Bitcoiners would do well to read more about safecoin, but will probably not find a huge amount of novelty from the incentives offered by safe compared to bitcoin itself.

[Back to Table Of Contents](#incentives---goodness-is-more-rational-than-badness_toc)

### 51% and sybil attacks

When bitcoin started it was based on the concept of 'one cpu one vote'. This has obviously changed with the advent of ASIC mining and the removal of small individual miners. The use of voting in bitcoin to determine the future direction is very interesting. Miners may include additional data with their blocks to indicate their preferences. This is also the mechanism used to implement changes via a soft fork.

However, the voting mechanism in bitcoin no longer represents the 'democratic' ideal imagined when bitcoin began.

One of the interesting developments was how 'votes' were cast to show support for Bitcoin XT and Bitcoin Classic. Full nodes would include the preference in their user agent, thus allowing a full node to indicate their preference without having to mine a block. This naturally led to people starting lots of full nodes to try to sway the perception of popularity. This is a classic sybil attack - one person pretending to be many. Since the vote miners include in blocks is the only one that matters, this tactic was unsuccessful in creating change on the bitcoin network (as should be expected).

How does voting work on the safe network and can it be attacked with sybil attacks?

There are two parts to this. The overall network consensus rules (such as how coins are named, or how vaults are named), and the consensus rules to retain the integrity of each individual piece of data.

Can the network rules be attacked by sybil attacks?

This would be possible but extremely difficult. Let's look at what it would take to change one of the rules. Imagine changing coin names from 32 bits to 33 bits, thus doubling the coin supply.

The attacker would have to control enough of the network that 'old-style 32 bit coins' with 32 bit names were ruled to be invalid. This means an attacker needs to obtain consensus over every coin (28 of 32 nodes for every coin). They'd have to control at least 88% of the network, and have that 88% evenly distributed (even distribution is probably a reasonable assumption though, given how vault naming works). Controlling 88% of the network should be extremely difficult, assuming there's healthy participation in the network - it would be much more difficult than the 51% attack on bitcoin.

Can the individual data be attacked by sybil attacks?

Yes it can, but again, it's extremely hard. The comparable 'ideal' to bitcoin voting is 'one vault one vote'. There is very little cost to starting a vault, so there's nothing to stop someone creating thousands or millions of malicious vaults and controlling the consensus mechanism that way. The issue is, they have no way to control the closeness of their nodes, so it's very difficult for them to form a close group consensus, especially if one or more people are simultaneously attempting a sybil attack, or there's significant healthy participation in the network. It also comes back to incentives - even if they could, why would they want to? They'd get more reward for utilizing the vaults as a resource than they would using them to perform malicious actions.

It's worth reading more about attacks to the safe network, since this is a complex topic and one that by my own confession isn't covered in adequate detail here. Check the [attacks on the safe network](https://safenetwork.wiki/en/FAQ#Attacks_on_the_SAFE_Network) section of the wiki for some additional info.

[Back to Table Of Contents](#and-sybil-attacks_toc)

### Proof of burn

This is an interesting one. Proof of burn involves 'destroying' coins forever, sending them somewhere they can never be spent from again. This has some legitimate and positive uses, although can also be a negative side effect for novice developers.

It's proposed that safecoin will be issued after proof of burn of maidsafecoin (which is on the mastercoin network). This facilitates the conversion of the intermediate maidsafecoin token into safecoin.

The details aren't clear exactly how this will work, since safecoin hasn't been coded yet, but there is [some info](https://safenetwork.wiki/en/FAQ#How_will_MaidSafeCoin_be_converted_to_safecoin_.3F) on the wiki.

More interestingly, safecoin itself can be burned. It's the same as for bitcoin or any crypto currency - change the owner to a public key that has no known private key, and it becomes impossible to spend. This means it's possible to accidentally destroy safecoin the same way bitcoin sent to 1BitcoinEaterAddressDontSendf59kuE will never be spent.

[Back to Table Of Contents](#proof-of-burn_toc)

### Divisibility

Bitcoin is considered expensive at several hundred dollars a coin, but because it's divisible it can still be used for making payments of a fraction of a cent.

How divisible is safecoin?

This is one of those surprisingly-not-so-simple questions to answer.

Because of the structure of safecoins as discrete coins, it's not easy to simply allocate some-portion-here and some-portion-there like you do with outputs in a bitcoin transaction. This would scatter the data for the coin in ways that cannot be easily retrieved.

There's a proposal for how divisibility will be achieved, but no agreement.

Rather than make things up I'll leave this section in a state of ambiguity, which also serves to reflect the state of this issue.

Have a look at [safecoin management](https://github.com/maidsafe/rfcs/blob/master/text/0012-safecoin-implementation/0012-safecoin-implementation.md#safecoin-management) in the rfc, and at this [long conversation about divisibility](https://forum.safenetwork.io/t/safecoin-divisibility/4806) on the forum.

I feel this will be one of those issues similar to the bitcoin blocksize debate. It could easily get out of hand. It's obvious that 4.3B atomic units is nowhere near enough for a modern world which already has IPV4 problems (coincidentally also a 2^32 space).

[Back to Table Of Contents](#divisibility_toc)

### Addresses

Bitcoin uses addresses as a convenient way to receive bitcoin.

How does safe make it simple to receive safecoin?

Since safecoin has not yet been implemented, this is not totally clear. What is known is that a public key will be used to designate the current owner of the safecoin, which is a 256 bit value. This requires the receivers public key to be communicated somehow to the sender. The scheme for encoded public keys (if any) is not yet clear.

To obtain a better understanding of how the owner of a coin is designated, check the [detailed design](https://github.com/maidsafe/rfcs/blob/1cd4ed22709fed673f5ce51c1d861d879abd7aec/text/0000-Unified-structured-data/0000-Unified-structured-data.md#structureddata) of structured data in the rfc.

[Back to Table Of Contents](#addresses_toc)

### Irreversible

As mentioned in the section labeled digital cash, bitcoin is irreversible.

So is safecoin!

Any coin can only be transferred if the signature of the transaction matches that of the current owner of the coin.

This means if the coin is transferred to someone else, the previous owner can no longer make changes to that coin. The change is irreversible.

An interesting point on this is that unlike bitcoin which must have the transaction included in a block before it's considered irreversible, safecoin is instantly irreversible.

Note that transferring safecoin is a POST action, not a PUT, so costs nothing to perform. This is outlined at the bottom of the [validation section](https://github.com/maidsafe/rfcs/blob/1cd4ed22709fed673f5ce51c1d861d879abd7aec/text/0000-Unified-structured-data/0000-Unified-structured-data.md#validation) of the unified structured data rfc. This is a big advantage over bitcoin, where every change to ownership costs money. Lightning network will go a long way to improving that for bitcoin, but at the added cost of complexity.

[Back to Table Of Contents](#irreversible_toc)

### Genesis block

There really is so much to say about the genesis block. Encoding political messages into it, bootstrapping the network from it, quirks like being unable to spend it - the genesis block is a little world unto itself.

All this detail can lead us into some interesting areas of the safe network.

Encoded political messages. Perhaps the most significant part of this in relation to safe is the nature of public vs private data on the safe network.

The safe network can function as a single epic server for static web content. With built in DNS. And SSL. It's basically a new version of the internet, replacing layers 3-7 of the OSI network model. How does it do this? The thing to research is public data vs private data on the safe network. Also consider, much of the web these days is dynamic, not static. There's already existing solutions to dynamic content on the safe network, well worth checking out how it works.

Bootstrapping the network. A concern for peer to peer networks is how to initially find other peers. This introduces a single point of failure and control. Bitcoin used IRC, safe uses either the last known set of nodes it connected to, or falls back to a hardcoded list.

Quirks of the genesis block. Are there any quirks to starting the safe network? Not especially. There's no time-factor in the safe network, so the idea of genesis is far less relevant. This is also true of not having blocks; sequential ordering doesn't matter in the safe network, despite being a critical component in the operation of bitcoin and the blockchain.

There is one quirk: consider that 'safecoin can only be mined by storing data' but 'storing data requires safecoin'. How can this chicken and egg problem be solved? There's a proposal which can be read in the section titled [bootstrap with clients](https://github.com/maidsafe/rfcs/blob/cd47937ebf053e90bde20f18eced2866854e8234/text/0012-safecoin-implementation/0012-safecoin-implementation.md#bootstrap-with-clients) in the safecoin implementation rfc.

[Back to Table Of Contents](#genesis-block_toc)

### Antifragile honeybadger

Bitcoin is described as antifragile. This means that as it faces adversity it grows stronger, not weaker.

How does safecoin fare in the face of adversity?

The only honest answer is, we don't know. It's a reasonable assumption that safe will exhibit the same antifragile properties as bitcoin since this property rises from the incentive structures, and the two systems are very similar in that respect.

[Back to Table Of Contents](#antifragile-honeybadger_toc)

### Testnet

Bitcoin has a testnet which allows for experimentation and real world testing on non-production environments.

How is the safe network tested through all the different phases of the software lifecycle?

This is tough to answer without a fully developed network, but there are some early indicators.

The source code has unit tests.

There are test networks currently operating on closed networks under the control of the maidsafe organization. This was recently opened up temporarily to public users, and more tests involving the public will continue into the future. Data on these test networks is frequently wiped so they aren't considered useful for public consumption at this stage.

Savvy users may be able to set up their own isolated safe network, but it's not documented how to do this and is not considered a reasonable undertaking at this point in time.

I imagine in the future there will be a complementary testnet like with bitcoin, including a valueless test safecoin token. We'll have to wait and see what happens with the development, and this is certainly one area of the safe network which would benefit from additional contributions.

Bitcoin has 'bitcoin in a box' which allows users to run private instances of their own bitcoin network for testing and developing. This will almost certainly be a feature incorporated into the safe toolkit.

Testing churn, quorum sizes, latency etc will be extremely important to assessing the viability of the network.

[Back to Table Of Contents](#testnet_toc)

### Satoshi Nakamoto

Satoshi is infamous for his genius and his anonymity.

Who are the key people behind the safe network?

The story is not as romantic but the people are just as brilliant! David Irvine is the main front of the project, and has been involved since the inception of the project back in 2006. He's currently very active developing, designing, documenting, and in the safe community.

Like bitcoin, there are prominent developers and community members working on safe. It's best to get involved in the community to gain a sense of who these people are. Overall, because of the modest amount of attention safe has received, the people do not tend to have the kind of cult-of-personality that people like Greg Maxwell, Peter Todd, Gavin Andresen, Andreas Antonopolous, etc have in the bitcoin community. This is healthy in some ways, but will be interesting to see how it evolves if the safe network gains significant traction.

[Back to Table Of Contents](#satoshi-nakamoto_toc)

### Byzantine generals problem

A phrase loved by bitcoin nerds, especially because it has a z in it so it sounds really cool, is the byzantine generals problem.

I know the question is burning in your mind: how does the safe network solve the byzantine generals problem? It was burning in mine, and here is what you need to know.

The byzantine generals problem occurs when nodes receive conflicting information and must resolve between themselves which is the correct information.

Bitcoin solves this by proof of work - whichever chain of proofs is the longest is the true source of information.

Safe solves this using a combination of techniques: close group consensus, proof of resource, and vault ranking.

"Close group consensus". It's been mentioned over and over and is the most important aspect to understanding the safe network.

Data can only enter the network if it has achieved close group consensus. This means that even as the network changes due to vaults joining and leaving, there can be confidence that data integrity will be retained because the data has been verified and stored in a particular manner. Without close group consensus, data would not be stored in this special way, and may be vulnerable to corruption due to future network changes.

Once data has entered the network, it retains truth by being subject to proof of resource. Proof of resource is itself subject to close group consensus.

The final part in retaining truth is vault ranking, where 'generals' that report non-consensus data to byzantine are demoted. This keeps the network honest and ensures data retains truth.

This solution is _significantly_ different to bitcoin and blockchains. It may not make sense straight away. I strongly encourage you to read more about close group consensus, proof of resource, and vault naming and ranking. It's incredibly interesting. This is one of those few moments in life where you'll feel you've experienced a work of art, much like you probably felt when you finally understood how blockchains worked.

Trust me on this, read about those topics; if you like dropping a bit of byzantine into your conversations you'll definitely enjoy this.

[Back to Table Of Contents](#byzantine-generals-problem_toc)

### Wallets

Bitcoin wallets are not inherent to the bitcoin network or blockchains. Wallets are a third-party piece of software that keep track of what money you have and when and where you spent it. The blockchain makes it relatively easy (if not subtly challenging) to create bitcoin wallet apps, and many different wallets exist to suit a wide variety of needs.

How does a user keep track of their safecoins on the safe network?

This is an interesting question, because of the inherently different representation of coins on the bitcoin and safe network. There are built in tracking mechanisms in the safe network, read about them on the [safecoin management](https://github.com/maidsafe/rfcs/blob/cd47937ebf053e90bde20f18eced2866854e8234/text/0012-safecoin-implementation/0012-safecoin-implementation.md#safecoin-management) section of the safecoin implementation rfc.

But let's do a somewhat ludicrous thought experiment in the meantime.

Bitcoin wallets look at every transaction ever made and find the ones that are useful to it and combine them all into a tracking mechanism called a wallet. Ludicrous!

The equivalent on the safe network (if a tracking mechanism were not already built in) would involve looking at every individual safecoin (all 4.3B of them), and seeing which ones belong to me. Also ludicrous!

Which one sounds crazier? I don't know myself! Let's wait until there's an official implementation of the network and safecoin and wallets, and in the meantime read whatever documentation may be available.

There are some other considerations which can be made.

One is that when I'm paid, instead of detecting a single transaction like in bitcoin, I will need to detect x number of coins being transferred into my name. This is x actions instead of one. This could get quite interesting.

Another issue is, how does a wallet know when it has received new coins? Unless the wallet is listening to every coin, it may not be aware that a coin on some part of the network has been modified to belong to it. This seems like it will be solved by having the management of coins be performed by the network itself, rather than the client. Read more about [account management](https://github.com/maidsafe/rfcs/blob/1cd4ed22709fed673f5ce51c1d861d879abd7aec/text/0012-safecoin-implementation/0012-safecoin-implementation.md#account-management) in the safecoin implementation rfc.

[Back to Table Of Contents](#wallets_toc)

### Multisig

Bitcoin transactions are actually tiny programs. They're verified by executing the program and checking the output is valid. This goes against the intuitive notion of a transaction as a simple 'signature checking' mechanism. Multisig is a good example of the power of the bitcoin transaction scripting language. The language opens powerful control mechanisms for data flow on the network. Platforms like ethereum take it a step further again, offering fully turing complete smart contracts.

How does safe network handle complex permissions for the movement of data on the safe network?

This is another two-parter. Immutable data does not move, so we'll ignore that here. The second part is mutable data, ie structured data.

Structured data does allow multisig control of coins. In the [validation](https://github.com/maidsafe/rfcs/blob/1cd4ed22709fed673f5ce51c1d861d879abd7aec/text/0000-Unified-structured-data/0000-Unified-structured-data.md#validation) section of the structured data rfc, it's clearly shown and explained how multisig will be implemented.

I personally feel this is a lost opportunity, and that a more generic scripting signature system like bitcoin should be used in structured data. This would allow for powerful smart contracts to govern the movement of data, and facilitate extremely interesting interactions on the network. There is no doubt in my mind that structured data will change significantly in regard to the signing logic, since this could also open the door to pay-for-computation-resource rather than just storage and bandwidth resource.

The similarities and unmet potential in the design of structured data compared with bitcoin and ethereum hint at the opportunities that may be unlocked via safecoin in the future.

I think the decision to keep it simple for now is a good one, since it allows the development to focus on key components of the network rather than 'nice but not essential future stuff'. Very pragmatic, so long as it doesn't end up with a blockage to progress later due to governance issues.

Note that multisig in safecoin is not m-of-n like with bitcoin. Multisig safecoin is strictly 'at least 50% of signers'. I think aiming for m-of-n is setting the goals way short of the target, and I'm sure once you understand how structured data works you will agree. 50% multisig is an appropriate pragmatic approach in these early days of the network, but be prepared for radical change to this in the future. I'm sensing governance and rules issues ahead!!

[Back to Table Of Contents](#multisig_toc)

### Smart contracts

As discussed in the multisig section, smart contracts are a hot topic in the world of blockchains, especially ethereum and rootstock.

Does safe support smart contracts?

Yes and no. The structured data component which is responsible for safecoins does not support smart contracts as we know them. It may in the future, but not the foreseeable future. One of the big decisions facing smart contracts is whether to make them turing complete, and if so, how to manage the incentive structure and costs around looping logic. All this is for the future, but the underlying safe network seems incredibly strong, and that strength starts with structured data.

It's also possible that other structured data tokens besides safecoin may implement smart contracts, we can only know once the network is live and apps are being developed using the network.

Due to the efficiency of the safe network and the design around proof-of-resource instead of proof-of-work or proof-of-stake, it seems quite natural that smart contracts will live on this network, incentivising the consumption of computation resource along side the current design of storage and bandwidth resource.

There may be other ways to implement smart contracts on the safe network other than utilizing safecoin. If you can think of any please add to the discussion using the link at the very bottom of all of this.

[Back to Table Of Contents](#smart-contracts_toc)

### Signed messages

One of the little used and sideshow benefits of bitcoin is the built in ability to sign and verify messages cryptographically.

The safe network operates on the same asymmetric public/private key cryptography as bitcoin, and can also perform message signing and verification.

To take this a step further and ask whether the safe network has any other little sideshows up its sleeve is a good question, but difficult to answer.

Where does the differentiation lie between a legitimate application and a sideshow advantage? Because of the extremely general nature of the safe network as a data storage platform, much more general than the bitcoin blockchain, it's more difficult to differentiate between 'sideshows' vs 'trivial applications'. Perhaps the difference isn't even important anyway.

No other sideshows of the safe network come to mind in the bitcoin sense of a sideshow, but there may be some out there which creative community members will bring to light.

[Back to Table Of Contents](#signed-messages_toc)

### Trustlessness

Bitcoin is trustless. Perfectly, purely, beautifully trustless. This means that transfers can happen in a network where the members may be malicious.

The safe network is also trustless.

Since both the bitcoin and the safe network are decentralized, they're trustless in the sense that there's no single point of failure or authority or control. You don't have to trust anyone. You yourself can join the network any time and leave any time, no lockin, no contract, no bans, no trust.

There is some component to trustlessness on the safe network which differs to bitcoin. Proof of work is what makes bitcoin trustless, and the degree of security it offers is well studied and understood over a wide variety of network conditions. Close group consensus has undergone some scrutiny of the security it provides, which appears comparable to the security of proof of work. But I feel the security of close group consensus hasn't been clearly demonstrated yet. It's possible to do analysis of the security of close group consensus, to come up with probabilities and costs to break that security under various network conditions, but I have yet to see the results of such an analysis. If you know of one, please post it to the discussion using the link at the very bottom of all this.

[Back to Table Of Contents](#trustlessness_toc)

### Cold storage

The mantra to safely using bitcoin is 'cold storage'. All the exchanges have it, individuals are compelled, hackers hate it.

What degrees of security can be applied to the safe network?

There are a few parts to this.

Firstly, security of keys for modifying structured data. This is identical to bitcoin and the exact same techniques apply. You can use cold storage here!

Secondly, security of account details for the network. These allow you to access your private data, but nobody else. How can a user keep this information secure?

Unlocking your account requires three pieces of data. A keyword, a pin, and a password. It's worth reading about why this [curious login mechanism](https://safenetwork.wiki/en/Accessing_the_SAFE_Network) is used instead of the username password paradigm we're all familiar with. It's something you could be explaining to new users _a lot_.

So the takeaway with client login is that the user has to keep three bits of information secure. I'm not that convinced it'll work that well. People tend to pick terrible passwords, and now there's three parts to remember it'll only make it weaker or people will write it down which is probably not going to be stored securely, or will be lost. To me, this is one of the great yet weak parts of the network.

You can surely see the weakness, but what's so great about it? Convenience. This one login is used as a seed to generate a unique login for every app that uses the safe network. This (to me anyway) is amazing. Reuse of passwords will be a thing of the past. Short and insecure passwords, also in the past. This style of login and identity management has been a long time in the making, hopefully the safe network popularizes it and it works as expected. We can only know by seeing how users use it.

It raises a good question though - why do I need an account, where are my login credentials stored and how do I change them?

You only need an account if you want to store your data on the network. To view data does not require an account.

Account credentials aren't really _stored_ anywhere. They're kept in RAM at login and retained in RAM until the user logs out or closes the application. When the safe network connection is closed, the credentials are freed from memory. Any data saved to the network is encrypted using these credentials and can only be retrieved by using those same credentials. This has got me thinking of a 'remember me' feature, it seems inevitable. Could be really handy - one strong password to decrypt the trilogy of information to log in (the trilogy can also be long values since they don't need to be remembered). I'm sure there will be lots of innovation on the client side.

This means 'there are no accounts on the safe network, just data'. To extend that logic, it's not possible to brute force an account in the traditional sense of 'obtain a copy of the encrypted or hashed account details and brute force them to plaintext'. It _is_ possible to simply guess account details over and over, but every account guess will lead to the creation of a legitimate (but probably empty) account on the safe network. If you guess a non-empty account, huzzah, but chances of that should be low. So this is a bit of a stalemate for security I feel. On one hand, application logins are simplified and more secure, on the other, they're still vulnerable to poor user selection of client credentials.

The question remains, how does a user change their account details like their password?

I don't have a good answer to this. If you have one, please let me know using the link at the bottom of this whole thing.

Read more about the [client login process](https://github.com/maidsafe/rfcs/blob/1cd4ed22709fed673f5ce51c1d861d879abd7aec/text/0010-Launcher-as-a-service/0010-Launcher-as-a-service.md) on the launcher rfc.

[Back to Table Of Contents](#cold-storage_toc)

### The bitcoin community

The bitcoin community is well known for being extremely enthusiastic, with many other less desirable adjectives equally applicable.

What's the safe community like and what issues has it presented for the progress of the safe network project?

This is something you're best to judge for yourself.

* [Forum](https://forum.safenetwork.io/) is the main place to commune.
* [Reddit for maidsafe](https://www.reddit.com/r/maidsafe) is the bigger reddit community.
* [Reddit for safe network](https://www.reddit.com/r/safenetwork) is the smaller reddit community.

I've found the community to be extremely welcoming and friendly, although there are of course a few bristly interactions and misunderstandings. The moderators and team of experts seem friendly and highly engaged. It's clearly still early days for the community, and it's generally a pretty nice place to be.

A good example of how bitcoiners tend to engage with the safe community also doubles as [an example of how to become frustrated](https://forum.safenetwork.io/t/how-does-maidsafe-solve-double-spending/257) (fault probably lies with both communities here).

[Back to Table Of Contents](#the-bitcoin-community_toc)

### BIPs

Bitcoin has a system for suggesting improvements called Bitcoin Improvement Proposal (BIP). This tool for organizing developers and ideas into a coherent form has been extremely successful and resulted in a lot of innovation implemented in bitcoin.

Does safe have something comparable?

At this stage the safe process is slightly more fragmented, but there is a comparable process using the familiar term Request For Comments (RFC).

The [RFC process](https://github.com/maidsafe/rfcs) is pretty solid and contains a fantastic wealth of information. Note that actual comments and discussion for each topic reside in the [issues](https://github.com/maidsafe/rfcs/issues) part of the rfcs repository.

There's also weekly plain language updates from the development team on [the updates category](https://forum.safenetwork.io/c/development/updates) of the forum which are a useful source of current developments.

BIPs are amazing and I regularly refer to them. The same is true of safe RFCs, so get stuck in and have a read. They'll be most useful once a mental map of the network has been established in your mind, but they do generally stand alone fairly well.

[Back to Table Of Contents](#bips_toc)

### Revolutionary concepts

Everyone stores everything forever. How can that possibly work? Bitcoin showed it can.

There's got to be an equally ludicrous objection about the safe network, but I haven't crystallized one yet.

The point is, don't let your current understanding of what is impossible stop you from seeing the technical operation of the safe network. It will intrigue you if you take the time to learn about it, probably in the same way bitcoin made you feel when you first understood it. The process of learning may be frustrating and fragmented, but it's worth it. Heck, I wrote all this, can't be _that_ bad!

[Back to Table Of Contents](#revolutionary-concepts_toc)

### Whitepaper

The bitcoin whitepaper is a well respected document for it's concise yet fairly complete description of the bitcoin network.

The safe network whitepaper is, shall we say, underwhelming and contains outdated or irrelevant information. You can read it [here](https://github.com/maidsafe/Whitepapers/blob/63af19e3e39d60a43cb722e32270fab40213abf1/Project-Safe.md). This is a link to the first version of the whitepaper, it has a more recent copy in the repository history.

This illustrates a more general issue with the safe network documentation - it's fragmented and spread over many years and formats, which makes it challenging to find out more about the network. I'm aware this very document itself is going to contribute slightly to that problem. There's a plan to fix the overall state of maidsafe documentation, but (ironically) I can't find where that's stated. In the meantime, have a look at [the roadmap](http://maidsafe.net/roadmap) for the project; it seems pretty interesting and gives some good hooks into what topics to research if you want to learn more about the network.

[Back to Table Of Contents](#whitepape_toc)

### First mover advantage and network effect

One of the reasons bitcoin is so strong is it was first. People want to be involved in a project that is likely to succeed, and bitcoin has shown the greatest success so far. The ability of bitcoin to incorporate new innovations from altcoins makes it extremely difficult to replace now that it's established as the leader.

Can the safe network overcome this?

Another speculative section, but let's look at the possible reasons safe may rise in popularity.

The fundamental difference of not using a blockchain may lead people to prefer the safe network if it can prove itself more efficient for users. It may be that users understand there's basically no difference between 'data' on the safe network and 'money' on the bitcoin network, and they may decide to use the more efficient system.

Safe has flown under the radar despite the impressive technical accomplishments, and will probably continue to do so until the network is live and can prove how well it achieves the goals (or not). In contrast, popular perception of bitcoin is being hijacked by the notion of 'the blockchain, not bitcoin', which may preface another interesting dynamic in the reception of the safe network once it gains more attention.

I personally think the transaction mechanism of safecoin is superior to bitcoin, and think it will eventually displace bitcoin for _transfers_ of wealth. Whether safecoin can capture the _storage_ of wealth component of bitcoin is totally uncertain. The monetary policy behaves similarly to bitcoin, so there is some chance it may be used as a general store of value.

Uninformed hype seems par for the course when it comes to cryptocurrency. It will be interesting to see how safe navigates those waters and how the learning curve unfolds. It took bitcoin a while to gain traction, partially because of the complexity. Safe certainly has similar issues of complexity and education to overcome.

There will be no shortage of opinions about this topic. Feel free to add your thoughts to the discussion using the link at the very bottom of all this.

[Back to Table Of Contents](#first-mover-advantage-and-network-effect_toc)

### Hashing the blockchain

There's a lot of lingo in bitcoin; if you've ever listened to nerds talking about it you'll know what I mean. It sounds like a foreign language.

How does a hardcore safe network conversation sound? Would a bitcoiner be able to follow it?

By far the most important term to understand is close group consensus. This is the 'blockchain equivalent term' which makes the safe network possible.

Here's some of the essential phrases and words for taking part in a technical conversation about safe network:

* churn
* close group consensus
* crust (connected rust)
* distributed hash table (DHT), especially the kademlia implementation
* farming
* k-buckets, especially the [unique implementation](https://github.com/maidsafe/rfcs/blob/1cd4ed22709fed673f5ce51c1d861d879abd7aec/text/0019-new-kademlia-routing-logic/0019-new-kademlia-routing-logic.md) in the safe network
* network addressable element
* opportunistic caching
* recycling safecoin
* self-encryption
* structured data (mutable vs immutable data)
* vault
* vault persona
* vault ranking
* xor distance

There's also some interesting comments from prominent figures in the bitcoin and blockchain community:

* [Vitalik Buterin comments](https://forum.safenetwork.io/t/vitalik-buterin-on-maidsafes-consensus-mechanism/3542) on the consensus mechanism - a great read.
* [Andreas Antonopolous](https://forum.safenetwork.io/t/andreas-antonopoulos-gives-thoughts-on-the-safe-network/8662) comments on his interpretation of the safe network.

If you know of any other comments that would help bridge the gap between bitcoin and safe, please let me know using the link at the bottom of all this.

[Back to Table Of Contents](#hashing-the-blockchain_toc)

## More

It's one thing to look at safe network from the perspective of a bitcoiner, but to really appreciate safe it's necessary to step away from the nomenclature of bitcoin and read about the network as a standalone project. Not every aspect of the safe network has been covered here, there are other aspects that are unique to safe and are difficult to discuss from a bitcoin-centric perspective.

To properly understand the safe network, the following resources are good places to start:

* [Source code](https://github.com/maidsafe)
* [Wiki](https://safenetwork.wiki/en/Main_Page)
* [Forum](https://forum.safenetwork.io/)
* [Book](http://systemdocs.maidsafe.net/content/en/)
* [Website](http://maidsafe.net/)
* [Blog](https://blog.maidsafe.net/)

If you have a correction, question or comment, please let me know in [the discussion on the forum](https://forum.safenetwork.io/t/the-safe-network-explained-using-bitcoin-terminology/9054).
