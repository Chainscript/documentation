                        
# Chainscript Introduction

Chainscript is an easy to use scripting langauge for writing, executing and verifing smart contracts.  It is written with JSON to make it easy for both humans and machines to process.

Chainscript smart contracts are transaparent, auditable and immutable.  The script is designed to be freely shared and executed by "agents".  Chainscript agents execute chainscript commands and return the results.  The results from an agent should always be verifiyable by public sources such as blockchain and bittorent. 

Chainscript is designed to accomodate contracts containing assets, profiles, accounts, events and more.  The scripts can be extended to include other types of information and commands.  Specific interactions with the contract can be authorized entities holding a private key.

For this introduction, we will become familiar with the basics of Chainscript.

## Envelopes

In Chainscript, we always start with the "envelope"
envelope = {}

// The envelope below is holding a "key/value" pair.  Keys are always listed 
// within quotes.  A colon is used to separate the key and value.

````
envelope = {
    "message": "hello world"
}
````

// Values can be numbers, characters and dates

envelope = {
    "number": 1,
    "characters": "Hello World",
    "date": "2015-08-12T19:41:49+02:00"
}

// The envelope is used to hold documents, like contracts, profiles, events

envelope = {
    "contract": {
        "currency": "BTC",
        "min_value": 100000,
        "terms": "Exchange BTC for USD"
    }
}

// Envelopes are only big enough to hold one document.  Listed below are all
// the available documents:

"document": {} // freeform document, add what ever you like
"address": {} // metadata for a single address
"contract": {} // holds a contracts terms and history
"profile": {} // represents a person, company or robot
"asset": {} // represents a digital asset
"event": {} // represents a foreseeable event

// Documents can have requried fields.  Any document can be 
// extended with by adding custom key/value paris

// Envelopes can contain a document and one or more articles.  Articles
// have a operational lifespan.  Their contents can change depending on the 
// commands executed.  Once they processed, they usually will be
// incorportated into the document and removed from the envelope.

"transfer" = {} // sends a payment from an address or contract
"signatures" = {} // holds one or more digital signatures of a document
"notarizations" = {} // holds records of notarizations

// Typically these articles will be recorded with the document type ...

envelope = {
    "document": {},
    "document_signatures": [],
    "document_notarizations": []
}

// There could only be one article of each type at a time

// In addtion to one document, you can include a command in an envelop.  
// Commands requests Chainscript to do something to a document or article.

envelope = {
    "contract": {
        "terms": "..."
    },
    "send_to_email": {
        "title": "Please review our contract",
        "to": "joe@gmail.com, jack@gmail.com",
        "from": "richard@coinhive.io"
    }
}

envelope = {
    "transfer": {
        "encoded_tx": "010000..."
    },
    "broadcast": {}
}

// other commands ...

"execute_transaction" = {} // executes multiple commands
"schedule_transfer" = {} // schedule a transfer for a later signature   
"broadcast" = {} // broadcasts a signed transfer to the network
"notarize" = {} // notarize a document's digest on the blockchain
"send_to_email" = {} // sends the envelope to an email address
"send_to_torrent" = {} // seed a bitTorrent with this document
"send_to_firebase" = {} // make available for realtime updates

// by specifiying only one command in a parent command, you can chain commands

envelope = {
    "transfer": {
        "encoded_tx": "010000..."
    },
    "transfer_notarizations": [],

    // 1 - send the broadcast
    "broadcast": {
        // 2 - when completed notarize the document
        "notarize": {
            "content_key": "transfer",
            // 3 - when completed, send this document to the email address
            "send_to_email": {
                "title": "Transfer broadcasted!",
                "to": "jack@gmail.com",
                "from": "richard@coinhive.io"
            }
        }
    }
}

// ... and maybe implement transactions?? ...

envelope = {
    "contract": {
        "terms": "..."
    },
    "execute_transaction": [{
        "broadcast": {}
    }, {
        "notarize": {}
    }, {
        "send_to_email": {}
    }]
}

// meta data can be used to instruct the processor how to handle the details
// in the example we list the available settings ...

envelope = {
    "contract": {
        "commands": [],
        "payments": [],
        "meta": {
            "previous_hash": "1bcbe8f72f3f3dbcd63fdbab37bcdbabb",
            "version": 2,
            "revised_on": "2015-08-12T19:41:49+02:00"
        }
    },
    "contract_revisions": [{
            "hash": "1bcbe8f72f3f3dbcd63fdbab37bcdbabb",
            "contract": {
                "meta": {
                    "version": 1,
                    "revised_on": "2015-08-12T19:41:49+02:00"
                }
            }
        }, {
            "hash": "1b1bccbef382829929299292929293883",
            "contract": {
                "meta": {
                    "previous_hash": "1bcbe8f72f3f3dbcd63fdbab37bcdbabb",
                    "version": 2,
                    "revised_on": "2015-08-12T19:41:49+02:00"
                }
            }
        },

    ],
    "meta": {
        "contract_hash": "1b1bccbef382829929299292929293883",
        "command_log": true,
        "transfer_log": true,
        "revision_log": true,
        "torrent_tracker": "http://",
        "firebase_endpoint": "http://"
    }
}

// by enabling "meta/command_log" the API will log each command processed
// by enabling "meta/transfer_log" each transfer payment will be logged
// by enabling "meta/revision_log" all changes to the document will be logged

