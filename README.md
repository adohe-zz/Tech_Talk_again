# Etcd And Distributed system

Just give a brief but comprehensive enough introduction to Etcd and some distributed system common knowledge.

##Etcd

###What is Etcd?
	
Accorrding to Github Etcd repo:

	Etcd is a distributed, consistent key value store for shared configuration and service discovery. It is written in Go and uses the Raft consensue algorithm to manage a high-available replicated log.

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
###What is CAP?
###Paxos, Raft and ZAB



[1]: http://raftconsensus.github.io/
[2]: http://en.wikipedia.org/wiki/Replication_(computing)
[3]: http://en.wikipedia.org/wiki/Name_service
[4]: http://en.wikipedia.org/wiki/Configuration_management
[5]: http://en.wikipedia.org/wiki/Synchronization
[6]: http://en.wikipedia.org/wiki/Leader_election
[7]: http://en.wikipedia.org/wiki/Message_Queue
[8]: http://en.wikipedia.org/wiki/Notification_system
