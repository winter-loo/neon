
The pageserver is a core component of Neon that handles page storage and retrieval.
Here's a breakdown of its key functionality:

Services:

- PostgreSQL protocol listener (listen_pg_addr) for handling page requests
- HTTP server (listen_http_addr) for management APIs
- Background tasks managed by task_mgr for operations like eviction and uploads

Key modules:

1. Tenant management:

    - Storage layers (delta_layer and image_layer)
    - Layer mapping and coverage tracking
    - Remote timeline client for cloud storage integration

2. Storage:
    
    - Virtual file system abstraction
    - Page cache management
    - Basebackup functionality for timeline snapshots

3. Control and configuration:
    
    - Controller upcall client for generation management
    - Configuration handling via PageServerConf
    - Authentication and authorization

4. Task Management:
    
    - Multiple tokio runtime support (4 runtimes)
    - Task tracking and cancellation
    - Request context management

The pageserver provides critical functionality for:

- Page storage and retrieval
- Timeline management
- Cloud storage integration
- Tenant isolation
- Backup and restore operations
- Cache management
- Access control

It uses a sophisticated layer system with both delta and image layers to
efficiently store and manage page versions, supporting both local and remote
storage operations.
