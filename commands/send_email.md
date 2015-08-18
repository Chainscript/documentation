# send_email command

The send_email command can be used to automatically email the document to a list of recipients.

## Executing

To execute the send_email command, we simply include the command in the envelope.  The command requires a key named 'to' which lists the email addresses of the recipients:

```JSON
{
  "send_email" : {
    "to" : "jack@acme.com, joe@acme.com"
  },
  "document": {
    "content": "Hello World!",
    "x_meta": {
      "uuid": "chainscript:document:e5c14929-d1ad-42fd-9948-44774e044f53"
    }
  },
  "x_chainscript": {
    "validation": {
      "result": "valid",
      "validated_on": "2015-08-18T17:19:23+00:00",
      "message": "Warning, executing envelope without command"
    },
    "digest": "55860718f36213a797285bb59f7623d4e53b96fa"
  }
}
```

## Results

The results of the command can be verified by the 'x_chainscript/validation/result' value:

```JSON
{
  "document": {
    "content": "Hello World!",
    "x_meta": {
      "uuid": "chainscript:document:e5c14929-d1ad-42fd-9948-44774e044f53"
    }
  },
  "x_chainscript": {
    "validation": {
      "result": "success",
      "validated_on": "2015-08-18T17:25:06+00:00"
    },
    "digest": "55860718f36213a797285bb59f7623d4e53b96fa",
    "snapshot_url": "https://chainscript.firebaseio.com/snapshots/chainscript:document:e5c14929-d1ad-42fd-9948-44774e044f53.json"
  }
}
```

## Summary

The send_email command allows you to quickly email the document.

The next command, (execute)[commands/execute.md] allows us to execute a list of commands.