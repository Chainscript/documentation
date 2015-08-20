# Make Account (make_account)

To register for an account with the agent, run the make_account command.  Agent can use the account ID issued for usage tracking and billing.

## Execute

Make_account can accept at the minimum a string: 

```JSON
{
  "make_account": "ACME Corporation"
}
```

A customized envelope can also be included:

```JSON
{
  "make_account": {
    "title": "ACME Corporation",
    "location": "New York City"
  }
}
```

## Results

The account is returned under the x_chainscript key:

```JSON
{
  "document": {
    "content": "",
    "x_meta": {
      "uuid": "chainscript:document:ac9b1eb2-38ff-4e0a-8885-e8cd00eff85f"
    }
  },
  "x_chainscript": {
    "validation": {
      "agent": "agent.chainscript.io",
      "version": "alpha.0.1",
      "result": "success",
      "validated_on": "2015-08-20T16:52:07+00:00",
      "message": "For your new account, we would like to give you 10 stamps."
    },
    "digest": "e0a29ba0619a858ed498cc1c607d0d7fafea8bb1",
    "account": {
      "title": "ACME Corporation",
      "uuid": "chainscript:account:7752615e-a149-43c4-8ef1-f3e4c61cdd5b",
      "stamps": 10,
      "created_on": "2015-08-20T16:52:07+00:00"
    }
  }
}
```

## Working with Accounts

The participant is responsible for managing the data from their account envelope.  The agent may keep accounting related data.  Validating an envelope with an account specified in x_chainscript will include the current accounting data (stamps, balance, operations, etc)
