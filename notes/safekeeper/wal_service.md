## send wal to pageserver

```rust
let wal_service_handle = current_thread_rt
    .as_ref()
    .unwrap_or_else(|| WAL_SERVICE_RUNTIME.handle())
    .spawn(wal_service::task_main(
        conf.clone(),
        pg_listener,
        Scope::SafekeeperData,
        global_timelines.clone(),
    ))
    // wrap with task name for error reporting
    .map(|res| ("WAL service main".to_owned(), res));
```

```
task_main
listener.accept // wait for client connection
handle_socket // handle the client connection
PostgresBackend::run
PostgresBackend::run_message_loop
PostgresBackend::process_message
SafekeeperPostgresHandler::process_query // received simple query message: "START_REPLICATION PHYSICAL 0/1518F48"
handle_start_replication
WalSender::run // run loop
	wait_wal
	WalReader::read
	PostgresBackend::write_message
```


## receive wal from compute node

```
handle_append_request
PhysicalStorage::write_wal
```

file: .neon/safekeepers/sk1/<tenant id>/<timeline id>/<xlog filename>.partial
