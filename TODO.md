# Sprint plan

# scope
- forwarding only (no recursive lookup and no connecting to new nodes, only working with active peers)

## TODO

- put Data -> Reader logic to chunker
- lazy reader timeout and reader errors over nil reader (chunk not found) the same?
- chunker tests with missing chunks
- parallelize join with benchmarks
- test localstore , fallback to db, 0 capacity stores etc
- integrate new p2p
- test protocol 
- test netstore (without protocol)
- rework protocol errors using errs after PR merged
- integrate cademlia into hive / peer pool

## CLI 
- hooking into DPA local API
- running as a daemon accepting request via socket?

## APIs
- DAPP API - js integration (Fabian, Alex)
- mist dapp storage scheme, url->hash mapping (Fabian, Alex) 

# Discuss alternatives 

I suggest we each pick 2/3 and read up on their project status, features, useability, objectives, etc 
- Is it even worth it to reinvent/reimplement the wheel?
- what features do we want now and in future
- roadmap 

# Brainstorming

- storage economy, incentivisation
- dht  - chain interaction
- proof of custody https://docs.google.com/document/d/1F81ulKEZFPIGNEVRsx0H1gl2YRtf0mUMsX011BzSjnY/edit
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

