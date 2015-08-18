# Chainscript

Chainscript is an easy to use scripting langauge for writing, executing and verifing smart contracts.

## Overview

Chainscript smart contracts are transaparent, auditable and immutable.  The script is designed to be freely shared and executed by "agents".  Chainscript agents execute chainscript commands and return the results.  The results from an agent should always be verifiyable by public sources such as blockchain and bittorent. 

Chainscript is designed to accomodate contracts containing assets, profiles, accounts, events and more.  The scripts can be extended to include other types of information and commands.  Specific interactions with the contract can be authorized entities holding a private key.

Chainscript is a simple language written in JSON.  JSON, which stands for Javascript Object Notation, is an interchangeable format widely used on the internet today.  It is easy for both humans to read and write and easy for machines to parse and generate.  

The format is used by Chainscript to combine both commands and data into one document.  

## Envelopes

Every Chainscript file starts with an envelope, defined the curly braces '{' and '}':

```JSON
{}
```

The envelope can hold "key/value" pairs.  Keys and their values are always typed within quotes.  A colon is used to separate the key and value.

```JSON
{
    "document": "Welcome to Chainscript!"
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
    "document" : {
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
    "document" : {
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

[Official Chainscript Agent](http://chainscript.io/execute.html)

Agents accept an envelope with commands and carry out the processing.  The minimum command needed is called 'document'.  Referring to the previous example given above, the agent will execute the document command and return a new Chainscript initalized with a new document:

```JSON
{
    "document": "Welcome to Chainscript!"
}
```

Copy and paste the script above into the execution window from the agent and click "RUN".  It will return:

```JSON
{
  "document": {
    "content": "Welcome to Chainscript!",
    "x_meta": {
      "uuid": "chainscript:document:0c7c3b96-61d4-4a86-82f1-0d7e5fc06783"
    }
  },
  "x_chainscript": {
    "validation": {
      "result": "valid",
      "validated_on": "2015-08-18T16:22:29+00:00",
      "message": "Warning, executing envelope without command"
    },
    "digest": "a8d5256a1a721c100e3db0b5065652535f7e6699"
  }
}
```

The agent returns a new file that transformed the document's arguments, "Welcome to Chainscript!", into a document with a content and x\_meta key.  

> It is highly advised to keep all custom or user defined data in 'document/content'.  Agents may append or include various key/pairs within the document.  Organizing your data within content will help to prevent key/value conflicts.

An additional key, named 'x_chainscript' was added to establish an audit trail for our document.  Agents use this key to keep information about the commands processed and the revisions made.


## Audit Trails

Chainscript envelopes contain meta data on the document to track changes.  Two important mechanisms are used for creating an audit trail.  


### Unique Identifiers

The first is the unique identifier.  Notice how in the document above a 'uuid' was generated and added to the 'x_meta' key:

```JSON
   "x_meta": {
      "uuid": "chainscript:document:0c7c3b96-61d4-4a86-82f1-0d7e5fc06783"
    }
