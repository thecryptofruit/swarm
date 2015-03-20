# A simple and robust incentive system for Swarm

## Goals

The objective of an incentive system is most generally to encourage
cooperative behavior and discourage freeriding: the uncompensated
depletion of limited resources. In the context of swarm, storage and
bandwidth are the two most important limited resources. It is not
immediately obvious, however, what actions benefit whom to what extent.

In this proposal, a very simple strategy is outlined that
satisfies the following constraints:

- It is in the node's interest irrespective of whether other nodes 
  follow it or not.

- It makes it expensive to hog other nodes' resources.

- It does not impose unreasonable overhead.

- It plays nice with "naive" nodes

- It rewards those that play nice, including those following this strategy


## Preliminaries

There are two important messages in swarm:

1. Request the preimage of a hash
2. Chunk payload
3. Acknowledge the receipt of payload

Chunk payload can be either a response to a previous request or constituting
a request to store that chunk. Both the sender and the recipient node know which is
which.

Responding to a request and accepting a request to store is a service 
rendered. Thus, it is sufficient to account for chunk payload messeges, 
but wheter it is the sender or the recipient that is rendering the 
valuable service to the other party depends on whether it is a store 
request or a response to a lookup.

If a payload message is a request to store the chunk, it must be acknowledged by
a signed receipt. These receipts are used to enforce penalties for loss of content
through a special Swarm Contract on the block chain.

## Accounting

The proposal is to simply account on both ends of each direct network 
connection for these two types of traffic, with store request traffic
being possibly more expensive than lookup service traffic, accounting for
the higher cost it imposes on the network (to be discussed later).

When the mutual balance on a given connection is too heavily tilted in 
favor of the peer, the peer should be compensated with a direct transfer 
of payment tokens. Whether ETH or a special-purpose token is the best 
solution depends on many considerations, discussed later. If the balance 
tilts heavily in the other direction, the peer should be throttled and 
eventually choked and disconnected. Receiving of payment tokens should 
be accounted for equivalent service rendered. Note that it is not 
necessary for both ends to agree on prices in order to successfully 
cooperate.


## Why would anyone store chunks?

Lookup results are worth caching, because repeated requests to the same
chunk can be served from cache, without the need to "purchase" the chunk
again.

One reason store requests are worth storing, because of the possibility that this
information may be profitably "sold" by serving lookups in the future and because
the whole swarm losing the chunk makes nodes that have received it liable through
Swarm Contract.

In general, it is expected that a certain chunk is going to be looked up
much more often than it is stored. From a certain node's perspective, the
probability of it being ever requested from this particular node is
proportional to the inverse of its distance from it, which, in turn,
determines the risk of it not being requested. Hence, the rational pricing
for store requests should increase in proportion to the distance from the
chunk key.

## Why would anyone forward store requests?

Forwarding store requests costs as much as originating them and half as
much as can be earned by receiving them from nodes that want it forwarded.
However, it still costs more than doing nothing, so it is not
immediately clear that it is worth doing.

What forwarding actually accomplishes is that it transfers the chunk to
a node that is twice as likely to be queried for it. When a node's storage
is filled to capacity, it can still accept new chunks, using the rest of Swarm
as a backup storage of less profitable chunks.

Note, furthermore, that if a chunk does not reach the nearest Swarm nodes before
being requested, the chances of it being reported as lost increase, which poses a burden
on all Swarm nodes that have ever issued receipts for it. They can avoid this by
timely forwarding. It can be further encouraged by offering discounts for Swarm
participants that themselves committed to storing that chunk.

# References

1. https://github.com/ethereum/go-ethereum/wiki/Swarm-Contract
