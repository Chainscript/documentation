# Add Event (add_event)

The add_event command sets an event on specific conditions, that when met execute a list of commands.  Example events can include expiration based on date/time, the balance of a bitcoin address or the deliver of a packaged by a shipping carrier.  

Events are executed by abstract event machines.  Event machines assumes a trusted source of information.  Event machines are transparent and publish their results to public sources: blockchain, bittorrent, etc.

## Agents
Trusted agents, who represent the event machines, are responsible for implementing events.  The parties involved with the document can choose which agents to trust.  Or, collectives can choose to implement their own agents.

Agents can choose to sign and publish evidence of the event by [notarizing](notarize.md) it on a blockchain.  

Agents function in a  role similar to [oracles](https://github.com/orisi/wiki/wiki/Mastering-Distributed-Oracles).

## Execute

At the minimum, the add_event command requires a type, a set of conditions and a list of commands:

```JSON
	{
		"document" : "A contract.",
		"add_event" : {
			"type" : "chainscript/check-datetime",
			"conditions" : { 
				"expires_in_minutes_from_now" : -15
			},
			"execute" : {
				"0": {"update" : "An EXPIRED contract."},
				"1": {"snapshot" : {}},
				"2": {"send_email" : {"to" : "caetano@gmail.com"}}
			}
		},
		"x_chainscript" : {
			"disable_command_log" : true,
			"disable_revision_history" : true
		}
	}
```

Once executed by an agent, the envelope is updated with some additional fields:

```JSON
{
  "document": {
    "content": "A contract.",
    "x_meta": {
      "uuid": "chainscript:document:0c953cc9-0548-4a1d-a1ae-166129247ceb",
      "revision": 1,
      "previous_digest": "1c8de1d4d466e1e5713d39c0fe96ff9e979a5257"
    },
    "x_attachments": {
      "events": {
        "chainscript:event:0b470db9-e967-45fa-a4d6-0d79e183cbbe": {
          "type": "chainscript/check-datetime",
          "conditions": {
            "greater_than": "2015-08-20T15:21:41+00:00"
          },
          "execute": {
            "0": {  "update": "An EXPIRED contract." },
            "1": {  "snapshot": {} },
            "2": {
              "send_email": {
                "to": "caetano@gmail.com"
              }
            }
          },
          "digest": "1c8de1d4d466e1e5713d39c0fe96ff9e979a5257",
          "created_on": "2015-08-20T15:36:41+00:00",
          "status": "pending",
          "snapshot_url": "https://chainscript.firebaseio.com/events/chainscript-event-0b470db9-e967-45fa-a4d6-0d79e183cbbe.json"
        }
      }
    }
  },
  "x_chainscript": {
    "disable_command_log": true,
    "disable_revision_history": true,
    "validation": {
      "agent": "agent.chainscript.io",
      "version": "alpha.0.1",
      "result": "success",
      "validated_on": "2015-08-20T15:36:41+00:00"
    },
    "digest": "54f2b1700f112b601545af1c1203ae6fdfd6892b",
    "snapshot_url": "https://chainscript.firebaseio.com/snapshots/chainscript-document-0c953cc9-0548-4a1d-a1ae-166129247ceb.json"
  }
}
```

### Event Types

The official Chainscript agent implements the following event types:

#### chainscript/check-datetime

Greater than/less than conditions against date/time:

```JSON
"add_event" : {
	"type" : "chainscript/check-datetime",
	"conditions" : { 
		"greater_than" : "2015-08-20T17:13:42+02:00",
		"less_than" : "2015-08-20T17:13:42+02:00",
	},
	"execute" : {}
}
```

Or an expiration date/time in mins, hours or days:

```JSON
"conditions" : { 
	"expires_in_minutes_from_now" : 120,
	"expires_in_hours_from_now" : 12,
	"expires_in_days_from_now" : 30,
}
```

### Events List

The events are added to the document/x_attachments/events key.  Notice in the results from above that the digest is recorded and the status is set to pending.  Agents should respect the digest and insure that the commands executed are executed against a matching digest.

### Snapshots

A snapshot of the original event is kept as a reference point.  The URL of the snapshot is provided by the agent.

A document snapshot command can be included in the list of commands to be executed:

```JSON
    "execute": {
        "0": {  "update": "An EXPIRED contract." },
        "1": {  "snapshot": {} }
    }
```

### Event Processing

The agent maintains a backend process to continuously check for the conditions of each event.  If the conditions are met, the commands are executed.

Upon successful execution, the status of the event is updated to "success".  The results are posted to the event's snapshot.




