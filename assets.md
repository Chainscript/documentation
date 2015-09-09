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
  "document": "My First Digital Asset",
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
