# Simple Queue Service (SQS)

## Basics
- Fast, reliable, scalable, and fully managed message queuing service
- Essentially a buffer between the application components that receive and process data
- Prevents work from being lost due to insufficient resources
- Ensures delivery of message at least once
- Supports multiple readers and writers with the same Queue
- Systems should be designed to be `idempotent`
- FIFO delivery not guaranteed

## Message Lifecycle
- Messages remain in the queue until they are completed or time out
- Messages are deleted to prevent messages from being re-read after the visibility timeout expires

## Delay Queues and Visibility Timeouts
- Delay Queues allow postponing the delivery of new messages in a queue for a specific number of seconds
- Delay queues are similar to visibility timeouts, both make messages unavailable to consumers for a specific period of time
- The difference is that delay queues hide the message when it first appears in the queue and visibility timeouts hide the message after it is received
- Up to 120k messages can be in flight, not delayed nor in a visibility timeout

### Queue Operations, Unique IDs, and Metadata
- Defined operations for SQS
  - `CreateQueue`
  - `ListQueues`
  - `DeleteQueue`
  - `SendMessage`
  - `SendMessageBatch`
  - `ReceiveMessage`
  - `DeleteMessage`
  - `DeleteMessageBatch`
  - `PurgeQueue`
  - `ChangeMessageVisibility`
  - `ChangeMessageVisibilityBatch`
  - `SetQueueAttributes`
  - `GetQueueAttributes`
  - `GetQueueUrl`
  - `ListDeadLetterSourceQueues`
  - `SetQueueAttributes`
  - `AddPermission`
  - `RemovePermission`
- Only the account owner or AWS identity that has the proper permissions can perform operations
- Messages are identified via a globally unique ID

### Queue and Message Identifiers
- Three Identifiers
  - Queue URLs - includes the unique queue name and other data SQS inserts, must be provided to perform actions
  - Message IDs - gets returned in `SendMessage`, max length of 100 characters
  - Receipt handles, - received each time a message is received, used to delete a message or change visibility

### Message Attributes
- Allow for structured metadata in messages
- Optional and separate from the message body
- May have up to 10 attributes

### Long Polling
- Prevents constant calls to `ReceiveMessage` that burn CPU cycles and tie up a thread when a SQS client is a loop
- Long polling uses `WaitTimeSeconds` in `ReceiveMessage` (for up to 20 seconds) to prevent the CPU cycle burn by waiting for the wait time for a message to appear, which will be immediately returned
- Drastically reduces load on client

### Dead Letter Queues
- Helps sideline and isolate unsuccessfully processed messages
- Functions like a standard queue

### Access Control
- Done via IAM and SQS Access Control
