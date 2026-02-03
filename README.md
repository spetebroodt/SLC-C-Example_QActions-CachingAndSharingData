# Skyline Example - QActions Caching and Sharing

## About

This connector demonstrates how to buffer data that needs to be re-used across multiple QAction runs on a given element.

> [!NOTE]
> This only covers sharing data accross QAction runs **within your own element**. For data sharing accross different element, see the [InterApp framework](https://aka.dataminer.services/InterApp).

## Use Cases

### Store and share using parameters

Remember/buffer data across **multiple QActions** simply by storing the data into standalone and/or table parameter(s).

This will cause some extra load due to the need to serialize and deserialize the data and the required parameter gets and sets. In most cases, this is sufficient as the above mentioned performance impact will be neglectable compared to other parts such as datasource access, getting and setting actual data parameters, etc.

This is the best and easiest option in most cases. This connector demonstrates how to store and consume serialized data into a single standalone parameter but depending on the use-case, it might be better to split the data across multiple parameters or even across table parameters which gives you the opportunity to get and set data in a granular way avoiding unnecessary hits on SLScripting memory.

This is the only option allowing to store data across element restarts by saving the parameter but important to bare in mind this can have a significant impact on storage-related costs so only save if really needed.

### Cache data for quick access across multiple runs of the same QAction

Caching data within SLScripting memory can be used when [Store and share using parameters](xref:#store-and-share-using-parameters) method is not performant enough.

Some logic will need to take care of refreshing the cache when needed (e.g. after startup).
	
### Cache and share data for quick access across multiple QActions

This method is typically only required when needing to share data across QActions triggered on table parameters (one on table level, one on row level).

This requires the use of a static field within a static class which means the field will be shared accross all elements using the same connector and running on the same SLScripting process. As a consequence, A ```ConcurrentDictionary``` needs to be used in order to

- Make sure cache from different elements won't interfere with each other. This can be done by including both **Root Dma ID** and **Element ID** to the key of the ```ConcurrentDictionary```.
- Allow thread-safe access from multiple QActions.

Additionally, some logic needs to take care of cleaning up cached data when elements are stopped/deleted to avoid a memory leak and after startup to avoid stale data across element restarts.
