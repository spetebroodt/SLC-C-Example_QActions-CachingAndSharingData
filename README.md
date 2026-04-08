# Skyline Example - QActions Caching and Sharing

## About

This connector demonstrates how to buffer data that needs to be re-used across multiple QAction runs on a given element.

> [!NOTE]
> This connector only shares data across QAction runs **within your own element**. For data sharing across different elements, see [About the InterApp framework](https://aka.dataminer.services/InterApp).

## Use Cases

### Store and share using parameters

Remember/buffer data across **multiple QActions** by storing the data in standalone and/or table parameters.

This will cause some extra load due to the need to serialize and deserialize the data and the required parameter gets and sets. In most cases, this is OK as the above-mentioned performance impact will be negligible compared to that of other operations, such as accessing data sources, getting and setting actual data parameters, etc.

This is the best and easiest option in most cases. This connector demonstrates how to store and consume serialized data in a single standalone parameter, but depending on the use case, it might be better to split the data across multiple parameters or even across table parameters. This will give you the opportunity to get and set data in a granular way, avoiding unnecessary hits on SLScripting memory.

This is the only option that allows storing data across element restarts by saving the parameter. However, bear in mind that this can have a significant impact on storage-related costs, so only save if it is really needed.

### Cache data for quick access across multiple runs of the same QAction

Caching data within SLScripting memory can be used when the above-mentioned [Store and share using parameters](#store-and-share-using-parameters) method lacks in performance.

Some logic will need to take care of refreshing the cache when needed (e.g., after startup).

### Cache and share data for quick access across multiple QActions

This method is typically only required when you need to share data across QActions that are triggered on table parameters (one on table level, one on row level).

This requires the use of a static field within a static class. This means that the field will be shared across all elements using the same connector and running in the same SLScripting process. As a consequence, a `ConcurrentDictionary` will need to be used

- to make sure the different element caches do not interfere with each other (by including both **Root DMA ID** and **Element ID** to the key of the `ConcurrentDictionary`), and
- to allow thread-safe access from multiple QActions.

For more information regarding QAction (static) fields, see [QAction member fields](https://aka.dataminer.services/QActionMemberFields).

Additionally, some logic needs to take care of cleaning up cached data

- when the element is stopped/deleted to avoid a memory leak, and
- after startup to prevent stale data across element restarts.
