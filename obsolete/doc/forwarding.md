=== Hive Mind 

# 
bzz protocol request handling, forwarding response strategies 

## Prerequisites

In package p2p, there is already a kademlia-based peer discovery protocol that makes sure that
* In each row of the node table up to a certain (known) row, there is at least one node that supports Swarm.
* At least one of each such node is actually connected as a peer.

## Retrieval 

A retrieval request for a key arrives with a key recently unseen.
It is looked up in local store and if not found. its assessed if it is worth having, or
if its proximity warrants its storage with us or not. If deemed too distant it can be forgotten, if within our proximity range then we open a request entry in the request pool. Further requests in the near future asking for the same key will check its status with the this entry.

Immediately upon receving the request target is mapped to its cademlia proximity bin and order those peers by proximity to the target. we immediately send a response too the initiator containing the first n best peers. The response also indicates if we forward the query and our suggested update to the request timeout.

Simultaneously, take the first connected peer and forward the request with timeout t where t equals?
would it make sense to also report about our request pool.

If we do not receive the data within that window we move on to the next peer.
However i dont see why it is NOT in my interest to ALWAYS forward everything to all the peers at least in the row.
If we receive no delivery within the lifecycle of the request (it is kept alive by the live timeouts of the incoming requests for the content), we consider the item nonexistent and may even keep a record of that! (has this been proposed? proof of nonexistence)
Surely we can keep forwarding the request (to new peers or repeated queries (renewals)). This can be a result of the freuency of a request or any other form of transaction fee incentive or own need.

If we get a delivery we forward to all those who are active in the request pool (in terms of being alive connected as well as interested based on their  timeout). These peers will typically be further from the target than where we are.


## Storage requests
Deliveries that are unexpected can be considered storage requests so we can simply drop this distinction once and for all (my fault in the first place).

Storage requests that appear first time in a while then we open an entry in the storage request pool we assess the key for proximity and if deemed too distant may be forgotten. If we want to keep it (which is probably 100% (we just do not forward) then we save it to persistent storage. memcaching will be triggered by what exactly? or how is forgetting determined? If the key is found in the database, its expiry may be updated.
Storage requests are forwarded to the peers in the same cademlia proximity bin. If we are sufficiently close, the bin might include peers more or less distant from the peer than we are.

If the pool is marked as saved then the message is discarded. Would it not make sense to send back a storage receipt with which we can update a counter in our storage, if we got reliable estimates about how many neighbours hold an item, then this information can be used when deciding what to forget if we need to forget at all (deleting from persistent storage).

Storage and delivery becomes more intersting if we consider storing and delivering longer sequences of chunks forming a tree or some more complex chained stream.




