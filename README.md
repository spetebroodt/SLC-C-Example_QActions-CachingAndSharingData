# Skyline Example QActions Caching and Sharing

This protocol demonstrates how to buffer data that needs to be re-used across multiple QAction runs on a given element.

Following different use-case are demonstrated:

1. Store and Share: Remember/buffer data across multiple runs of QActions simply by storing the data into standalone and/or table parameter(s).
	This will cause some extra load due to the need to serialize and deserialize the data and the required parameter gets and sets.
	In most cases, this is sufficient as the above mentioned performance impact will be neglectable compared to other parts such as datasource access, getting and setting actual data parameters, etc.
	This is the best and easiest option in most cases.
	This is the only option allowing to store data across element restarts by saving the parameter but bare in mind this has a cost on db-usage so only save if really needed.
	This connector demonstrates how to store and consume serialized data into a single standalone parameter but depending on the use-case,
		it might be better to split the data across multiple parameters or even across table parameters which gives you the opportunity to get and set data in a granular way avoiding unnecessary hits on SLScripting memory.
2. Cache: Cache data for quick access across multiple runs of the same QAction.
	To be used when method 1 is not performant enough.
	Some logic will need to take care of refreshing the cache when needed (e.g. after startup).
3. Cache and Share: Share cached data for quick access across multiple QActions.
	Typically only required when needing to share data across QActions triggered on table parameters (one on table level, one on row level).
	A ConcurrentDictionary needs to be used to allow safe access from multiple QActions at the same time and also because multiple elements will be accessing the same ConcurrentDictionary instance.
	Some logic needs to take care of cleaning up cached data when elements are stopped/deleted to avoid a memory leak and after startup to avoid stale data across element restarts.
