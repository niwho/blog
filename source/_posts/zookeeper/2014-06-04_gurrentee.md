title: zookeeper 特性
date: 2014-06-05 00:00:33 
updated: 2014-06-06 00:33:13
tags:
- zookeeper
categories:
- gurrentee
---

Summary: zookeeper gurrentee

###What ZooKeeper Guarantees about Watches
With regard to watches, ZooKeeper maintains these guarantees:
<!--more-->
* Watches are ordered with respect to other events, other watches, and asynchronous replies. The ZooKeeper client libraries ensures that everything is dispatched in order.
* A client will see a watch event for a znode it is watching before seeing the new data that corresponds to that znode.
* The order of watch events from ZooKeeper corresponds to the order of the updates as seen by the ZooKeeper service.

###Things to Remember about Watches
* Watches are one time triggers; if you get a watch event and you want to get notified of future changes, you must set another watch.
Because watches are one time triggers and there is latency between getting the event and sending a new request to get a watch you cannot reliably see every change that happens to a node in ZooKeeper. Be prepared to handle the case where the znode changes multiple times between getting the event and setting the watch again. (You may not care, but at least realize it may happen.)
* A watch object, or function/context pair, will only be triggered once for a given notification. For example, if the same watch object is registered for an exists and a getData call for the same file and that file is then deleted, the watch object would only be invoked once with the deletion notification for the file.
* When you disconnect from a server (for example, when the server fails), you will not get any watches until the connection is reestablished. For this reason session events are sent to all outstanding watch handlers. Use session events to go into a safe mode: you will not be receiving events while disconnected, so your process should act conservatively in that mode.

###Consistency Guarantees
ZooKeeper is a high performance, scalable service. Both reads and write operations are designed to be fast, though reads are faster than writes. The reason for this is that in the case of reads, ZooKeeper can serve older data, which in turn is due to ZooKeeper's consistency guarantees:

####Sequential Consistency
Updates from a client will be applied in the order that they were sent.

####Atomicity
Updates either succeed or fail -- there are no partial results.

####Single System Image
A client will see the same view of the service regardless of the server that it connects to.

####Reliability
Once an update has been applied, it will persist from that time forward until a client overwrites the update. This guarantee has two corollaries:

* If a client gets a successful return code, the update will have been applied. On some failures (communication errors, timeouts, etc) the client will not know if the update has applied or not. We take steps to minimize the failures, but the guarantee is only present with successful return codes. (This is called the monotonicity condition in Paxos.)
* Any updates that are seen by the client, through a read request or successful update, will never be rolled back when recovering from server failures.

####Timeliness
The clients view of the system is guaranteed to be up-to-date within a certain time bound (on the order of tens of seconds). Either system changes will be seen by a client within this bound, or the client will detect a service outage.

Using these consistency guarantees it is easy to build higher level functions such as leader election, barriers, queues, and read/write revocable locks solely at the ZooKeeper client (no additions needed to ZooKeeper). See Recipes and Solutions for more details.

###Note
>Sometimes developers mistakenly assume one other guarantee that ZooKeeper does not in fact make. This is:  


>* Simultaneously Consistent Cross-Client Views
ZooKeeper does not guarantee that at every instance in time, two different clients will have identical views of ZooKeeper data. Due to factors like network delays, one client may perform an update before another client gets notified of the change. Consider the scenario of two clients, A and B. If client A sets the value of a znode /a from 0 to 1, then tells client B to read /a, client B may read the old value of 0, depending on which server it is connected to. If it is important that Client A and Client B read the same value, Client B should should call the sync() method from the ZooKeeper API method before it performs its read.  

>So, ZooKeeper by itself doesn't guarantee that changes occur synchronously across all servers, but ZooKeeper primitives can be used to construct higher level functions that provide useful client synchronization. (For more information, see the ZooKeeper Recipes. [tbd:..]).


