# Execute Commands (execute)

The execute command accepts a list of command to be executed in order.

## Execute

```JSON
{
	"document": "Empty contract",
	"execute": {
		"0": {
			"snapshot": {}
		},
		"1": {
			"update": { 
				"title": "Sample Contract",
				"conditions": "pay upon delivery"
			}
		},
		"2": {
			"send_email": {
				"to": "test@localhost.com"
			}
		}
	}
}
```

## Results

The execute command is recorded in x_chainscript/command_log.  A list of the commands execute are listed as the arguments.

```JSON
{
  "document": {
    "content": {
      "title": "Sample Contract",
      "conditions": "pay upon delivery"
    },
    "x_meta": {
      "uuid": "chainscript:document:f00e406e-cd37-4e9d-823b-5bd1cca75bbc",
      "revision": 1,
      "previous_digest": "84b11ff66373c7f7d0e0eef29a70a77b251106c6"
    }
  },
  "x_chainscript": {
    "command_log": {
      "0": {
        "command": "execute",
        "arguments": "snapshot,update,send_email",
        "executed_on": "2015-08-20T16:46:09+00:00",
        "document_digest": "2a5ff629477d3a6868795a064c3efbf77da56607"
      }
    }
  }
}
```
