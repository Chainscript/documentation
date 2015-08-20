# Add Event (add_event)

The add_event command attaches specific conditions that when met execute a list of commands.  Example events can include expiration based on date/time, the balance of a bitcoin address or the deliver of a packaged by a shipping carrier.  

Trusted agents, which implement the event machines, are responsible for implementing events.  In this case, agents are similar to the idea of an [oracle](https://github.com/orisi/wiki/wiki/Orisi-White-Paper).

# Execute

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
            "0": {
              "update": "An EXPIRED contract."
            },
            "1": {
              "snapshot": {}
            },
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

### Events list

The events are added to the document/x_attachments/events key.  Notice in the results from above that the digest is recorded and the status is set to pending.  Agents should respect the digest and insure that the commands executed are executed against a matching digest.





