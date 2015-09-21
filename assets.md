# Digital Assets

## Issue an Asset

Assets can be easily created using the asset_create command.  
Assets are created using the Colored Coins protocol but others will be implemented.

"address" is the only required field.

"type" can be of the following:

Possible asset types:
- "asset/item" : asset represents a single physical item, can have a quantity of same type
- "asset/shares" : an asset that is divisible (company stock, pct ownership, land, etc)
- "asset/tokens" : an asset that can be used for votes, alt-currency, etc

```JSON
{
  "content": "My First Digital Asset",
  "asset_create": {
    "address": "mxWmN9fq7jz5wktqVDoJAMuHYeqtdYirhn",
    "type": "asset/item",
    "quantity": 1,
    "protocol": "testnet3/coloredcoins"
  },
  "x_chainscript": {
    "disable_command_log": true,
    "disable_revision_history": true
  }
}
```

The chainscript agent will return:

```JSON
{
  "body": {
    "content": "My First Digital Asset",
    "x_attachments": {
      "asset": {
         "uuid" : "chainscript:asset:coloredcoins:LD8cVe1Ms62QcRMd8kZngpoS8atgbrLWTg3NB",
         "address": "mxWmN9fq7jz5wktqVDoJAMuHYeqtdYirhn",
         "type": "asset/item",
         "protocol": "testnet3/coloredcoins"
      }
   },
   "x_transaction": {
      "encoded_hex": "0100000001881aba7004ad2ba5...",
      "status": "unsigned",
      "blockchain": "testnet3",
      "asset": "chainscript:asset:coloredcoins:LD8cVe1Ms62QcRMd8kZngpoS8atgbrLWTg3NB"
   }
}
```

The client will then need to sign the encoded transaction with 'bitcoinjs-lib'.  The result will then be sent using the 'broadcast' command:

```JSON
{
  "body": {...},
  "broadcast": {},
  "x_transaction": {
      "encoded_hex": "0100000001881aba7004ad2ba5...",
      "status": "signed",
      "blockchain": "testnet3",
      "asset": "chainscript:asset:coloredcoins:LD8cVe1Ms62QcRMd8kZngpoS8atgbrLWTg3NB"
   }
}
```

After the broadcast command the result:

```JSON
{
  "body": {
    "content": "My First Digital Asset",
    "x_attachments": {
      "asset": {
         "uuid" : "chainscript:asset:coloredcoins:LD8cVe1Ms62QcRMd8kZngpoS8atgbrLWTg3NB",
          "address": "mxWmN9fq7jz5wktqVDoJAMuHYeqtdYirhn",
          "type": "asset/item",
          "protocol": "testnet3/coloredcoins",
          "evidence": "chainscript:tx:testnet3:d2963888d729b22b25dfe18e67e0...",
          "holders": {
             "mxWmN9fq7jz5wktqVDoJAMuHYeqtdYirhn": 1
          }
      }
   }
}
```



## Transfer an Asset

```JSON
"asset_transfer" : {
    "from_address": "mxWmN9fq7jz5wktqVDoJAMuHYeqtdYirhn",
    "to_address": "mt1qK4XZSgicQkSZsLDiseag9BL1J8Pmvw",
    "quantity": 1
}
```

## Get latest holders

Chainscript agent can update the asset in the document with the current list of asset holders.

```JSON
"asset_holders" : {}
```
