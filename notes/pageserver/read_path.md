handle_pagerequests
handle_pagerequests_serial
PageServerHandler::pagestream_read_message
    ```
    let key = rel_block_to_key(rel, blkno);
    timeline_handles.get(tenant_id, timeline_id, ShardSelector::Page(key))
      -> timeline::handle::Handle<TenantManagerTypes>
    PageServerHandler::wait_or_get_last_lsn
      -> Lsn
    BatchedFeMessage::GetPage {
        span,
        shard,
        effective_request_lsn,
        pages: smallvec::smallvec![(rel, blkno, timer)],
    }
    ```
pagesteam_handle_batched_message
PageServerHandler::handle_get_page_at_lsn_request_batched
  -> response_msg = PagestreamBeMessage
pgb_writer.write_message_noflush(&BeMessage::CopyData(&response_msg.serialize()))?;
pgb_writer.flush()
  pgb_writer: PostgresBackend



PageServerHandler::handle_get_page_at_lsn_request_batched
Timeline::get_rel_page_at_lsn_batched
Timeline::get_vectored
Timeline::get_vectored_impl
Timeline::get_vectored_reconstruct_data
  Timeline::get_vectored_reconstruct_data_timeline
Timeline::reconstruct_value // Reconstruct a value, using the given base image and WAL records
  WalRedoManager::request_redo

Test case:

* setup

```
create table t(a int);
insert into t select * from generate_series(1, 1000);
```

* show getpage request

filter pageserver log: `tail -f pageserver.log | grep 'get vectored request'`

```
select pg_current_wal_lsn();
select * from t where a % 10 = 0;
```


A LayerFringe manages the set of layers that intersect the current keyspace
during a read operation. It ensures layers are processed in the correct order.

```
Timeline::get_vectored_reconstruct_data_timeline
layers.find_in_memory_layer
-> fringe.update
-> layers.range_search // ***
   fringe.update
fringe.next_layer
ReadableLayer.get_values_reconstruct_data
```

```
fringe.update
fringe.next_layer
ReadableLayer::get_values_reconstruct_data
  -> Layer::get_values_reconstruct_data
  -> InMemoryLayer::get_values_reconstruct_data
```

Two kinds of layers: on-disk Layer and in-memory Layer

For ok-disk Layer:

```
Layer::get_values_reconstruct_data
LayerInner::get_or_maybe_download
DownloadedLayer::get_values_reconstruct_data
DownloadedLayer::get
    -> DeltaLayerInner::get_values_reconstruct_data
    DeltaLayerInner::plan_reads
    DeltaLayerInner::do_reads_and_update_state
    VectoredBlobReader::read_blobs
    reconstruct_state.update_key
    -> ImageLayerInner::get_values_reconstruct_data
    ImageLayerInner::plan_reads
    ImageLayerInner::do_reads_and_update_state
```

On-disk layer has two kinds of layers: delta layer and image layer

For in-memory layer:

```
InMemoryLayer::get_values_reconstruct_data
vectored_dio_read::execute
reconstruct_state.update_key
reconstruct_state.on_lsn_advanced
```
