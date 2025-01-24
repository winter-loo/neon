The DeletionQueue is a multi-stage queue system in the Neon pageserver that
handles deletion operations, particularly for remote storage objects. Here's
how it works:

1. Structure and Components:

- It consists of multiple stages connected by channels
- Has a client interface (`DeletionQueueClient`)
- Contains three main workers:
    - `ListWriter` (frontend)
    - `Validator`
    - `Deleter` (executor)

2. Key Features:

- Handles deletion of layer files and metadata
- Coalesces multiple object deletes into larger DeleteObjects requests
- Validates operations against the control plane
- Includes metrics tracking for:
    - Submitted keys
    - Dropped keys
    - Executed keys
    - Validated keys
    - Errors and remote errors

3. Operation Flow:

- Frontend writes DeletionLists to disk
- Validator checks operations for validity
- Deleter executes the actual deletion operations
- Uses auto-flush mechanisms with configurable intervals (10 seconds) and batch sizes

4. Crash Consistency:

- Maintains persistent state on disk through DeletionLists
- Integrates with the pageserver's generation-based consistency model
- Handles cleanup of remote storage objects safely
