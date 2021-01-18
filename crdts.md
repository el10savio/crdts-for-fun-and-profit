# CRDTs For Fun and Profit

## Introduction

Filled with enthusiasm you set out to build your app. Keeping in mind that this would be the next big thing you start piecing it up together. It's a hit! and people love your app. But one fine day, you see a huge influx of users crashing the system. You then realize you have one big monstrous app that can come crashing down so easily!

Most software applications start as three-tiered applications, the frontend, backend, and a storage database layer. This architecture works well until it hits scale. If any single part of our application fails, your entire application fails.

## Scaling above & beyond

To meet the ever-growing demand you then decide to scale the application. You can either scale vertically or horizontally. Scaling vertically means, to buy better hardware, by obtaining a more performant server with more memory, RAM, etc. Scaling horizontally means to buy more hardware and deploy the application across several machines instead of a single machine.

Scaling vertically does seem promising at first, but there will always be this bottleneck where if your highly performant machine crashes (crashes are inevitable) yet again your entire application will then crash. Also, there exists a finite limit to how much one can scale up hardware.

Thus, scaling horizontally fares better in the case of resiliency as you can get as many machines as you want (as your budget wants) and then spread out the application across many machines. We can scale each machine up or down based on a certain need. Welcome to the world of distributed systems!

## Welcome to distributed systems

Setting up distributed systems is pretty simple. We make multiple replicas of the application and/or the database and put a load balancer in front of them. A load balancer routes requests to servers over the network. An example of a load balancer is a round-robin load balancer, where, each request is sent in a cycle to each server.

In the case of data stores, putting a load balancer seems plausible at first. But, we immediately stumble upon an interesting problem. If we have many stores of data that we read & write requests from, we now have diverged nodes in the network. If we write data in multiple replicas,  eventually due to node and network failures they'd have become different and thus inconsistent. This is a tradeoff between availability & consistency.

## Paxos

To establish consistency of nodes in a distributed system we have to establish consensus for each value for all the nodes to persist. One such algorithm for distributed consensus is Paxos. It establishes consensus among any number of nodes in a network in the midst of network and node failures. 

Paxos works by first broadcasting the data that has to be persisted to all the nodes over the network. Nodes can then choose to accept the message or not, the proposer then gets each node's response and figures out if it is a majority.

Consensus is handy to establish highly consistent systems. But, proves intensive on each node and the network as a lot of communication overhead happens in each round to establish a majority. For nodes physically far apart from each other (like nodes in a WAN network across continents) getting them consistent with Paxos has a large overhead. Sometimes, in these cases where we prefer speed and availability over consistency. Getting nodes to partial consistency for a brief period of time and then eventually consistent is a good tradeoff to perform.

## CRDTs

CRDTs abbreviated as Conflict-Free Replicated Data Types aim to get nodes in a cluster highly consistent "eventually", without the need of a consensus round, even in the presence of failures. An interesting application of CRDTs is that we can build offline-first applications where data can be stored locally.  Also in infrastructure to sync data between nodes quickly like collaborative text editors.

CRDTs have properties that enable nodes to converge eventually to an expected value. Nodes "converge" to the right value by leading in a direction of operation. An example of convergence is a site visitors counter where the count can only increase. When two site counters tracking the same site have conflicting values, they then get in sync and converge to the right value by setting their counts to the maximum value, thus moving forward and converging.

Another property of CRDTs is associativity where the order of operations is immaterial since associativity guarantees that irrespective of the order, the result of the operations would still be the same value. Idempotency is another property that guarantees that even if duplicate operations are sent multiple times to the node, it still leads to the same result in the node due to the idempotency of the underlying CRDT data type. 

Some examples of interesting CRDTs are:

**GCounter CRDT**
 GCounters abbreviated as grow-only counters are CRDT counters modified to only increment the count in it and becomes consistent across nodes in a cluster having replicated the counter. They work by setting a value that only increases and during a conflict merge to a value of the largest count.

**GSet CRDT**
GSets abbreviated as grow-only sets are CRDTs modified to only add elements in it and becomes consistent across nodes in a cluster having replicated the set. They work by adding value that only increases the size of the set and during a conflict, merge to only add new values to it.

Another data type similar to CRDTs which employ Operational Transform on its data by replicating operations across nodes instead of the data. They are used mostly in collaborative text applications.

## Conclusion

CRDTs seem too good to be true, but they do have some disadvantages. Since the nodes would be "eventually" consistent, in the application end we would never accurately know when they would be the right value. Another disadvantage is to maintain deletes or removal of entries in the data at times we use tombstones to mark them for deletion, which increases the overall size of the data structure along with associated metadata to process it.

CRDTs are a good fit if you can bend the problem space for it and comes with a lot of advantages for the user where reliability and availability are a major concern.
