# DynamoDB

## Keys

- SK on table is optional
- primary key is PK + SK
- query
  - PK is required
  - SK is optional
- put & delete
  - item based actions
  - need both PK & SK (i.e. the full primary key)

## Indexes

- local secondary index
  - same PK as table, but different SK
    - i.e. "local" on each partition
  - must be created with table

## Tips

- `ulid` lexically cronologically sortable unique id

- use `conditionExpression: "attribute_not_exists(PK)"` with PutItem to
  ensure you don't overwrite existing entry.

- when getting DynamoDB client add `httpOptions: { connectTimeout: 1000, timeout: 1000}`
  to add 1s timeout

- use `transactions` to make multiple operations in one
  request - they all succeed or fail together (with rollback)

- `UpdateExpression: "SET #likesCount = #likescCount += :inc"` increment with atomicity
  (otherwise you'll need to do a separate get first)

- `batchGetItem()` single request to query 100 items (total 16MB data). Returns list
  of unprocessed keys if limit reached.
