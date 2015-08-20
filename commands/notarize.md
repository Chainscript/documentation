# Notarize (notarize)

The notarize command timestamps a document on a specific blockchain.  A digest of the document is used to stamp the exact contents of the document.  A unique resource name is used to identifiy the blockchain and transaction hash as the evidence.

## Execute

```JSON
{
  "document": "My unique text",
  "notarize": {}
}
```

## Results

After executing the notarize command a transaction and notarization is added to x_chainscript.  The transaction records information, included as OP_RETURN, about the transaction which timestamps the document.  

The actual notarization is used to record the evidence (blockchain & transaction hash).

```JSON
{
  "document": {
    "content": "My unique text",
    "x_meta": {
      "uuid": "chainscript:document:60e52ac9-0a83-4be1-bee0-418a993d60a8"
    }
  },
  "x_chainscript": {
    "validation": {
      "agent": "agent.chainscript.io",
      "version": "alpha.0.1",
      "result": "success",
      "validated_on": "2015-08-20T16:56:51+00:00"
    },
    "digest": "8d415efa211e8fb24f5d9fc2ad8c32aaa33d647d",
    "transactions": {
      "0": {
        "status": "broadcasted",
        "op_return": "8d415efa211e8fb24f5d9fc2ad8c32aaa33d647d",
        "blockchain": "debug",
        "reference": "chainscript:notarization:64d2ec92-c3ad-4e4e-a96b-95ec569a4e0d",
        "uuid": "chainscript:debug:tx:000000000000000000000000000000000000000000000000000000000000000",
        "broadcasted_on": "2015-08-20T16:56:51+00:00"
      }
    },
    "notarizations": {
      "chainscript:notarization:64d2ec92-c3ad-4e4e-a96b-95ec569a4e0d": {
        "digest": "8d415efa211e8fb24f5d9fc2ad8c32aaa33d647d",
        "evidence": "chainscript:debug:tx:000000000000000000000000000000000000000000000000000000000000000",
        "notarized_at": "2015-08-20T16:56:51+00:00"
      }
    },
    "notarized": true
  }
}
```

## Validation 

When validating a document containing one or more notarizations, the digest of the current document is compared.  If a notarization is found with the same digest and the corresponding blockchain transaction has been confirmed, one can confirm the timestamp of the contents.
