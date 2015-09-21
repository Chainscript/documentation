# Chainscript

Chainscript is an easy to use scripting langauge for writing, executing and verifing smart workflows.  A workflow is defined as a process which involves one or more actors.  The actors share chainscript which defines and execute each step of the process.  After each step is executed, the chainscript is updated to reflect the current state of the process.

## Overview

Chainscript smart workflows are transaparent, auditable and immutable.  The script is designed to be freely shared and executed by "agents".  Chainscript agents execute chainscript commands and return the results.  The results from an agent should always be verifiyable by public sources such as blockchain and bittorent. 

Chainscript is designed to accomodate contracts containing assets, profiles, accounts, events and more.  The scripts can be extended to include other types of information and commands.  Specific interactions with the contract can be authorized entities holding a private key.

Chainscript is simplly written in JSON.  JSON, which stands for Javascript Object Notation, is an interchangeable format widely used on the internet today.  It is easy for both humans to read and write and easy for machines to parse and generate.  

The format is used by Chainscript to combine both commands and data into one document.  

## Envelopes

Every Chainscript file starts with an envelope, defined the curly braces '{' and '}':

```JSON
{}
```

The envelope can hold "key/value" pairs.  Keys and their values are always typed within quotes.  A colon is used to separate the key and value.

```JSON
{
    "content": "Welcome to Chainscript!"
}
```

Values can be numbers, characters and dates:

```JSON
{
    "numbers": 1,
    "characters": "Hello World",
    "date": "2015-08-12T19:41:49+02:00"
}
```

Envelopes can be used to hold other envelopes like documents, contracts, profiles, events, etc:

```JSON
{
    "content" : {
      	"title" : "Transfer of Ownership",
      	"buyer" : "ACME Inc",
      	"seller" : "John's Garage",
      	"terms" : "(to be negotiated)"
    }
}
```

... and can be nested or structured as needed:

```JSON
{
    "body" : {
        "contract" : {
            "title" : "Transfer of Ownership",
            "currency" : "BTC",
            "price" : "1.234",
            "terms" : "(to be negotiated)"
        },
        "buyer" : {
            "title" : "ACME Inc",
            "city" : "New York City"
        },
        "seller" : {
            "title" : "John's Garage",
            "city" : "New York City"
        }
    }
}
```

Chainscript files are openly extensible and can contain any valid JSON with the exception of JSON Arrays.  Arrays will be converted to envelopes.  To illustrate the conversion, assume we have the following envelope:

```JSON
{
    "document" : [
        "dogs",
        "cats",
        "fish"
    ]
}
```

When validated by a Chainscript agent, it will be converted to an envelope with a numeric index:

```JSON
{
    "document" : {
        "0" : "dogs",
        "1" : "cats",
        "2" : "fish"
    }
}
```

## Agents

Agents are public or private nodes which implement Chainscript.  Agents validate, process and return Chainscript.  

Let's use the official agent to run some examples:

