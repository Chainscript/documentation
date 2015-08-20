# Broadcast (broadcast)

The broadcast command pushes transactions to the blockchain network.  Transactions are included as an envelope attachment named x_transaction.  A signed raw blockchain transaction, encoded in hex, is required.  The status of x_transaction should be set to 'signed'

## Executing

```JSON
{
  "document": {
    "content": "Hello World!"
  },
  "x_transaction": {
    "encoded_hex": "0100000001bb19a19be8b....",
    "status": "signed",
    "blockchain": "bitcoin",
    "reference": "chainscript:notarization:5979cd12-961c-4a12-ba5d-58efaa3b751d"
  },
  "broadcast": {}
}
```
## Blockchain

The blockchain specified should match the encoded transaction.

## Options

If x_chainscript/debug is set to true, the command will execute but not actually push the transaction to the network.

An optional reference can be added to the transaction.
