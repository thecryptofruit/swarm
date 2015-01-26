# Sprint plan

## code status
- merge bzz-fefe into bzz
- tests for memstore and db store should run again

## chunker 
- benchmark for other than binary trees
- parallelise join 

## DPA
- reconsider homogeneity of chunk stores: mem, disk/db, and dht are fundamentally different, DPA should know about these individually not just a random chain of stores
- API for local storage requests by client (self)

## dht store
- https://github.com/ethersphere/swarm/blob/master/doc/forwarding.md
- implement simple request pool 
- forwarding strategy, timeout estimation

## peer selection, routing table
- migrate all from kademlia branch
- redo cademlia as swarm peer pool

## CLI 
- hooking into DPA local API
- running as a daemon accepting request via socket?

# Discuss alternatives 

I suggest we each pick 2/3 and read up on their project status, features, useability, objectives, etc
- Is it even worth it to reinvent/reimplement the wheel?
- what features do we want now and in future
- roadmap 

# Brainstorming

- storage economy, incentivisation
- nonoutsourceable proofs of storage as mining criteria 
- proof of storage capacity directly rewarded by contract
- streaming, hash chains 
- routing and learning graph traversal
- minimising hops
- forwarding strategies, optimising dispersion of requests 
- lifetime of requests, renewals (repeated retrieval requests), expiry, reposting (repeated storage request)

# Simulations

- full table homogeneous nodes network size vs density vs table size expected row-sizes 
- forwarding strategy vs latency vs traffic
- stable table, dropout rate vs routing optimisation by precalculating subtables for all peers. expected distance change (proximity delta) per hop


## Swarm

How far does the analogy go?
    
swarm of bees | a decentralised network of peers
-------|------------
living in a hive | form a distributed preimage archive
where they | where they
gather pollen | gather data chunks which they 
to produce honey | transform into a longer data stream (document)
they consume and store |  they serve and store  
buzzing bzz | using bzz as their communications protocol

