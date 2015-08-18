# snapshot Command

The snapshot command is used to publish a public copy of your document.  As multiple parties may be involved, a snapshot of the document can be made to keep everyone in sync.  The offical agent publishes snapshots to a (Firebase)[http://firebase.com] node so that all parties can have realtime updates of snapshots for a given document.

## Executing

To execute the snapshot command, we simply include the command in the envelope:

```JSON
{
  "snapshot" : {},
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

The results of the command include a 'snapshot_url' which gives us the public URL of the document:

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
      "validated_on": "2015-08-18T17:20:29+00:00"
    },
    "digest": "55860718f36213a797285bb59f7623d4e53b96fa",
    "snapshot_url": "https://chainscript.firebaseio.com/snapshots/chainscript:document:e5c14929-d1ad-42fd-9948-44774e044f53.json",
  }
}
```

## Summary

A snapshot of your document provides all parties involved with a public link to the document.  The link is stored on a Firebase node which gives all parties realtime access to the document.

The next command, [send_email](send_email.md) allows us to email the document to one or more recipients.