
[layer map data structure](https://excalidraw.com/#json=89cXQ_yp9A9BQEkI4qHug,rfvmFt8O-jw8JhmNpOxs4g)

From https://neon.tech/blog/persistent-structures-in-neons-wal-indexing

> If the first layer encountered is an image layer, we can simply read out the
> value for the given key from the image layer and return it. But if we hit a
> delta layer, we need to collect the updates it contains, and continue searching
> below until we hit an image layer. At that point, we have a tuple
> (Value, Vec<Update>), and we can use the Write-Ahead Log (WAL) redo process to
> reconstruct the requested data.


