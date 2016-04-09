Chunker is the interface to a component that is responsible for disassembling and assembling larger data and indended to be the dependency of a DPA storage system with fixed maximum chunksize.
It relies on the underlying chunking model.
When calling Split, the caller provides a channel (chan *Chunk) on which it receives chunks to store. The DPA delegates to storage layers (implementing ChunkStore interface). NewChunkstore(DB) is a convenience wrapper with which all DBs (conforming to DB interface) can serve as ChunkStores. See chunkStore.

After getting notified that all the data has been split (the error channel is closed), the caller can safely read or save the root key. Optionally it times out if not all chunks get stored or not the entire stream of data has been processed. By inspecting the errc channel the caller can check if any explicit errors (typically IO read/write failures) occured during splitting.

When calling Join with a root key, the caller gets returned a lazy reader. The caller again provides a channel and receives an error channel. The chunk channel is the one on which the caller receives placeholder chunks with missing data. The DPA is supposed to forward this to the chunk stores and notify the chunker if the data has been delivered (i.e. retrieved from memory cache, disk-persisted db or cloud based swarm delivery). The chunker then puts these together and notifies the DPA if data has been assembled by a closed error channel.

The chunker works in a simple way, it builds a tree out of the document so that each node either represents a chunk of real data or a chunk of data representing an branching non-leaf node of the tree. In particular each such non-leaf chunk will represent is a concatenation of the hash of its respective children. This scheme simultaneously guarantees data integrity as well as self addressing. Abstract nodes are transparent since their represented size component is strictly greater than their maximum data size, since they encode a subtree. 

In fact the DPA will never know when presented with a key whether that key is an intermediate node that happens to be a child of another node, it is retrieved the same way. 
This allows us to do full subtree retrieval for chunk requests and that means that there is potential for acceleration of document retrieval over the scheme where only the chunks hashing to our address space are kept. Storage requests can be sent so that a subtree is rooted using the address of its root node, towards the peers to which the root would be routed. The peer can accept and download the request and store it. Note that it is guaranteed that anyone requesting the roothash will likely want the entire subtree. So this presents an incentive to diverge from a fixed blockchunk.

It is possible to implement chunking by simply composing readers so that no extra allocation or buffering is necessary for the data splitting. This means that in principle there can be direct IO between : memory, file system, network socket (bzz peers storage request is read from the socket ). In practice there may be need for several stages of internal buffering.
Unfortunately the hashing itself does use extra copies and allocates though since it does need it.



 