[Official Chainscript Agent](http://agent.chainscript.io)

Agents accept an envelope with commands and carry out the processing.  The minimum command needed is called 'content'.  Referring to the previous example given above, the agent will execute the content command and return a new Chainscript initalized with a new content:

```JSON
{
    "content": "Welcome to Chainscript!"
}
```

Copy and paste the script above into the execution window from the agent and click "RUN".  It will return:

```JSON
{
  "body": {
    "content": "Welcome to Chainscript!",
    "x_meta": {
      "uuid": "chainscript:envelope:c8c23648-deb5-4b73-aee5-cd683595e4b0",
      "content_digest": "4d2f58ac64571f5fd1a233b5b51c64cfc51837c0"
    }
  },
  "x_chainscript": {
    "validation": {
      "agent": "io.chainscript.agent",
      "version": "0.1.alpha",
      "result": "valid",
      "validated_on": "2015-09-21T16:10:04+00:00",
      "message": "Envelope was executed without a command."
    },
    "hash": "6e7a9ceea908251a95d4e2bb6924158e1ed0ff6a"
  }
}
```

The agent returns a new file that transformed the content's arguments, "Welcome to Chainscript!", into an envelope with a content and x\_meta key.  

> It is highly advised to keep all custom or user defined data in 'body/content'.  Agents may append or include various key/pairs within the document.  Key's starting with "x_" are reserved for the agent.  Organizing your data within content will help to prevent key/value conflicts.

An additional key, named 'x_chainscript' was added to establish an audit trail for our document.  Agents use this key to keep information about the commands processed and the revisions made.


## Audit Trails

Chainscript envelopes contain meta data about content to track changes.  Two important mechanisms are used for creating an audit trail.  


### Unique Identifiers

The first is the unique identifier.  Notice how in the envelope above a 'uuid' was generated and added to the 'x_meta' key:

```JSON
   "x_meta": {
      "uuid": "chainscript:envelope:c8c23648-deb5-4b73-aee5-cd683595e4b0"
    }
```

The unique identifier, or uuid, is composed of 3 parts:

```
chainscript : envelope : c8c23648-deb5-4b73-aee5-cd683595e4b0
```

The three parts tells us that this identifer belongs to a Chainscript envelope with an ID of c8c23648-deb5-4b73-aee5-cd683595e4b0.  

The x_meta section is managed by Chainscript agents and should not be modifed by hand.  As agents execute your code, they could append or modify this section.

With a unique identifier, all parties invovled can locate and identify their document.


### Digests

The second mechanism that is used to verify an document's audit trail is the 'digest' and 'hash chain'.  

A digest is a cryptographic representation of a document.  Given the same set of data, the function will return the same digest.  However, if any piece of information has been modified, the digest will be completely different.  

Let's look at an example.  Compare the digest from the two documents below:

```JSON
{
  "body": {
    "content": "Welcome to Chainscript!",
    "x_meta": {
      "uuid": "chainscript:envelope:c8c23648-deb5-4b73-aee5-cd683595e4b0",
      "content_digest": "4d2f58ac64571f5fd1a233b5b51c64cfc51837c0"
    }
  }
}
```

```JSON
{
  "body": {
    "content": "Welcome to chainscript!",
    "x_meta": {
      "uuid": "chainscript:envelope:5056a22f-c294-4d36-8254-550ffc025071",
      "content_digest": "a4906e36d99f55bac7af184189c48d5bd4bfe958"
    }
  }
}
```

In the first envelope we have the content "Hello Chainscript!" with an uppercase 'C'.  In the second envelope we have the content "Hello chainscript!" with a lowercase 'c'.  Comparing the two 'content_digest' values we can see two radically different numbers.

### Updates

To update an envelope, we can issue a command to instruct the agent to perform the operation.  Using the first example above, we'll add the 'update' command to the top of the file:

```JSON
{
  "update": {
    "title": "Welcome to Chainscript!",
    "information": "The developer friendly way to write, execute and verify smart contracts"
  },
  "body": {
    "content": "Welcome to Chainscript!",
    "x_meta": {
      "uuid": "chainscript:envelope:e4289623-3307-4e35-bcba-9aca6b753f89",
      "content_digest": "4d2f58ac64571f5fd1a233b5b51c64cfc51837c0"
    }
  },
  "x_chainscript": {
    "validation": {
      "agent": "io.chainscript.agent",
      "version": "0.1.alpha",
      "result": "valid",
      "validated_on": "2015-09-21T16:15:32+00:00",
      "message": "Envelope was executed without a command."
    },
    "hash": "78f0db90f2e210ea6203fa0a3d11ac4aeb4b9f3f"
  }
}
```

When executed by the agent we get a new version of the envelope with an updated 'content_digest' value along wiht some new keys, 'previous_hash' and 'revision':

```JSON
{
  "body": {
    "content": {
      "title": "Welcome to Chainscript!",
      "information": "The developer friendly way to write, execute and verify smart contracts"
    },
    "x_meta": {
      "uuid": "chainscript:envelope:e4289623-3307-4e35-bcba-9aca6b753f89",
      "content_digest": "86f6deb99a444826c4f0005ac68738b86e6bfa2b",
      "revision": 1,
      "previous_hash": "78f0db90f2e210ea6203fa0a3d11ac4aeb4b9f3f"
    }
  },
  "x_chainscript": {
    "validation": {
      "agent": "io.chainscript.agent",
      "version": "0.1.alpha",
      "result": "success",
      "validated_on": "2015-09-21T16:15:57+00:00"
    },
    "hash": "515bcd982e6f9a703cfc53d5e63850094b605c2a"
  }
}
```

In the envelope under 'x\_chainscript/hash' we have a value that is a digest of 'body'.  When an agent updates anything in the 'body' key, the previous hash value is stored under 'x\_meta/previous_hash' and a new hash value is calculated for 'x\_chainscript/hash'.  This establishes a 'hash chain' which allows all clients to maintain an unbroken link of changes and revisions.

'revision' is a value that stores the latest revision number.  This is used for keeping track of a single continous hash chain.


### Breaking the Hash Chain

If we try to validate a direct modification of the content without computing a proper hash chain, the agent will return an error:

```JSON
{
  "body": {
    "content": "Welcome to Chainscript! ***BREAKING CHANGE***",
    "x_meta": {
      "uuid": "chainscript:envelope:614a203f-6d7f-4bfc-aabd-949d9bb29407",
      "content_digest": "4d2f58ac64571f5fd1a233b5b51c64cfc51837c0"
    }
  },
  "x_chainscript": {
    "validation": {
      "agent": "io.chainscript.agent",
      "version": "0.1.alpha",
      "result": "valid",
      "validated_on": "2015-09-21T16:22:21+00:00",
      "message": "Envelope was executed without a command."
    },
    "hash": "730a89cee9ada9e68170db16419155ad3911d7ef"
  }
}
```

The agent returns:

```JSON
{
  "body": {
    "content": "Welcome to Chainscript! ***BREAKING CHANGE***",
    "x_meta": {
      "uuid": "chainscript:envelope:614a203f-6d7f-4bfc-aabd-949d9bb29407",
      "content_digest": "4d2f58ac64571f5fd1a233b5b51c64cfc51837c0"
    }
  },
  "x_chainscript": {
    "validation": {
      "agent": "io.chainscript.agent",
      "version": "0.1.alpha",
      "result": "invalid",
      "validated_on": "2015-09-21T16:23:11+00:00",
      "message": "Invalid body hash, document has been modified."
    },
    "hash": "730a89cee9ada9e68170db16419155ad3911d7ef"
  }
}
```

## History and Revisions

Command and revision auditing are both available by enabling them in the x_chainscript key as properties:

```JSON
{
	"content": "Hello Chainscript!",
	"update": "Goodbye!",
	"x_chainscript" : {
		"snapshots_enabled" : true,
		"command_auditing" : true,
		"revision_auditing" : true
	}
}
```

When executed, we get a new envelope with some additional goodies:

```JSON
{
  "body": {
    "content": "Goodbye!",
    "x_meta": {
      "uuid": "chainscript:envelope:bba46b00-b23c-4311-af61-a10b44bdda13",
      "content_digest": "9847610a81d4da0b947c8f32616976303ee02f5c",
      "revision": 1,
      "previous_hash": "365fbafc497900be746f0929401c20226e218dc5"
    },
    "x_revisions": {
      "1442852700654": {
        "previous_content": "Hello Chainscript!",
        "revision": 1,
        "previous_hash": "365fbafc497900be746f0929401c20226e218dc5",
        "revised_on": "2015-09-21T16:25:00+00:00"
      }
    }
  },
  "x_chainscript": {
    "snapshots_enabled": true,
    "command_auditing": true,
    "revision_auditing": true,
    "validation": {
      "agent": "io.chainscript.agent",
      "version": "0.1.alpha",
      "result": "success",
      "validated_on": "2015-09-21T16:25:00+00:00"
    },
    "hash": "07002449144007177c8ef2b4c3212a47bd4adb13",
    "command_history": {
      "1442852700654": {
        "command": "update",
        "arguments": "Goodbye!",
        "executed_on": "2015-09-21T16:25:00+00:00",
        "hash": "07002449144007177c8ef2b4c3212a47bd4adb13"
      }
    },
    "snapshot_url": "https://chainscript.firebaseio.com/snapshots/chainscript-envelope-bba46b00-b23c-4311-af61-a10b44bdda13.json"
  }
}
```

First notice the 'body/x\_revision' key which contains information about the revision issued from the 'update' command.  There's also an 'x\_chainscript/command_history' key created to log each command executed.

### Snapshots
In the previous example, notice how we get 'x\_chainscript/snapshot_url'. 'snapshot\_url' is a link to an URL which holds a copy of the envelope.  This is useful for keeping all parties in sync.

## Commands

* [Add Events (add_event)](commands/add_event.md)
* [Broadcast a Transaction (broadcast)](commands/broadcast.md)
* [Build a Transaction (build_transaction)](commands/build_transaction.md)
* [Execute List of Commands (execute)](commands/execute.md)
* [Make Account (make_account)](commands/make_account.md)
* [Notarize (notarize)](commands/notarize.md)
* [Send Email (send_email)](commands/send_email.md)
* [Snapshot (snapshot)](commands/snapshot.md)



## Summary

Chainscript files contain one envelope which must contain one body.  Inside the envelope we can have key/value pairs to represent different kinds of documents, contracts or publish various types of structured information.

Agents execute Chainscript files and process commands.  The basic command, 'content: {}', initalizes a new document for us with a unique identifier and digest for establishing an audit trail.  Parties can agree to use the official agent or implement their own.

After an envelope has been been initalized, we can modify it with the 'update: {}' command.  When validated and processed by an agent the new change will be merged, including the previous digest for the hash chain, and a new content digest will be computed that represents 'body/content'.

By using Chainscript, multiple parties can interact with a document or contract with a fully transparent audit trail.







