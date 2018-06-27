#  Proof-of-Burn Storage and Syncing Incentivization

## Introduction

In Swarm, uploading new data incurs bandwidth and storage costs, whereas syncing, that is moving chunks to nodes to which they 
are closer, is necessary for keeping data in Swarm retrievable. Thus, the former needs to be disincentivized in a manner that 
does not disincentivize the latter. In particular, merely demanding Swap payments for accepting chunks is not incentive compatible 
with the above objectives, as it would make forwarding chunks to closer nodes costlier than not forwarding.

Furthermore, the finite storage capacity of Swarm necessitates some rent-like payment for keeping a chunk in Swarm that is its 
storage by the closest nodes and its syncing whenever changes in the structure of the network require it. Thus, from an uploader's 
perspective, the costs need to be proportional to both the amount of chunks they upload and the amount of time for which they 
expect them to be retrievable.

In this document, a simple construction for this problem is proposed that would both disincentivize spam and provide nodes with 
an incentive to store and forward chunks for which their respective uploader has paid as well as establishing a pricing mechanism 
for a fair and efficient pricing of storage.

## Preliminaries

The solution is modeled after [international mail](https://en.wikipedia.org/wiki/Treaty_of_Bern). Suppose, each chunk is uploaded 
with a kind of "postage stamp" (technically, a digital signature) attached, proving that its delivery all the way to its 
neighborhood in the network has been prepaid. Thus, the forwarding of stamped chunks becomes a service similar to retrieving 
chunks and can thus be included in Swap accounting. Even if nobody eventually receives the Ethers paid for the stamp, the demand 
and acceptance of stamped chunks becomes a [Nash equilibrium](https://en.wikipedia.org/wiki/Nash_equilibrium) similar to Proof-
of-Work cryptocurrencies, where also nobody receives the resources spent on mining.

Since the actual service provided by the network is not the one-time delivery of the chunk to its neighborhood, but storage for 
a certain amount of time, unlike postal stamps, Swarm stamps have a validity time period. Their spot value is determined by
their Ether amount divided by the length of their validity period. Thus, when either bandwidth or storage resources become 
scarce and some chunks need to be delayed or discarded, a chunk with a stamp that has half the Ether amount but one third of 
the validity period will be prioritized over another chunk with twice its Ether amount but thrice its validity period.

## Implementation Guidelines

Stamps are created in batches: a transaction is sent to a contract identifying the public key of the stamp and indicating the 
validity period. The amount of ethers sent in the transaction is divided between all the stamps created with the corresponding 
private key. Even though nodes do not have a global overview of the network and thus cannot count the total number of times 
a particular private key was used to sign chunks, turns out they can compare these numbers for two signatures: assuming a 
uniform distribution of chunk hashes, the number of chunks reaching a particular node is directly proportional to their overall 
number and inversely proportional with their Kademlia distance (defined as the XOR of the node address and the chunk hash).
Even though uploaders can manipulate the distribution of chunk hashes, any deviation from the uniform distribution would 
actually decrease the average estimated value of these chunks due to 
[Jensen's inequality](https://en.wikipedia.org/wiki/Jensen%27s_inequality#Finite_form).

Multiple stamps on the same chunk are allowed, as long as all of them are valid. Their spot value estimates are simply 
arithmetically added. Re-uploads of the same chunk with a different stamp results in the chunk carrying both stamps.

In order to save on the need to re-upload the same chunks with a different stamps when their current stamp either expires or 
becomes insufficient, additional "top-up" transactions can be defined. The increase of the Ether value of the stamps is only 
meaningful for their remaining validity time. Thus, before adding the amount paid to the stamps' value, they undergo the 
following -- value-neutral -- transformation: their validity period is replaced by the remaining validity period whereas their 
value is multiplied by the remaining period over the total period. The owner of the corresponding private key can also extend 
the validity period after the same value-neutral transformation, albeit with a decrease in stamp value, if no additional funds 
are provided. The extension of validity period without change in stamp value is fairly straightforward: if a stamp has originally 
been paid *X* for a time period *t*, a new payment of *Y* should be accompanied by an increment of *t* by *tY*/*X*. Even 
decreasing *t* with a partial refund is a valid operation by the owner, as long as the expiration date remains in the future.
Valid stamps can be invalidated with immediate effect by the owner through first applying the value-neutral transformation and 
then refunding the new amount.

Thus, Ether paid for past syncing and storage is irretrievable from the contract and can be considered burned.

## Privacy Concerns

Chunks stamped with the same signature can be suspected of originaing from the same source that signed them. This problem can be 
addressed by a blind stamping service charging a per-signaure rate for 
[blind signatures](https://en.wikipedia.org/wiki/Blind_signature#Blind_ECDSA_signatures[4]). With such a service, modifications 
doable by the stamp owner must be turned off. For users of such services, regular stamping and re-uploads are necessary.

## Notes

The proposed mechanism provides a fairly simple, yet powerful and versatile incentivization for long-term storage, while also 
protecting the network against spam. It can be combined with more sophisticated solutions of availability insurance.
