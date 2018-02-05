---
layout: post
title:  "Splitting and Merging"
author: "Ian Coleman"
date:   2017-11-24 00:00:00 +0000
updated: 2017-11-27 00:00:00 +0000
---
I think the mechanism by which the network sections split and merge is quite fascinating and underappreciated, so I wrote this up. Hopefully the elegance of the implemented algorithm can be better appreciated by those who may otherwise not read code etc.

The code for splits and merges is quite difficult to interpret but I think I've got it right. If there's any corrections on the technical details I'd be very glad to know so I can incorporate the changes.

# Splitting and Merging Sections

Splitting and merging sections on the SAFE Network is an important and basic part of the network functionality. It determines which nodes are responsible for your data and how that responsibility changes over time. It's quite an elegant and fascinating mechanism and deserves some explanation.

## Why Split And Merge

The SAFE Network consists of many vaults. 8 or more vaults can combine to form a cluster of nodes known as a 'section' which becomes responsible for any data allocated to it by the network.

The structure of the section that form the SAFE Network is constantly changing. New vaults will periodically join a section (making the section larger) and existing vaults periodically leave the section (making the section smaller).

Very large sections are detrimental to the network because they create a centralisation risk to the data they're responsible for.

Very small sections are also detrimental because there's less redundancy of data and a greater risk of data loss.

So to achieve a balance, large sections can split into two smaller sections and small sections can merge with another to form a larger section.

This splitting and merging mechanism ensures data remains secure and efficiently distributed on the network.

## Numbers

There are only a couple of important numbers to keep in mind.

The minimum number of vaults in a section is 8.

The minimum number of vaults to form two new sections is 11 per new section.

## Identifying Sections

Sections need a way to organize and refer to themselves and each other. This is done by using the common leading digits in the vault names (ie the 'prefix') as the name for the section.

Consider two vaults, one with name starting with `001100...` and the other starting with `001001...`.

These vaults both start with `0` so could be part of a section identified by that prefix, ie `section 0`

But they could never be part of `section 1` since the start of their name doesn't match that section prefix.

Similarly, these vaults both start with `001` so could be part of a section identified by that prefix, ie `section 001`

Vaults try to join the 'longest' prefix they can match to, since this leads to vaults being as evenly distributed through the network as possible, and can only be part of one section at a time.

The section prefix determines how they should split or merge, and allows sections to refer to each other during these processes.

## Splitting

If a section can safely split into two new sections it will do so.

The rules for this are very simple:

* Extend the current section prefix into two new hypothetical prefixes by adding a `0` in one case and a `1` in the other
* Simulate splitting the current vaults in the section into these two new hypothetical prefixes
* If both new sections would have at least 11 members, do the split
* Otherwise, don't split and stay as a single section

For example, consider `section 0` with 25 vaults.

Say 10 of these vaults have a name starting with `00`

And the remaining 15 of these vaults have a name starting with `01`

Because the hypothetical `00` prefix would only have 10 vaults nothing further happens (ie 11 vaults are required before splitting).

But then a new vault with name starting `00` joins the section, taking the total to 26.

This means the hypothetical prefix `00` would now have 11 vaults, so a split should happen.

The vaults organize between themselves to split into `section 00` with 11 vaults and `section 01` with 15 vaults.

The data is also split between these two new sections, so each section has about half the amount of data to look after.

Once the sections have split, `section 0` no longer exists! Any vault trying to join `section 0` will have to try instead joining `section 00` or `section 01` instead, depending on the specific name of the new vault.

This splitting can continue as nodes join the network, gradually extending the prefix.

## Merging

Vaults may leave the network at any time. This means sections may end up being so small they can no longer provide sufficient security for the data they're managing. If this happens, the section needs to merge with another.

The rules for merging are also simple:

* If the section size more than 8, don't try to merge
* If the section size is less than 8, try to merge with the sibling section

There's some interesting situations to consider, but let's look at the simplest merge case first:

`section 000` is stable with 8 vaults. But then a vault leaves, reducing it to 7 vaults. This section needs to merge with another section.

`section 000` looks for the *sibling* section, ie `section 001` (which is created at the same time as `section 000` when `section 00` split).

If `section 001` exists, `section 000` will try to merge with it and become `section 00`. This is the simplest merge case.

What if `section 001` doesn't exist (maybe it already split into `section 0010` and `section 0011`)?

`section 000` merges with *all children* of `section 001`, ie with `section 0010` and `section 0011`. This sounds drastic but in reality the imbalance between the number of children siblings have is never too great.