// Finally, after processing a document and command, the API will return
// a validation stamp, indicating the results and status

// invalid document
envelope = {
    "contract": {},
    "validation": {
        "message": "Contract cannot be validated",
        "result": "invalid",
        "missing_keys": ["min_amount", "terms"]
    }
}

// command that succeeded
envelope = {
    "contract": {},
    "validation": {
        "executed": ["send_to_email"],
        "executed_on": "2015-08-12T19:41:49+02:00",
        "available_commands": ["send_to_email", "send_to_torrent"],
        "result": "valid",
        "message": "Email sent succssfully"
    }
}

// command that failed
envelope = {
    "contract": {},
    "send_to_email": {},
    "validation": {
        // first command was executed
        "executed": ["broadcast"],
        "executed_on": "2015-08-12T19:41:49+02:00",
        // second command aborted
        "result": "aborted",
        "message": "An email address is required.",
        "aborted": "send_to_email",
        "aborted_on": "2015-08-12T19:41:49+02:00",
    }
}

// Chainscript is designed to be executed on a decentralized system.
// Multiple entites can request operations on a valid document. 

// Everything needed to execute and validate a smart contract is contained
// in the JSON.  When processed, the JSON's state is evaluated before
// executing any commands.  Once processed, the command's state is reflected.

//   POST http://chainscript.io/execute 


// Entites can agree to have a valid copy if the hashes and, if included,
// digital signatures are valid.  Revisions are always include the hash
// of their previous revisions to main an unbreakable link back to the 
// original document.  Envelopes can always be validated:

//   POST http://chainscript.io/validate


// ## DOCUMENT NOTARY
// "document" is a generic document type that allows parties to define 
// a custom structure.  Writing documents in JSON results in 
// clear terms and definitions which are both readable by humans and robots

envelope = {
    "document": {
        "title": "Contractual Agreement between two Parties",
        "buyer": "ACME Produce Company",
        "seller": "Joe Farmer",
        "terms": "SELLER agrees to sell produce to BUYER at the price of 10 per unit for 6 months.  Payments are due every 2 months",
        "start_date": "2015-08-12T00:00:00+00:00",
        "payment_schedule": [{
            "payment": 1,
            "due_date": "2015-10-01T00:00:00+00:00",
        }, {
            "payment": 2,
            "due_date": "2015-12-01T00:00:00+00:00",
        }, {
            "payment": 3,
            "due_date": "2016-02-01T00:00:00+00:00",
        }]
    }
}

// Using the client side Javascript library, parties can sign the document

//  
//      chainscriptlib.signDocument(envelope, "document", private_key);
//  

envelope = {
    "document": {},
    "document_signatures": [{
        "address": "1EHEhk8XGNFQTmzTt2FX8WBXGXt1sgJkj",
        "public_key": "0367db750d87753ce449085c63b6d140f...",
        "hash_to_sign": "3f977f6433e0d8ffe8455042a5b040f4b7553115",
        "signature": "3045022100d4dceec60659bfd28938abf15e50a4f964..."
    }, {
        "address": "1EmG3tNd9qswqHcnL9r7Q1gvJNwdumC2tS",
        "public_key": "034bf430938ad0671933181ac36de73051...",
        "hash_to_sign": "3f977f6433e0d8ffe8455042a5b040f4b7553115",
        "signature": "3045022100e08da59b9cbeff1da5d8f68922ea3746f8..."
    }]
}

// These signatures can be used to prove that the two parties signed the 
// document

// The notarization of document produces a timestamp on the blockchain
// with the document's hash.  The document can then be validated against
// the timestamp to insure the original is in hand

envelope = {
    "document": {},
    "document_signatures": [],
    "notarize": {
        "content_keys": ["document", "document_signatures"]
    }
}

// After executed at chainscript.io/execute the API returns ...

envelope = {
    "document": {},
    "document_signatures": [],
    "document_notarizations": [{
        "content_keys": ["document", "document_signatures"],
        "hash_hex": "336763792d4d4e7f86398f19b417da86363c1aa9",
        "notarized_at": "2015-08-12T19:41:49+02:00",
        "timestamp": "chainscript:bitcoin:tx:b1b16c78153fd211cab00c0...",
        "resource": "https://chainscript.firebaseio.com/notarized/33676379.."
    }],
    "validation": {
        "executed": "notarize",
        "executed_on": "2015-08-12T19:41:49+02:00",
        "result": "success",
        "message": "Document has been notarized"
    }
}

// ## SIMPLE ESCROW
// An escrow is a contract between 3 parties, sender, receiver and moderator

"contract" = {
    "contract_type": "chainscript/escrow-simple-beta",
}

// "contract_type" informs the system of the proper validation logic to use.
// In this case it's 'escrow-simple-beta'.  The 'beta' term notes that
// the logic for this contract can change.

// Escrow contracts requires three participants, noted by their public keys

"contract" = {
    "contract_type": "chainscript/escrow-simple-beta",
    "sender_public_key": "035debdf1f48e5694f7b77857e742a077e1d36e6a206bf6a4ec8487e46fa0ddf14",
    "receiver_public_key": "035b9439f858618979da5f890a522e457f7478d23da69f8aefb37a0dfe0a9182ff",
    "moderator_public_key": "03e8f837eb74f07ab01b1c4e4fbc3ae35df5907f4f6c4d90851c345ccc6668d884"
}


// TODO : finish escrow contracts

