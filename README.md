# Etcd And Distributed system

Just give a brief but comprehensive enough introduction to Etcd and some distributed system common knowledge.

##Etcd

###What is Etcd?
	
Accorrding to Github Etcd repo:

	Etcd is a distributed, consistent key value store for shared configuration and 	service discovery. It is written in Go and uses the Raft consensue algorithm to 	manage a high-available replicated log.

Etcd focus on being:

- _Simple_: curl'able user facing API(HTTP+JSON)
- _Secure_: optional SSL client cert authentication
- _Fast_: benchmarked 1000s of writes/s per instance
- _Reliable_: properly distributed using [Raft][1]

Etcd use [active][2], leader based replication model.

###Etcd usage scenario

Inspired by ZooKeeper, Etcd, ZooKeeper and even doozerd are all similar in their architecture. All these have sever nodes that require a quorum of nodes to operate (usually a simple majority). They are strongly-consistent and expose various primitives that can be used through client libraries within application to build complex distributed systems. Typical use cases include:

- [Naming service][3]
- [Configuration management][4]
- [Synchronization][5]
- [Leader election][6]
- [Message queue][7]
- [Notification system][8]


##Raft

###What is Raft?

within "[the Raft paper][9]", they describe Raft algorithm like this: Raft is a consensus algorithm for managing a replicated log. It produces a result equivalent to (multi-)Paxos, and it is as efficient as Paxos. Actually it provides the same guarantees as (multi-)Paxos under the same asumptions(they adopt the same system model -- Asynchronous system model). But Raft structure is different from Paxos; This makes Raft more understandable than Paxos and also provides a better foundation for building practical systems. 

###What is consensus?

Consensus is a fundamental problem in fault-tolerant distributed systems. Consensus involves multiple servers agreeing on values. Once they reach a decision on a value, that decision is final. Typical consensus algorithms make progress when any majority of their servers are available; for example, a cluster of 5 servers can continue to operate even if 2 servers fail. If more servers fail, they stop making progress (but will never return an incorrect result).

Consensus typically arises in the context of replicated state machines, a general approach to building fault-tolerant systems. Each server has a state machine and a log. The state machine is the component that we want to make fault-tolerant, such as a hash table. It will appear to clients that they are interacting with a single, reliable state machine, even if a minority of the servers in the cluster fail. Each state machine takes as input commands from its log. In our hash table example, the log would include commands like set x to 3. A consensus algorithm is used to agree on the commands in the servers' logs. The consensus algorithm must ensure that if any state machine applies set x to 3 as the nth command, no other state machine will ever apply a different nth command. As a result, each state machine processes the same series of commands and thus produces the same series of results and arrives at the same series of states.

###What is CAP?

It has been fifteen years since Dr Eric Brewer introducted the idea that there is a fundamental trade-off between *consistency*, *availability*, and *partitoon tolerance*. This trade-off, which has become known as the *CAP Theorem*, has been widely discussed ever since. Some of the interest in the CAP Theorem, perhaps, derives from the fact that it illustrates a more general trade-off that apperas everywhere in the study of distributed computing: the impossibility of guaranteeing both *safety* and *liveness* in an *unreliable distributed system*. You may think Consistency (as defined in the CAP Theorem) is a classical safety property: every response sent to a client is correct; Availability is a classical liveness property: eventually, every request receives a response. Of course partitions (as defined in the CAP Theorm) is a kind of unreliable. I think most of you may be familiar with this beautiful picture: 
![CAP](https://raw.githubusercontent.com/AdoHe/Tech_Talk_again/master/images/CAP.png)

and the famous "2 of 3" formulation, even which is always mislead since it tended to oversimplify the tensions among properties. Please refer this for more details: 
[CAP Twelve Years Later: How the "Rules" Have Changed][10]

###Paxos, Raft and ZAB

Over the last ten years, Leslie Lamport's [Paxos protocol][11] has become almost synonymous with consensus. Unfortunately, Paxos has two significant drawbacks. The first drawback is that Paxos is exceptionally difficult to understand. The second drawback is that the Paxos architecture is a poor one for building practical systems. As a result, practical systems bear little resemblance to Paxos. Each implementation begins with Paxos, discovers the difficulities in implementating it, and then develops a significantly different architecture. Just like the comment from Chubby implementers:

	There are significant gaps between the description of the Paxos algorithm and
	the needs of a real-world system….the final system will be based on an unproven 	protocol.
	
Chubby has implementated Paxos-like algorithm. ZAB (ZooKeeper Atomic Broadcast), which is used in ZooKeeper, also a Paxos-like algorithm. The main conceptual difference between Zab and Paxos is that it is primarily designed for primary-backup systems, like Zookeeper, rather than for state machine replication. For Raft, it is just an alternative to Paxos.

##Etcd in SOA Framework

###SOA Framework

Microservices are currently getting a lot of attention:articles, blogs, discussions on social media, and conference presentations. More and more companies are adapting service-oriented architecture. We build our own SOA Service framework, which enables service auto-registration/auto-discovery. The whole architecture is quite simple:
![SOA](https://raw.githubusercontent.com/AdoHe/Baiji/master/Documents/arch.jpg)

Here SOA Registry Center is a key compoment.

###Etcd in Service Discovery

Service discovery is a key component of most distributed systems and service oriented architectures. The problem seems simple at first: How do clients determine the IP and port for a service that exist on multiple hosts? Usually, you start off with some static configuration which gets you pretty far. Things get more complicated as you start deploying more services. With a live system, service locations can change quite frequently due to auto or manual scaling, new deployments of services, as well as hosts failing or being replaced. Dynamic service registration and discovery becomes much more important in these scenarios in order to avoid service interruption. For a Service discovery, typically there are three properties:

- a consisteny, high-available service store directory.
- service registry and health check mechanism.
- service lookup and connect mechanism.

Etch, based on Raft algorithm provides such properties, and is quite suitable for this scenarios:


[1]: http://raftconsensus.github.io/
[2]: http://en.wikipedia.org/wiki/Replication_(computing)
[3]: http://en.wikipedia.org/wiki/Name_service
[4]: http://en.wikipedia.org/wiki/Configuration_management
[5]: http://en.wikipedia.org/wiki/Synchronization
[6]: http://en.wikipedia.org/wiki/Leader_election
[7]: http://en.wikipedia.org/wiki/Message_Queue
[8]: http://en.wikipedia.org/wiki/Notification_system
[9]: http://ramcloud.stanford.edu/raft.pdf
[10]: http://www.infoq.com/articles/cap-twelve-years-later-how-the-rules-have-changed
[11]: http://research.microsoft.com/en-us/um/people/lamport/pubs/lamport-paxos.pdf