It's worth noting that `section 00` (the section it merges into) cannot exist on the network since any node trying to join `section 00` would instead prefer to join the longer prefix of `section 000` or `section 001` (or any of their children sections). So the merge can always shorten the prefix without conflicting on another section which already might have that prefix, since no such shorter-prefix-section can exist.

Merging is definitely more complex than splitting, but the fundamental decisions are still quite simple. Hopefully someone closer to the code can clarify what happens when the simplest merge situation can't be performed.

One of the neat things about merging and splitting is splitting needs more than 11 vaults for each new section but merging needs less than 8. So even if a vault or two leaves immediately after a split it won't cause a merge. This buffer makes it hard to induce lots of churn just when a section has split by trying to make it merge.

## Section Size Distribution

One of the implicit features of this mechansim is sections have no upper limit on their size.

Most sections will have about 8-16 vaults, but some will contain many more. It all depends on the names of vaults joining the network and whether it leads to splitting or not.

For example, if `section 001` has 50 nodes all starting with `001000...` there can be no split (even though the section is very large!) because it can only split into hypothetical `section 0010`; there are no nodes to form hypothetical sibling `section 0011`. So sometimes large sections will be formed.

The network tends to exhibit a Poisson Distribution of section sizes with the most common size being 12.

## Considerations

There are some more details which are [still being finalised](https://forum.safedev.org/t/section-split-merge-in-plain-english/1163/2) while datachains are being developed.

Large sections will be able to relocate young nodes if a split is desired but not imminent.

Quorum decisions are not formed by the entire section, mainly by excluding the youngest nodes. This means large sections do not necessarily reach quorum decisions any slower than small sections.

## Simulation

Simulating the split and merge of nodes shows some interesting information about the algorithm.

The following comes from simulating a network of 100K nodes (1M joins and 900K departures).

Note

* the simulation does not cap section sizes (ie doesn't account for the as-yet-undeveloped feature where young nodes in large sections are relocated).

* nodes are added until the network is 100K in size, then has a one-joins-one-leaves mechanism for the next 900K nodes.

### Section Size Distribution

The final distribution of section sizes in the network is shown below:

![group_size_distribution]({{ "/assets/938227852f22c8eae45dd13d36fbc0788f40f10f.png" | absolute_url }})

```
size count percent
------------------
   8   163   2.365
   9   345   5.006
  10   538   7.806
  11   746  10.824
  12   794  11.521
  13   746  10.824
  14   604   8.764
  15   578   8.387
  16   494   7.168
  17   373   5.412
  18   344   4.991
  19   283   4.106
  20   231   3.352
  21   170   2.467
  22   131   1.901
  23   112   1.625
  24    81   1.175
  25    53   0.769
  26    36   0.522
  27    25   0.363
  28    20   0.290
  29    14   0.203
  30     7   0.102
  33     2   0.029
  34     1   0.015
  35     1   0.015
```

This distribution does change slightly depending on the initial conditions of the simulation, but overall the distribution is quite consistent.

### Statistics

20,243 splits

13,227 merges

52 was the largest section size throughout the simulation

35 was the largest section size at the end of the simulation

30 sibling nodes were involved in the largest merge

2 sibling sections were involved in the largest merge

## Source Code

The splitting and merging algorithm is fairly difficult to deduce from the source code. The logic is mainly located in [maidsafe/routing](https://github.com/maidsafe/routing), see [node.rs](https://github.com/maidsafe/routing/blob/57a18a6b974628c3be032ca97aced362f1714a9a/src/states/node.rs) and [peer_manager.rs](https://github.com/maidsafe/routing/blob/f8bf1212a7fc75212b6820eb97355ea340cfbee7/src/peer_manager.rs).

The simulation code is at [github.com/iancoleman/safe_network_simulations](https://github.com/iancoleman/safe_network_simulations). The simulation is deterministic, so the same seed will always produce the same simulation which is a handy thing.

## More Reading

There's more detail about this in [RFC 0037 - Disjoint Groups](https://github.com/maidsafe/rfcs/blob/master/text/0037-disjoint-groups/0037-disjoint-groups.md), which does seem to provide some clarity around the merging questions, ie "No two sections must be comparable".

## Summary

The splitting and merging logic is remarkably simple but produces some fairly complex behaviour. The main points I feel are not obvious from the code or RFC documents and worth highlighting are

* splits happen when two valid sections can be formed, not simply if the section is overall large in size.

* merges may include many sections from the sibling side of the merge.

* further development is happening to reduce the occurrence of large sections using a relocation mechanism based on vault ageing.

## Discussion

Discussion can be found at [https://safenetforum.org/t/explaining-group-splits-and-merges/18383](https://safenetforum.org/t/explaining-group-splits-and-merges/18383)
