---
aliases:
- /head/key-concepts-ledger.html
- /HEAD/key-concepts-ledger.html
- /key-concepts-ledger.html
date: '2023-01-26'
menu:
  corda-enterprise-4-7:
    identifier: corda-enterprise-4-7-key-concepts-ledger
    parent: corda-enterprise-4-7-key-concepts
    weight: 1020
tags:
- concepts
- ledger
title: Ledger
---


# Ledger

## Summary

* *The ledger is subjective from each peer’s perspective*
* *Two peers are always guaranteed to see the exact same version of any on-ledger facts they share*

## Video

{{% vimeo 213812040 %}}

## Ledger Data

In Corda, there is **no single central store of data**. Instead, each node maintains its own database of those facts that it is aware of.

The facts that a node knows about are those that it is involved with. For example, if there are nodes representing Alice and Bob on the network and Alice loans Bob some money, both Alice and Bob will store an identical record of the facts about that loan. If the only parties involved with the loan are Alice and Bob, then they will be the only nodes that ever see or store this data.

**Important:** The result of this design is that each peer only sees a subset of facts on the ledger, and no peer is aware of the ledger in its entirety.

Example: A network with five nodes, where each numbered circle on an intersection represents a fact shared between two or more nodes.

{{< figure alt="ledger venn" width=80% zoom="/en/images/ledger-venn.png" >}}
The preceding Venn diagram represents 5 nodes (Alice, Bob, Carl, Demi and Ed) as sets. Where the sets overlap are shared facts, such as those known by both Alice and Bob (1 and 7).

Notably, in this Venn diagram, Alice only shares facts with Bob. Alice does not share facts with any of Carl, Demi or Ed.

**Summary**

The Corda Ledger is a **subjective** construct from each peer’s point of view.

**What does that mean?**

* There are no omniscient peers who can see everything on the ledger.
* Each node sees their own facts, and the facts that they share with others.

For example, Alice and Demi don’t share any facts, so they each see a completely different set of shared facts.

## Shared Facts

In the previous diagram, we could see that although Carl, Demi and Ed are aware of shared fact 3, **Alice and Bob are not**.

﻿On Corda, there is no central or general ledger operating with agency on ﻿behalf of all of the nodes on the network. Instead, each node on the network maintains its own vault containing all of its known facts.

You can think of this vault as being a database or simple table.

{{< figure alt="ledger table" width=80% zoom="/en/images/ledger-table.png" >}}
Where the rows are shared between nodes (facts 1 and 7 in this example), these are shared facts.

Corda guarantees that whenever one of these facts is shared by multiple nodes on the network, it evolves in lockstep in the database of every node that is aware of it. This means that Alice and Bob will both see an **exactly identical version** of shared facts 1 and 7.

**Note:** Not all on-ledger facts are shared between peers. For example, Alice's fact 11 is not shared with Bob. Fact 11 could, in fact, not be shared with any other node at all. If this is the case, it is deemed a unilateral fact.

**Note:** Although there is no central ledger, it is possible to broadcast a basic fact to all participants should you wish to. You would do this by using the network map service to loop over all parties.