```

The unique identifier, or uuid, is composed of 3 parts:

```
chainscript : document : 0c7c3b96-61d4-4a86-82f1-0d7e5fc06783
```

The three parts tells us that this identifer belongs to a Chainscript document with an ID of 0c7c3b96-61d4-4a86-82f1-0d7e5fc06783.  

The x_meta section is managed by Chainscript agents and should not be modifed by hand.  As agents execute your code, they could append or modify this section.

With a unique identifier, all parties invovled can locate and identift their document.


### Digests

The second mechanism that is used to verify an document's audit trail is the 'digest'.  A digest is a cryptographic representation of a document.  Given the same set of data, the function will return the same digest.  However, if any piece of information has been modified, the digest will be completely different.  

Let's look at an example.  Compare the digest from the two documents below:

```JSON
{
  "document": {
    "content": "Welcome to Chainscript!",
    "x_meta": {
      "uuid": "chainscript:document:0c7c3b96-61d4-4a86-82f1-0d7e5fc06783"
    }
  },
  "x_chainscript": {
    "digest": "a8d5256a1a721c100e3db0b5065652535f7e6699"
  }
}
```

```JSON
{
  "document": {
    "content": "Hello chainscript!",
    "x_meta": {
      "uuid": "chainscript:document:3153f271-6701-4c8d-adb2-315a3f6523eb"
    }
  },
  "x_chainscript": {
    "digest": "c65943f11b70f582a506caf8251701a800d76975"
  }
}
```

In the first document we have the content "Hello Chainscript!" with an uppercase 'C'.  In the second document we have the content "Hello chainscript!" with a lowercase 'c'.  Comparing the two digests we can see two radically different numbers.

To update a document, we can issue a command to an agent to perform the operation.  Using the first example above, we'll add the 'update' command to the top of the file:

```JSON
{
  "update": {
    "title": "Welcome to Chainscript!",
    "information": "The developer friendly way to write, execute and verify smart contracts"
  },
  "document": {
    "content": "Welcome to Chainscript!",
    "x_meta": {
      "uuid": "chainscript:document:0c7c3b96-61d4-4a86-82f1-0d7e5fc06783"
    }
  },
  "x_chainscript": {
    "digest": "a8d5256a1a721c100e3db0b5065652535f7e6699"
  }
}
```

When executed by the agent we get some new information added to our document:

```JSON
{
  "document": {
    "content": {
      "title": "Welcome to Chainscript!",
      "information": "The developer friendly way to write, execute and verify smart contracts"
    },
    "x_meta": {
      "uuid": "chainscript:document:0c7c3b96-61d4-4a86-82f1-0d7e5fc06783",
      "previous_digest": "a8d5256a1a721c100e3db0b5065652535f7e6699"
    }
  },
  "x_chainscript": {
    "validation": {
      "result": "success",
      "validated_on": "2015-08-18T16:41:59+00:00"
    },
    "digest": "4170db2fcdad8c4eb59f5969b9af63f6010828d6"
  }
}
```

Notice the last digest was stored as /document/x\_meta/previous_digest.  As updates are posted to the document, each change results in a new digest which includes the previous digest.  This establishes a link between revisions.  This reinforces the audit trail.

Referring to the previous document above, if we try to validate a modifcation through the agent without the update command we get the following:

```JSON
{
  "document": {
    "content": "Welcome to Chainscript!  This will get reject!",
    "x_meta": {
      "uuid": "chainscript:document:0c7c3b96-61d4-4a86-82f1-0d7e5fc06783"
    }
  },
  "x_chainscript": {
    "digest": "a8d5256a1a721c100e3db0b5065652535f7e6699"
  }
}
```

The agent returns:

```JSON
{
  "document": {
    "content": "Welcome to Chainscript!  This will get reject!",
    "x_meta": {
      "uuid": "chainscript:document:0c7c3b96-61d4-4a86-82f1-0d7e5fc06783"
    }
  },
  "x_chainscript": {
    "digest": "a8d5256a1a721c100e3db0b5065652535f7e6699",
    "validation": {
      "result": "invalid",
      "message": "Document has been modified since last digest.",
      "validated_on": "2015-08-18T16:49:29+00:00"
    }
  }
}
```

## History and Revisions

(TODO: write about revision history and command logs)

## Summary

Chainscript files contain one envelope which must contain one document.  Inside the document we can key/value pairs to represent different kinds of document, contracts or publish various types of structured information.

Agents execute Chainscript files and process commands.  The root command, 'document: {}', initalizes a new document for us with a unique identifier and digest for establishing an audit trail.  Parties can agree to use the official agent or implement their own.

After a document has been been initalized, we can modify it with the 'update: {}' command.  When validated and processed by an agent the new change will be merged, including the previous digest, and a new digest will be computed.

By using Chainscript, multiple parties can interact with a document or contract with fully transparent audit trail.

In the next section, we will introduce Chainscript's (Snapshot Command)[commands/snapshot.md].







