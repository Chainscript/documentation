# broadcast command

(todo)

## execute

Broadcast requires an "x_transaction" attachment describing the transaction to be broadcasted.

```JSON
{
  "document": {
    "content": "Hello World!"
  },
  "x_transaction": {
    "encoded_hex": "0100000001bb19a19be8b....",
    "status": "signed",
    "op_return": "9e571bae...",
    "blockchain": "bitcoin",
    "reference": "chainscript:notarization:5979cd12-961c-4a12-ba5d-58efaa3b751d"
  },
  "broadcast": {}
}
```
