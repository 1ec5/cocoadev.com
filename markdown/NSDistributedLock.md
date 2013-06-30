General/NSDistributedLock can be used to to restrict access to a shared resource, such as a file.

----
Why does General/NSDistributedLock use a directory instead of a file? (I know this implementation detail isn't documented, but in practice both Foundation and General/GNUStep use a directory.)
----

My guess would be that it's more efficient to create a directory node in the file system than a file, since a directory requires no actual space allocating to it. But I'm only guessing. --GC