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

The latter can be either a response to the latter, or constituting a request
to store that chunk. Both the sender and the recipient node know which is
which.

Responding to a request and accepting a request to store is a service 
rendered. Thus, it is sufficient to account for chunk payload messeges, 
but wheter it is the sender or the recipient that is rendering the 
valuable service to the other party depends on whether it is a store 
request or a response to a lookup.


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

Store requests are worth storing, because of the possibility that this
information may be profitably "sold" by serving lookups in the future. Note,
however, that the risk of them never being requested is quite high, especially
if the price for the service is low enough for people to push information
that they do not want to retrieve in the future. This is why store requests
need to be expensive.

In general, it is expected that a certain chunk is going to be looked up
much more often than it is stored. From a certain node's perspective, the
probability of it being ever requested from this particular node is
proportional to the inverse of its distance from it, which, in turn,
determines the risk of it not being requested. Hence, the rational pricing
for store requests should increase in proportion to the distance from the
chunk key.


## Why would anyone forward store requests?

_Requires further work_

Forwarding store requests costs as much as originating them and half as
much as can be earned by receiving them from nodes that want it forwarded.
However, it still costs more than doing nothing, so it is not
immediately clear that it is worth doing.

What forwarding actually accomplishes is that it transfers the chunk to
a node that is twice as likely to be queried for it. Thus, there is value to
be created from the transfer, but it is not clear how much. Such situations
are commonly solved by revenue sharing arrangements (see DARPA's Red Balloon
Challenge), but in this case, detecting cheating needs to be solved.

## References

1. http://en.wikipedia.org/wiki/DARPA_Network_Challenge
