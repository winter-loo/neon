The pipeline for ingesting WAL looks like this:

WAL receiver  -> wal_decoder ->  WalIngest  ->  Repository


handle_walreceiver_connection
waldecoder.poll_decode
WalIngest::ingest_record
WalIngest::ingest_xact_record
DatadirModification::put_rel_page_image
DatadirModification::put
DatadirModification::put_data
SerializedValueBatch::put


handle_walreceiver_connection
DatadirModification::commit
TimelineWriter::put_batch
InMemoryLayer::put_batch


start_background_loops
ingest_housekeeping_loop
Iteration::run
Tenant::ingest_housekeeping
Timeline::maybe_freeze_ephemeral_layer
Timline::freeze_inmem_layer_at
OpenLayerManager::try_freeze_in_memory_layer
InMemoryLayer::freeze


handle_walreceiver_connection
DatadirModification::commit
TimelineWriter::put_batch
TimelineWriter::handle_open_layer_action
TimelineWriter::roll_layer
Timline::freeze_inmem_layer_at
OpenLayerManager::try_freeze_in_memory_layer
InMemoryLayer::freeze


Tenant::load_remote_timeline
Tenant::timeline_init_and_sync
Timeline::maybe_spawn_flush_loop
Timeline::flush_loop
Timeline::flush_frozen_layer
Timeline::create_delta_layer
InMemoryLayer::write_to_disk
DeltaLayerWriter::put_value_bytes
  BlobWriter::write_blob_maybe_compressed
  DiskBtreeBuilder::append
DeltaLayerWriter::finish

> Every delta file consists of three parts: "summary", "values", and
> "index". The summary is a fixed size header at the beginning of the file,
> and it contains basic information about the layer, and offsets to the other
> parts. The "index" is a B-tree, mapping from Key and LSN to an offset in the
> "values" part.  The actual page images and WAL records are stored in the
> "values" part.


TimelineWriter::open_layer
Timeline::get_layer_for_write
OpenLayerManager::get_layer_for_write
InMemoryLayer::create
