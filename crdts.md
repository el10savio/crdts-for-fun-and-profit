# CRDTs For Fun and Profit

## Introduction

Filled with enthusiasm and determination you set out to build your app. Keeping in mind that this would be the next big thing you start initially prototyping, ideating, and slowly composing and piecing together your application. Amazingly, it's great! works well. You set out to tell your friends and family spreading the word and one fine day, you see a huge influx of users enjoying your app. But then, you realize, this is exactly what has happened now, one big app!

Most software applications start as a three-tiered application, separating the frontend, the backend, and the storage database layer. This architecture works well until it hits scale. A massive blatant problem here we see is, if any single part of our application fails, our whole service fails.

## Scaling above & beyond

To meet the ever-growing demand you then decide to scale the application, we can either scale-vertically or scale-horizontally. Scaling vertically means, to buy better hardware, by obtaining a more performant server with more memory, RAM, etc. Scaling horizontally means to buy more hardware to service our application and deploy it in a clustered way across several machines instead of a single machine.

Scaling vertically does seem promising at first, there will always still be this bottleneck where if the highly performant machine crashes (crashes are inevitable) yet again our entire application will then crash. Also, there exists a finite limit to how much we can scale up our hardware at any point.

Thus, scaling horizontally fares better in the case of resiliency as we can infinitely obtain as many machines as we want (as your budget wants) to then distribute our application across many nodes in a distributed fashion. Each node in the cluster can be individually scaled up or down based on a certain need. Welcome to the world of distributed systems!

## Welcome to distributed systems

Setting up distributed systems is pretty simple. We make multiple replicas of the application and/or the database and put a load balancer in front of them. A load balancer, it routes requests to servers over the network in several different configurations. An example of a load balancer configuration is a round-robin load balancer, here, each request is sent across to each server sequentially in a cycle.

In the case of data stores, putting a load balancer seems plausible at first. But, we quickly stumble upon an interesting problem now. If we have multiple stores of data which we individually read & write requests from, we now have diverged nodes in the network. Now, there's a high chance if there were write entries for a given value in multiple replicas, they'd have become different and thus inconsistent. This is a tradeoff between availability & consistency.

## Paxos

An example of establishing consistency of nodes in a distributed system is to establish consensus for each value for all nodes to persist, to do away with any inconsistencies. One algorithm for distributed consensus is Paxos. It establishes consensus among any number of nodes in a network in the midst of network and node failures. Paxos works by first broadcasting the data to be persisted to all the nodes over the network. Nodes can then choose to accept the message or not, the proposer then gets the responses from each node and thus figures out if a majority has been established or not. Ordering of messages is achieved as each proposed message gets a unique ID tagged along with it.

Consensus is handy to establish highly consistent systems. But, proves intensive on each node and the network as a lot of communication happens in each proposer round to establish a majority. For nodes physically far apart from each other, like nodes in a WAN network across continents, getting them consistent with Paxos has a large overhead. Sometimes, in these cases where we prefer speed and availability over consistency getting nodes to partial consistency for a brief period of time and then eventually consistent is a good tradeoff to perform.

## CRDTs

CRDTs abbreviated as Conflict-Free Replicated Data Types are an interesting data type when used effectively, bend the problem of consistency and aim to get nodes in a cluster highly consistent "eventually" without the need of a consensus round between nodes in the cluster. They also work reliably in the presence of node and network failures. An effect of this is that we can build offline-first applications where data can be stored locally on the client-side and works even if network connectivity is down. An interesting application of CRDTs is used at times in infrastructure to sync data between nodes quickly like text editors and syncing file systems together.

CRDTs have properties which enable nodes to converge eventually to the expected value. Nodes "converge" to the right value by leading in a direction of operation. An example of convergence is a site visitors counter where the count can only increase. Where two site counters tracking the same site have conflicting values, they then get in sync and converge to the right value by both setting their counts to the maximum value, thus moving forward and converging.

Some other properties of CRDTs are associativity where the order of operations is immaterial since associativity guarantees that irrespective of the order, the result of the operations would still be the same value. Idempotency is another property that guarantees that even if duplicate operations are sent multiple times to the node, it still leads to the same result in the node due to the idempotency of the underlying CRDT data type. Some examples of interesting CRDTs are:

***GCounter CRDT***
 GCounters abbreviated as grow-only counters are CRDT counters modified to only increment the count in it and becomes consistent across nodes in a cluster having replicated the counter. They work by setting a value that only increases and during a conflict merge to a value of the maximum count.

***GSet CRDT***
GSets abbreviated as grow-only sets are CRDTs modified to only add elements in it and becomes consistent across nodes in a cluster having replicated the set. They work by adding value that only increases the size of the set and during a conflict, merge to only add new values to it.

Another data type similar to CRDTs which employ Operational Transform on its data by replicating operations across nodes instead of the data. They are used mostly in collaborative text applications.

## Conclusion

CRDTs seem too good to be true, but they do have some disadvantages. Since the nodes would be "eventually" consistent, in the application end we would never accurately know when they would be the right value. Another disadvantage is to maintain deletes or removal of entries in the data at times we use tombstones to mark them for deletion, which increases the overall size of the data structure along with associated metadata to process it.

Overall, CRDTs are a good fit if you can bend the problem space to utilize it and comes with a lot of advantages for the user. Reliability and availability will definitely be improved and fares great to use as a tool to sync data.
