# Overview

From [architecture decisions]:

> In Neon, incoming WAL is processed as it arrives, and indexed and buffered in
> memory. When the buffer fills up, it is written to a new file. Files are never
> modified in place. In the background, old data is reorganized by merging and
> deleting old files, to keep read latency in check and to garbage collect old
> page versions that are no longer needed for PITR. This design was inspired by
> Log-Structured Merge-trees.

From [storage engine under the hood]:

> * Just like LSM trees, the storage is non-overwriting. Files are created and
>   deleted, but they are never updated in place. Immutable files are important
>   to integrate with cloud storage, such as S3, which don’t allow random updates.
> * The incoming WAL stream is first buffered and organized in memory. When 1GB
>   of WAL has accumulated, it is written out to a new layer file.
> * Old files are merged and compacted by creating new files and deleting old ones.


[architecture decisions]: https://neon.tech/blog/architecture-decisions-in-neon
[storage engine under the hood]: https://neon.tech/blog/get-page-at-lsn#storage-engine-under-the-hood


# Write path

From [write path]:

> The safekeeper nodes then stream the logs to the pageservers, where the logs
> are processed, reorganized, and written to immutable files. Finally, these
> files are uploaded to cloud storage for long-term storage.

> The storage system processes incoming WAL logs, reorganizes them, and writes
> it out to layer files.

more details in [write_path](./write_path.md)

> As new data is added to the system, it is organized into layer files. We have
> two types of layers: delta and image layers.
> * Image layer contains a snapshot of all key-value pairs in a key-range at one
>   LSN. Image layers are created in the background to speed up access and to
>   allow garbage collecting old data.
> * Delta layer contains all changes in a key and LSN range. If a key wasn’t
>   modified, it’s not stored. Incoming WAL is written out as delta layers.


[write path]: https://neon.tech/blog/get-page-at-lsn#write-path

# Read path

From [read path]:

> Every read request from a compute node to the pageserver includes a log sequence
> number (LSN), and the pageserver returns the data as it was at the requested LSN.
> When a read request comes in, the pageserver finds the last image of the page
> by the LSN, and any write-ahead-log (WAL) records on top of it, applies the WAL
> records if needed to reconstruct the page, and returns the materialized page to
> the caller. We call this function GetPage@LSN.

[read path]: https://neon.tech/blog/get-page-at-lsn#read-path
