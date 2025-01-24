default work directory: .neon
configuration files: pageserver.toml, identity.toml

if have SENTRY_DSN env var, then sent panic information to sentry.io platform.

tenant path: .neon/tenants

use virtual_file module to manage file descriptors.

initialize page cache.

listen at `listen_http_addr` and `listen_pg_addr`.

connect to [storage broker](./storage_broker.md)

set a generic remote storage(GenericRemoteStorage), such as LocalFs.

set up [deletion queue](./deletion_queue.md).

Scan the local 'tenants/' directory and start loading the tenants(init_tenant_mgr)

remote_layer_path: `.neon/tenants/cb97d9bfcd6a1df56ae74be95164e7a1/timelines/cb97d9bfcd6a1df56ae74be95164e7a1/000000000000000000000000000000000000-FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF__00000000014F7390-00000000014F7409-v1-00000001`

000000000000000000000000000000000000-FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF__00000000014F7390-00000000014F7409
<key start>-<key end>__<start LSN>-<end LSN>

getpage_at_lsn_handler
getpage request: http://127.0.0.1:9898/v1/tenant/cb97d9bfcd6a1df56ae74be95164e7a1/timeline/4161ed79104ba2bbc35ec84faf555315/getpage?key=000000000000000000000000000000000000&lsn=00000000014F7390

deletion_list_path: `.neon/deletion/<sequence>-01.list`
tmp deletion_list_path: `.neon/deletion/<sequence>-01.list.tmp`

```text
init_tenant_mgr
  Tenant::spawn
  task_mgr::spawn {
    Tenant::attach
    Tenant::load_remote_timeline
    Tenant::activate
  }
```

BACKGROUND_RUNTIME.spawn(drive_init)


1. **Layers**: In a storage system, data can be organized into layers,
which may represent different versions or segments of data. Each layer can
contain a set of files or data blocks.

launch_disk_usage_global_eviction_task

launch_disk_usage_global_eviction_task

HttpEndpointListener

```text
let router = http::make_router
let service = utils::http::RouterService::new(router)
let server = hyper0::Server::from_tcp(http_listener)?.serve(service);
```

pageserver::consumption_metrics::run

ConsumptionMetricsTasks

page_service::spawn {
  BACKGROUND_RUNTIME.block_on {
    libpq_listener_main
    page_service_conn_main {
        PageServerHandler::new
        pgbackend = PostgresBackend::new_from_io
        pgbackend.run
            self.run_message_loop
            self.process_message
            handler.process_query // PageServerHandler
            self.handle_pagerequests
                -> self.handle_pagerequests_pipelined
                -> self.handle_pagerequests_serial
                    Self::pagestream_read_message
                    pagesteam_handle_batched_message
                        handle_get_rel_exists_request
                        handle_get_nblocks_request
                        handle_get_page_at_lsn_request_batched
                        handle_db_size_request
                        handle_get_slru_segment_request
                    pgb_writer.write_message_noflush(&BeMessage::CopyData(&response_msg.serialize()))?;
    }
  }
}

BACKGROUND_RUNTIME.block_on {
    // cleanup and shutdown
}
